# OpenTracing Project Organization

[OpenTracing](http://opentracing.io) is a set of standard APIs that consistently model and describe the behavior of distributed systems. There are three constituencies that care about this standard:

1. **Tracing tool maintainers:** "Instrumenting all software" is an unreasonable goal for any one particular tracing or monitoring project or vendor. OpenTracing essentially amortizes this work.
2. **Software developers who build and deploy applications:** These developers want to use whatever tracing and observability tools that work best within their organization's infrastructure, and they want to choose those tools independent of whatever third-party (open-source) software packages they happen to have built around.
3. **Software developers who contribute to widely-used software:** Inasmuch as this software needs to exist within a microservices deployment, it must integrate with whatever tracing tool(s) its diverse users already depend on.

OpenTracing's project organization makes room for each of these constituencies.

## OpenTracing Specification Council (OTSC)

The authors of any tracing or observability tool that supports OpenTracing are **always** invited to contribute to discussions about the future of OpenTracing as a standard. OpenTracing endeavors to maintain a list of such authors and will occasionally solicit feedback from that group.

A subset of these tracing system authors are part of the **OpenTracing Specification Council** (OTSC). Said council members have write access across the [github.com/opentracing](https://github.com/opentracing) and [github.com/opentracing-contrib](https://github.com/opentracing-contrib) organizations (though other individuals may also have write access to specific repos within those organizations). Each OTSC member represents a tracing system which (a) maintains a public OpenTracing implementation, and (b) has substantial institutional support, and thus longevity. The current OpenTracing Specification Council is as follows (in alphabetical order by username):

- Bas van Beek ([@basvanbeek](https://github.com/basvanbeek)): Zipkin
- Ben Sigelman ([@bhs](https://github.com/bensigelman)): LightStep
- Chris Erway ([@cce](https://github.com/cce)): TraceView
- Fabian Lange ([@CodingFabian](https://github.com/CodingFabian)): Instana
- Erika Arnold ([@erabug](https://github.com/erabug)): New Relic
- Emanuele Palazzetti ([@palazzem](https://github.com/palazzem)): DataDog
- Pavol Loffay ([@pavolloffay](https://github.com/pavolloffay)): Hawkular
- 吴晟 (Wu Sheng) ([@wu-sheng](https://github.com/wu-sheng)): SkyWalking
- Yuri Shkuro ([@yurishkuro](https://github.com/yurishkuro)): Jaeger

OTSC members have the following primary responsibilities:

- Define priorities for OpenTracing as a project and standard
- Accurately represent the concerns and needs of downstream tracing and observability tools
- Strategize around possible third-party package integrations
- Weigh in and offer tie-breaking votes for issues where consensus among the larger community has proven elusive

OTSC meetings agenda and minutes are recorded in [this Google document](https://docs.google.com/document/d/1Pc86BVWVGnldsdAtwDQLfMWEpO4ni4IHm0xaNQvnScI/).

## OpenTracing Industrial Advisory Board (OTIAB)

OpenTracing has an **Industrial Advisory Board** (OTIAB) that comprises engineers with detailed knowledge of tracing technology at companies of different scales and software maturity. The OTSC members engage with the OTIAB on a periodic basis to gather feedback about successes, challenges, and open-source packages which would benefit from greater OpenTracing support.

The current OpenTracing Industrial Advisory Board is as follows (in alphabetical order):

- Brian Hanafee ([@bhanafee](https://github.com/bhanafee)): Wells Fargo
- Bruce Wong ([@brucemwong](https://github.com/brucemwong)): StitchFix
- Dimitrios Kouzis-Loukas ([@lookfwd](https://github.com/lookfwd)): Bloomberg
- Jonathan Kaldor ([@jmkaldor](https://github.com/jmkaldor)): Facebook
- Jonathan Mace ([@jonathanmace](https://github.com/jonathanmace)): Brown University / PivotTracing
- Stephen Day ([@stevvooe](https://github.com/stevvooe)): Docker
- Suman Karumuri ([@mansu](https://github.com/mansu)): Pinterest
- 吴晟 (Wu Sheng) ([@wu-sheng](https://github.com/wu-sheng)): Huawei

OTIAB members have the following primary responsibilities:

- Describe and rationalize tracing and observability challenges within their own companies: it is particularly important that they are regularly exposed to instrumentation, integration, and usability issues related to tracing, and further that they are able to effectively distill these concerns and communicate them back to the OTSC
- Participate in periodic (but infrequent: monthly or less) calls with the OTSC
- Provide feedback about possible priorities and/or specific proposals from the OTSC
- Represent OpenTracing’s interests within their own organizations

## OpenTracing Working Groups

Working Group members meet regularly to discuss and define the OpenTracing project structure, as well as perform day-to-day project management duties.

### Cross-Language Working Group

The Cross-Language Working Group manages the OpenTracing APIs and ecosystem. In order to handle the growing volume of contributions, this working groups is tasked with overseeing the development and maintenance of the OpenTracing language APIs and contributed instrumentation. 

Any number of working group members are allowed; preferably at least two or more experts per language. Members with overlapping responsibilities divide the work amongst themselves.

#### Responsibilities
- Participate in Cross-Language Working Group sessions.
- Develop protocols for proposing and resolving Issues and Pull Requests.
- Triage backlogs for API and OT-contrib repositories.
- Actively participate in PR reviews.
- Work with OT-contrib maintainers to ensure instrumentations are not abandoned, have tags applied uniformly, and are well tested.
- Ensure that APIs do not drift away from each other, or the core specification.
- Inform the community with monthly activity summaries and versioning announcements.

### Documentation Working Group

The Documentation Working Group codifies the existing knowledge base and maintains proper documentation as part of the OpenTracing website.

#### Documentation Projects
- Project Overview
- Cross-Language Specification
- Tags
- Language APIs
- OpenTracing CookBook
- Walkthroughs and Tutorials
- FAQ
- Index of Instrumentations
- Guidelines for framework/library maintainers

#### Responsibilities
- Schedule and attend Documentation Working Group sessions.
- Maintain community guidelines for contributing documentation changes.
- Setup and maintain documentation infrastructure.
- Produce missing documentation.

## Contributed OpenTracing Support

Repositories under [github.com/opentracing-contrib](https://github.com/opentracing-contrib) pertain to specific open-source software packages and projects. Each may have its own owners and internal policies regarding PRs, review requirements, and committer management. You can learn more about OpenTracing contributions via the [opentracing-contrib meta-repository](https://github.com/opentracing-contrib/meta).

## Afterword: what's the point of `^^^`?

Sometimes formal governance and acronyms like "OTSC" or "OTIAB" signify hierarchy and exclusion in an open-source effort. The intention with OpenTracing's organization is to do exactly the opposite: by guaranteeing participation from multiple constituencies and multiple software projects and organizations, these simple governance structures aim to bring more ideas to the table. Inasmuch as it's possible, all OpenTracing discussions will take place out in the open and/or at clearly-advertised times and (virtual) places.

If you would like to take on a more formal role on the OTSC or OTIAB, please reach out to an existing OTSC member and we can take it from there.

