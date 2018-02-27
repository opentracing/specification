# 0001: Trace-Parent Accessors

**Updated:** February 26, 2018  
**Current State:** Draft  
**Author:** [tedsuo](https://github.com/tedsuo)

The Opentracing model of computation specifies two primary object types, **spans** and **traces**, but does not specify identifiers for these objects. The lack of identifiers makes it impossible to correlate tracing data with data in other systems. This complicates a number of important tasks, and prevents the creation of reusable trace observers.

Meanwhile, [Trace-Context HTTP headers](https://github.com/w3c/distributed-tracing) are in the process of being standardized via the w3c. The tracing community has voiced strong support in implementing these headers for use in tracing interop.  In order to dovetail with this effort, we propose adding accessors for these fields to the OpenTracing `SpanContext` interface.

Note that this proposal only includes the `Trace-Parent` header. It does not address the `Trace-State` or `Correlation-Context` headers.

# The Trace-Parent HTTP Header
Before discussing the OpenTracing interface, it’s worth describing the proposed `Trace-Parent` header at is will appear in HTTP.  Note that the header is currently being renamed from `Trace-Parent` to `traceparent`.

The `traceparent` header contains the following fields: `version`, `trace-id`, `span-id`, and `trace-options`.

`Version` is 1-byte representing a 8-bit unsigned integer. Version 255 reserved.

`Trace-id` is the ID of the whole trace forest. It is represented as a 16-bytes array, e.g.,4bf92f3577b34da6a3ce929d0e0e4736. All bytes 0 is considered invalid. Implementation may decide to completely ignore the Trace-Parent if the trace-id is invalid.

`Span-id` Is the ID of the caller span (parent). It is represented as a 8-bytes array, e.g., 00f067aa0ba902b7. All bytes 0 is considered invalid. Implementation may decide to completely ignore the Trace-Parent if the span-id is invalid.

`Trace-options` is an 8-bit unsigned integer. The least significant bit (the 7th bit) provides recommendation whether the request should be traced or not (`1` recommends the request should be traced, `0` means the caller does not make a decision to trace and the decision might be deferred). When `trace-options` is missing the default value for this bit is `0`.

# Specification Changes
The `SpanContext` section of the specification is extended to include the following properties:

* `TraceID`, accessible as a **string**.
* `SpanID`, accessible as a **string**.
* `IsSampled`, accessible as a **boolean**.

## Identifiers (SpanID and TraceID)
**String** values are used for identifiers. In this language-independent context, a string is defined as an immutable, variable length sequence of unicode characters. 

While a more specific value types could work for the current identifiers, such as a 16-byte array for `TraceID`, a more general purpose format is preferred for the following reasons:

* Forwards compatibility with future versions of Trace-Context.
* Backwards compatibility with pre-existing ID formats.
* Common formats such as strings are strongly supported across languages as a means of transferring data between independent subsystems.

### Alternate Formats
In some cases, additional formats may be appropriate, if a language supports multiple common transport formats. Exposing accessors in other formats should be done to prevent double allocations while formating the identifiers. For example, converting from a tracer’s native format to a string may trigger an allocation. If there are many systems which want to consume the identifier in a format which requires an allocation when converting from a string, a second allocation could occur. 

If tracing systems converge on common in-memory formats for Trace-Context identifiers, accessors may be added for those formats at that time.

### Backwards Compatibility and Optional Support
The OpenTracing specification does not currently require trace and span identifiers. To continue support for existing tracers, the empty string value can be returned when no ID has been set.

## Trace-Options (IsSampled)
Rather than expose Trace-Options as a separate interface, the individual options are exposed directly on SpanContext. Currently, there is only one option.

`IsSampled` indicates whether the current trace is being recorded. Note that users are not expected to check this value before interacting with any OpenTracing interface.

## Version
This proposal do not include exposing the header `Version` number, as this field is an implementation detail only relevant to parsing the header.

# Use Cases

## Identifiers (SpanID and TraceID)

### Log Correlation
The primary expected consumer for Trace-Context identifiers are logging systems which run independently from the tracing system. Request level log indexing has become a common practice in logging, and the tracing system contains the mechanism for propagating the relevant identifiers.

### Trace Observers
The OpenTracing community would like to develop secondary observation systems which utilize the tracing runtime, but are tracer independent. Examples include:

* Generating metrics from tracing data.
* Integrating with runtime and system diagnostics.
* Integrating with 3rd-party context-propagation systems.
* Correlating logs, as mentioned above.

## Trace-Options (IsSampled)
**INCOMPLETE** Before this draft proposal is accepted, the use cases for `IsSampled` must be enumerated.

# Risk Assesment:
Because this proposal includes the exposre of new information, and adds etirely new concepts to the interface, some risks exist. Additionally, there are some risks in coordinating with an separate, independent standardization effort. 

The following potential risks have been identified for this proposal:

* The Trace-Context header specification may be versioned into a radically different form, which may not be supportable via this interface.
* Trace-Context headers have not yet been widely adopted. If the effort was abandoned in favor of a different project, this interface may turn out to be incorrect.
* Tracers may not be able to support this feature, regardless of their support for the Trace-Context headers.

These concerns are mitigated by the strong overlap in participation between the OpenTracing and Trace-Context community.