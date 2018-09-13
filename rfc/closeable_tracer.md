# Closeable Tracers

**Current State:** Draft  
**Author:** [austinlparker](https://github.com/austinlparker)

This proposal builds off of discussions primarily in the Java API surrounding the inclusion of a **Close** method in that library. The primary goal of adding this to the specification is to provide a clean decoupling of OpenTracing `Tracer`s from their concrete implementations in a consistent way across platforms and languages.

# Background
Currently, a `Tracer` has three major responsibilities - creating `Span`s and performing de/serialization of them across boundaries via `Extract` and `Inject`. In practice, a concrete implementation of a `Tracer` has more responsibilities; for example, appending created `Span`s to a buffer, or flushing that buffer through a wire format to some listening system that records traces. One very common method implemented by a concrete `Tracer` is `Close`. The implementation, and utility, of this method varies by language. In general,
calling `Close` on an instantiated `Tracer` will finalize traces and release any resources held by the `Tracer`. 

Discussions (See [this thread](https://github.com/opentracing/opentracing-java/issues/250) for example) around the inclusion of a `Close` method in Java have crystallized around including this feature in the Java interface. Specifically, there would be value in having `Close` specified on the OpenTracing interface in order to prevent a cast to the concrete tracer, which cannot occur in a situation where the implementation is being loaded dynamically. This situation exists in C# as well, where a similar pattern of try-with-resource (called `using`) exists.

# Specification Changes
The `Tracer` interface is extended with the following:

* **Close**, a method that is responsible for closing the active tracer. A `Tracer` that has been closed should not be able to record any new spans. For `Tracer`s that record `Span`s for persistance, calling **Close** should flush the current in-memory collection to the configured persistance store. For stateless `Tracer`s or similar, this can be a NoOp. The **Close** method should be considered idempotent; closing an already closed `Tracer` should not raise an error.

# Use Cases
The primary use case of this enhancement would be to reduce vendor-specific code for developers who are adding OpenTracing to their code. In lieu of having to perform potentially unsafe casts of a `Tracer` to access it's implementation-specific `Close` method, the interface would ensure that it was available regardless.

In addition, languages that support try-with-resource or using patterns (Java and C# respectively) will be able to have their `Tracer` implementations implement the respective interfaces in those languages to allow for a more natural usage of the concrete `Tracer`. For example, in C# the following initialization code could be used -

```csharp
using (var tracer = new Tracer()) {
    GlobalTracer.Register(tracer);
    // perform more initialization and setup
    // start main loop or do work
}
// if an exception occurs, the Dispose method of tracer would be called to enable for cleanup and batching/sending of Spans.
```

# Risk Assessment
The following risks have been identified:

* While this is an additive change, it could potentially require changes in existing implementations to meet the new interface signature, especially if they already have a **Close** method in their `Tracer`.
* Since the behavior of **Close** is variable between stateful and stateless tracers, potential for confusion/misuse by end users exists. This is addressable via documentation and education, however.