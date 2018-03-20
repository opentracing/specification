# Versioning Process for the OpenTracing Specification
The OpenTracing specification consists of a cross-language, abstract interface, along with an attendant set of language-specific interfaces. Every interface is versioned, with each language interface supporting a version of the abstract interface.

In order effect changes to the OpenTracing specification, all interfaces are developed together in a three step process:

* **DRAFT:** An RFC is drafted to define and justify the desired changes.
* **TEST:** A new version of each language interface is designed, tested, and released.
* **ACCEPTED:** The abstract interface is versioned to reflect the final changes.

# Proposal Components

## RFC (Request for Comments)
Proposals begin as a single document, written in markdown and committed to the `/rfc` directory.

An RFC consists of the following:

* Proposal name, status, and author.
* Problem statement.
* Historical background.
* Abstract interface.
* Use cases.
* Risk Assessment.

An RFC has the following qualities:

* The issue is clearly defined, and within the scope of the OpenTracing charter.
* The utility to the OT community is illustrated with concrete use cases. It should be clear not only what will change, but how the new interface is expected to be used.
* Changes to the abstract interface are clearly defined. Language-specific interfaces will be implemented in a later phase, but the abstract interface must be clear enough that the changes could be ported to many languages without much deviation.
* All expected behavior should be documented, in sufficient detail such that language-specific tests and examples can be generated from the descriptions.
* Risks, backwards compatibility, and potential for inconsistency are addressed.

## Tracking Issue
Often, a proposal will consist of multiple artifacts, such as GitHub issues, pull requests, release candidates, etc. In order to manage these resources, a single, long-running tracking issue is assigned to each proposal.

A tracking issue consists of the following:

* Proposal name, status, and author.
* Short summary or proposal abstract.
* A link to the RFC.
* Lists of open issues, PRs, and upcoming deadlines.
* Links to relevant artifacts, such as release candidates.
* Links to meetings and other discussion channels.

## Release Candidates
Each language interface implements the abstract interface contained in the RFC.

A complete release candidate consists of the following:
* Repository branch containing the new version.
* Packages for installing the new version.
* More than one tracer which binds to the new version.
* Major instrumentation ported to the new version.

# Proposal Lifecycle

## Draft
* Pull request to add the draft RFC to `/rfc`. 
* Tracking issue for the proposal is opened.
* Debate continues via pull requests against the draft.

A draft proposal is first submitted as a pull request against the specification repository. 

Once there is consensus that the pull request meets the minimum criteria for the beginnings of a draft, and has strong potential to be accepted, the pull request is merged. A tracking issue is created at this point to manage the proposal. The draft RFC is then refined via more pull requests.

Consensus is used to determine that all of the abstract proposal work has been completed, and the investigation should move to language implementations.

## Test
* RFC status changed from `Draft` to `Test`.
* Release Candidate per major language is created.
* API conventions are coordinated between languages.
* New risks and ambiguities may lead to proposal changes.

Once the draft proposal is finalized, language implementation begins. Language maintainers begin creating a release candidate on a branch of the language repository. Links to the release candidates, along with their status, are recorded in the tracking issue. Multiple tracers create a branch which is compatible with new API, along with important instrumentation libraries. These assest are then used to vet the new API, exploring edge cases and potential issues.

At this stage, issues and further refinements are surfaced and debated via tested examples, as English often is too imprecise to address the finer points of API design.

## Accepted
* Remaining language interfaces are released.
* Abstract interface is versioned and updated.
* Documentation is updated.
* RFC status changed from `Test` to `Accepted`.
* Tracking issue for the proposal is closed.

After a quorum of language interfaces have been tested and released, consensus may be reached that a proposal has been finalized. At this point, the abstract interface contained in the `specification.md` document is updated to reflect the final changes, based on the language in the RFC document and release candidates.

In addition to the specification, the OpenTracing website and other major documentation efforts are updated at this time to reflect the latest version. Once all work is complete, the tracking issue is closed.


