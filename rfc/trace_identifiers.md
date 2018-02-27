# Trace Identifiers

**Current State:** Draft 
**Author:** [tedsuo](https://github.com/tedsuo)

The Opentracing model of computation specifies two primary object types, `Spans` and `Traces`, but does not specify identifiers for these objects. The lack of identifiers makes it very difficult to correlate tracing data with data in other systems. This complicates a number of important tasks, and prevents the creation of reusable trace observers.

To address these difficulties, the OpenTracing `SpanContext` interface is extended to include `SpanID` and `TraceID` accessors.

# Background: Existing Protocols
Before discussing changes to the OpenTracing specification, it’s worth reviewing several popular wire protocols which contain these trace identifiers.

## Trace-Context HTTP Headers
[Trace-Context HTTP headers](https://github.com/w3c/distributed-tracing) are in the process of being standardized via the w3c. The tracing community has voiced strong support in implementing these headers for use in tracing interop.

The `Trace-Parent` header contains the following fields: `version`, `trace-id`, `span-id`, and `trace-options`.

`Trace-id` is the ID of the whole trace forest. It is represented as a 16-bytes array, e.g.,4bf92f3577b34da6a3ce929d0e0e4736. All bytes 0 is considered invalid. Implementation may decide to completely ignore the Trace-Parent if the trace-id is invalid.

`Span-id` Is the ID of the caller span (parent). It is represented as a 8-bytes array, e.g., 00f067aa0ba902b7. All bytes 0 is considered invalid. Implementation may decide to completely ignore the Trace-Parent if the span-id is invalid.

## B3 HTTP Headers
The [B3 HTTP headers](https://github.com/openzipkin/b3-propagation) are widely adopted, mostly by Zipkin-like tracing systems. The B3 protocol includes `X-B3-TraceId` and `X-B3-SpanId` as required headers.

`TraceId` is 64 or 128-bit in length and indicates the overall ID of the trace. Every span in a trace shares this ID.

`SpanId` is 64-bit in length and indicates the position of the current operation in the trace tree. The value should not be interpreted: it may or may not be derived from the value of the TraceId.

# Specification Changes
The `SpanContext` section of the specification is extended to include the following properties:

* `TraceID`, accessible as a **string**.
* `SpanID`, accessible as a **string**.

**String** values are used for identifiers. In this context, a string is defined as an immutable, variable length sequence of unicode characters. A string is preferred over other formats for the following reasons:

* Forwards compatibility with future versions of Trace-Context and other standards.
* Backwards compatibility with pre-existing ID formats.
* Strongly supported across many languages, and commonly used for transferring data between independent subsystems.

## Alternate Formats
In some cases, additional formats may be appropriate, if a language supports multiple common transport formats. Exposing accessors in other formats should be done to prevent double allocations while formating the identifiers. For example, converting from a tracer’s native format to a string may trigger an allocation. If there are many systems which want to consume the identifier in a format which requires an allocation when converting from a string, a second allocation could occur.

If tracing systems converge on common in-memory formats for Trace-Context identifiers, accessors may be added for those formats at that time.

## Backwards Compatibility and Optional Support
The OpenTracing specification does not currently require trace and span identifiers. To continue support for existing tracers, the empty string value can be returned when no ID has been set.

# Use Cases

## Log Correlation
The primary expected consumer for Trace-Context identifiers are logging systems which run independently from the tracing system. Request level log indexing has become a common practice in logging, and the tracing system contains the mechanism for propagating the relevant identifiers.

## Trace Observers
The OpenTracing community would like to develop secondary observation systems which utilize the tracing runtime, but are tracer independent. Examples include:

* Generating metrics from tracing data.
* Integrating with runtime and system diagnostics.
* Integrating with 3rd-party context-propagation systems.
* Correlating logs, as mentioned above.

# Risk Assessment
Because this proposal includes the exposure of new information, and adds entirely new concepts to the interface, some risks exist.

Existing tracers may not be able to support this feature, as their internal model does not include and client-side trace identifiers.
