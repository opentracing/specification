# Multithreading Span usage.

**Current State:** Draft
**Author:** [carlosalberto](https://github.com/carlosalberto)

In the OpenTracing specification, there is no mention of **Span** behavior under multithreaded applications, nor clarification of any thread-safety requirement. This proposal is thus intended to be explicit about this specific scenario.

# Background

Different tracing systems have historically taken different decisions regarding this, with many of them making **Spans** (and their operations) thread-safe. Yet, it has been shown lately that a few frameworks have decided to not make **Spans** thread-safe, and instead provide their own ways to handle this scenario. This could in the near future create confusion among both users and tracing authors, as well as incompatibilities among implementations.

# Specification Changes

The `Span` interface adds one or more paragraph detailing the expected behavior of the objects under multithreading scenarios, specially when a single operation may take place through different threads (maybe using a thread pool). `Span` objects should then either:

* Being thread-safe in all its operations (logs, tags, baggage).
* Guaranteed to work fine and correctly under the described scenarios.

# Use Cases

## Application using a thread pool

Multithreaded applications using a thread pool, with the user creating a `Span` for a given operation, which would be executed as a series of callbacks, each one running on a different, unknown thread.

For many frameworks, this could be approached with the `Span` being passed between the threads/callbacks during its execution, to add logs, set tags or modify its baggage.

# Risk Assessment

The following risks have been identified:

* This change may affect tracing systems that have taken for granted that there is no need to provide **any** form of thread-safety, as they would need to update or change its own API or code.

* The clarification may stay too vague or be insufficient in the long term, regarding the frameworks that decide to not provide thread-safety. Thus a proper, clear addition needs to happen for this case.
