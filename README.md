# The OpenTracing Specification repository

OpenTracing Specification Official Website: [http://opentracing.io/spec](http://opentracing.io/spec).

The [opentracing/specification](https://github.com/opentracing/specification) repository is a place to document (and discuss) the OpenTracing specification itself (independent of any particular language or platform).

Historically this activity took place on the docs repo ([github.com/opentracing/opentracing.io](https://github.com/opentracing/opentracing.io)), but that became awkward since trivial website presentation and highly non-trivial semantic discussions were happening under the same namespace. The `specification` repository was thus proposed as part of [opentracing.io#144](https://github.com/opentracing/opentracing.io/issues/144).

## What's here

There are two important files in this repository:

1. [`specification.md`](https://github.com/opentracing/specification/blob/master/specification.md), a versioned description of the current pan-language OpenTracing standard
1. [`data_conventions.yaml`](https://github.com/opentracing/specification/blob/master/data_conventions.yaml), a structured YAML file describing standard Span tags and Span log keys
