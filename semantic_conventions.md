# Semantic Conventions

The [OpenTracing Specification](https://github.com/opentracing/specification/blob/master/specification.md) describes the overarching language-neutral data model and API guidelines for OpenTracing. That data model includes the related concepts of **Span Tags** and **(structured) Log Fields**; though these terms are defined in the specification, there is no guidance there about standard Span tags or logging keys.

Those semantic conventions are described by this document. The document is divided into two sections: first, tables listing all standard Span tags and logging keys; then guidance about how to combine these to model certain important semantic concepts.

### Versioning

Changes to this file affect the OpenTracing specification version. Additions should bump the minor version, and backwards-incompatible changes (or perhaps very large additions) should bump the major version.

## Standard Tables

### Span tag keys

| Span tag key name   | Type    | Notes    |
|:--------------------|:--------|:---------|
| `error`             | bool    | "true" if and only if the associated Span is in an error state |
| `component`         | string  | The software package, framework, library, or module that generated the associated Span. E.g., `"grpc"`, `"django"`, `"JDBI"`. |
| `sampling.priority` | integer | If greater than 0, a hint to the Tracer to do its best to capture the trace. If 0, a hint to the trace to not-capture the trace. If absent, the Tracer should use its default sampling mechanism. |

### Log field keys


