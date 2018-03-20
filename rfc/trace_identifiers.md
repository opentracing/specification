# Trace Identifiers

**Current State:** Draft 
**Author:** [tedsuo](https://github.com/tedsuo)

The OpenTracing SpanContext interface is extended to include `SpanID` and `TraceID` accessors. 

The OpenTracing model of computation specifies two primary object types, `Spans` and `Traces`, but does not specify identifiers for these objects. Identifiers for the two primary object types make it easier to correlate tracing data with data in other systems, simplify important tasks, and allow the creation of reusable trace observers. Some use cases are detailed below.

# Background: Existing Protocols
Before discussing changes to the OpenTracing specification, it’s worth reviewing several popular wire protocols which contain these trace identifiers.

## Trace-Context HTTP Headers
[Trace-Context HTTP headers](https://github.com/w3c/distributed-tracing) are in the process of being standardized via the w3c. The tracing community has voiced strong support in implementing these headers for use in tracing interop.

The `Trace-Parent` header contains the following fields: `version`, `trace-id`, `span-id`, and `trace-options`.

| field | format | description |
| :---  | :---   | :---    |
| `trace-id` | 16-byte array | The ID of the whole trace forest. If all bytes are 0, the `Trace-Parent` may be ignored. |
| `span-id`  | 8-byte array  | The ID of the caller span (parent). If all bytes are 0, the `Trace-Parent` may be ignored. |

## B3 HTTP Headers
The [B3 HTTP headers](https://github.com/openzipkin/b3-propagation) are widely adopted, mostly by Zipkin-like tracing systems. The B3 protocol includes `X-B3-TraceId` and `X-B3-SpanId` as required headers, which contain the `TraceId` and `SpanId` values, respectively.

| field | format | description |
| :---  | :---   | :---        |
| `TraceId` | 64 or 128-bit | The ID of the trace. Every span in a trace shares this ID. |
| `SpanId`  | 64-bit | Indicates the position of the current operation in the trace tree. The value may or may not be derived from the value of the `traceId`. |

# Specification Changes
The `SpanContext` section of the specification is extended to include the following properties:

| method | format | description |
| :---  | :---   | :---        |
| `TraceID` | string | Globally unique. Every span in a trace shares this ID. |
| `SpanID` | string | Unique within a trace. Each span within a trace contains a different ID. |

**String** values are used for identifiers. In this context, a string is defined as an immutable, variable length sequence of unicode characters. The empty string is a valid return type.

A string is preferred over other formats for the following reasons:

* Forwards compatibility with future versions of Trace-Context and other standards.
* Backwards compatibility with pre-existing ID formats.
* Strongly supported across many languages, and commonly used for transferring data between independent subsystems.

## Alternate Formats
In some cases, additional formats may be appropriate, if a language supports multiple common transport formats. Rather than manually converting the string value to another format, an additional accessors could be added to allow the tracer to do the conversion.

If tracing systems converge on common in-memory formats for Trace-Context identifiers, accessors may be added for those formats as well.

## Backwards Compatibility and Optional Support
The OpenTracing specification does not currently require trace and span identifiers. To continue support for existing tracers, the empty string value can be returned when no ID has been set.

# Use Cases

## Log Correlation
The primary expected consumer for Trace-Context identifiers are logging systems which run independently from the tracing system. 

Log indexing has become a common practice, often by including a request identifier in the log. In the past, this has involved manually propagating these identifiers as headers. However, systems which using OpenTracing automatically propagate these identifiers via the Inject/Extract interface. Some of these identifiers are user-generated, and contained in Baggage. However, the most relevant identifiers for log indexing are the Trace and Span IDs. Therefore, exposing these values would be immensley valuable.

## Trace Observers
The OpenTracing community would like to develop secondary observation systems which utilize the tracing runtime, but are tracer independent. Trace and span identifiers would allow these observers to correlate tracing data without having knowledge of the wire protocol or tracing implemnetation. Examples include:

* Generating metrics from tracing data
* Integrating with runtime and system diagnostics 
* Integrating with 3rd-party context-propagation systems
* Correlating logs, as mentioned above

# Risk Assessment
Because this proposal includes the exposure of new information, and adds entirely new concepts to the interface, some risks exist.

## Tracer support
Some existing tracers may not be able to support this feature, as their internal model does not include any client-side trace identifiers. These tracers may choose to not support this feature by returning empty string values.

## Protocol support
It's possible that new tracing protocols may emerge which use an entirely different header scheme. Examples could include a tracing system which handles trace joins explicitly as part of the protocol, and thus no longer has an equivalent concept of a trace id, or a system which uses backpropagation to contain additional data.

This is mitigated by the fact that the concept of a span and a trace are directly part of the OpenTracing model of computation. Some form of identifier for these objects will be availiable to a tracer that conforms to this model of computation. While its likely that additional identifiers or interfaces may be necessary to handle future changes, it is impossible that the trace and span concepts will be removed from this version of OpenTracing. 

Because the accessors produce a variable-width string value, new formats and wireprotocols for these identifiers will not result in a breaking change for the OpenTracing interface. Likewise, systems which consume this data are by definition separate from the tracing system, and are not dependent on the format of the identifier.

## Extra Allocations and Overhead
Internally, tracers do not always use strings to represent their identifiers. So there is a conversion cost when using these accessors. 

While a single allocation may be inevitable, exposing accessors in additional formats could be done to prevent double allocations while formatting the identifiers. For example, converting from a tracer’s native format to a string may trigger an allocation. If there are many systems which want to consume the identifier in a format which requires an allocation when converting from a string, a second allocation could occur.