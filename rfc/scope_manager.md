# Scope Manager

**Current State:** Draft  
**Author:** [carlosalberto](https://github.com/carlosalberto)

In the OpenTracing specification, under the "Optional API Elements" section, it is mentioned languages may choose to provide utilities to pass an active **Span** around a single process.

Upon many iterations and feedback from several contributors, the Java 0.31 API defined the new **Scope Manager** concept, which is a simple and explicit way to manage the active **Span** at a given call-context point. This document intends to standardize this concept, so other supported languages and platforms can leverage it, with their respective semantical differences.

# Technical background

For any thread, at most one **Span** may be "active". Of course, there may be many other **Spans** involved with the thread which are (a) started, (b) not finished, and yet (c) not "active": perhaps they are waiting for I/O, blocked on a child **Span**, or otherwise off of the critical path.

For platforms where the call-context is propagated down the execution chain -such as `Go`-, such context can be used to store the active **Span** at all times.

For platforms not propagating the call-context, it's inconvenient to pass the active **Span** from function to function manually, so OpenTracing should require, for those platforms, that **Tracer** contains a **Scope Manager** that grants access to the active **Span** through a container, called **Scope** (using some call-context storage, such as thread-local or coroutine-local).

# Specification Changes

New `ScopeManager` and `Scope` interfaces are added to the specification, and the `Tracer` interface is extended to support creation of `Span`s that are automatically set as the **active** one for the current context.

## ScopeManager

The `ScopeManager` interface allows setting the active `Span` in a call-context storage section, and has the following members:

* **activate**, capability to set the specified `Span` as the active one for the current call-context, returning a `Scope` containing it. A required boolean parameter **finish span on close** will mark whether the returned `Scope` should, upon deactivation, **finish** the contained `Span`.
* **active**, the `Scope` containing the current active `Span` if any, or else null/nothing.

## Scope

The `Scope` interface acts as a container of the active `Span` for the current-call context, and has the following members:

* **span**, the contained active `Span` for this call-context. It will never be null/nothing.
* **close**, marking the end of the active period for the current `Span`, and optionally **finishing** it. Calling it more than once leads to undefined behavior.

If the language supports some kind of auto finishing statement (such as `try` for Java, or `with` for Python), `Scope` should adhere to such convention. Additionally, `Scope` is not guaranteed to be thread-safe.

## Tracer changes

The `Tracer` interface will be extended with:

* **scope manager**, the `ScopeManager` tracking the active `Span` for this instance.
* **start active span**, a new behavior for starting a new `Span`, which will be automatically marked as active for the current call-context. It will return a `Scope`. A parameter **finish span on close** will mark whether the `Scope` should, upon deactivation, **finish** the contained `Span`. A default value for this parameter may be provided, depending on the suitability for the language and its use cases.
* Both **start span** and **start active span** will implicitly use any active `Span` as the parent for newly created `Span`s (with a `ChildOf` relationship), unless the parent is explicitly specified, or (the new) **ignore active span** parameter is specified (in which case the resulting `Span` will have no parent at all).

# Use Cases

## Single-threaded operations.

The active `Span` will be accessible at all times through the `Tracer`, without any need to pass the `Span` around when creating children.

## Multi-threaded operations.

The active `Span` will be accessible at all times, probably using thread-local storage, and it will be possible to pass `Span` instances between threads and manually manage its active period, and have full, manual control on when it should be finished.

# Risk Assessment

The following risks have been identified:

* This change will mean API breakage, as both **start span** and **start active span** behaviors of `Tracer` will implicitly use any active `Span` as the parent for newly created `Span`s (previously, if no parent was specified, all created `Span`s were parentless).
* For platforms having a prior version with a different in-context propagation (as it happened to the 0.30 Java version, which included a concept called `ActiveSpanSource`, which used reference-count to handle the lifetime of `Span`s), either a full migration will be needed, or at least a shim/bridge layer should be provided to ease with the proposed changes.
* There can be languages and platforms for which, given their specific threading or memory models, implementing this changes will either be not sufficient or redundant.

