# W3C Typed Context Protocol Community Group Charter

**Version 2.0 — Release candidate** · **Date:** Oct, 2026 · **Chair:** Ron Itelman

## 1. Mission

The mission of the W3C Typed Context Protocol Community Group is to develop specifications, vocabularies, implementation guidance, and best practices for making the contextual conditions under which data, messages, and other artifacts are interpreted at declared boundaries referable, measurable, comparable, routable, and auditable across people and systems.

The group's primary technical program is the **Typed ConteXt Protocol (tcxp://)**: a protocol for observing communication events at declared boundaries, making relevant conditions of interpretation referable, comparing an immediate sender's intent with a reader's interpretation, and routing the measured state to an explicit response before or during downstream action.

The group seeks to enable independent human and machine participants to:

1. declare which contextual prerequisites matter for a task or decision;
2. detect when required interpretive information is unresolved;
3. compare declared interpretations when comparable references are available;
4. route the result to `ACT`, `ASK`, or `HALT` behavior under an explicit policy;
5. record the evidence, resolution, and outcome in an auditable trace; and
6. learn from repeated contextual failures without silently imposing one participant's interpretation on another.

The protocol is intended to complement, not replace, existing ontologies, knowledge graphs, schemas, data catalogs, policies, provenance systems, and domain-specific governance. It standardizes a gauge and interaction envelope at a boundary; it does not standardize the meaning of every message.

## 2. Problem Statement

Organizations and technical ecosystems are federated. Their participants commonly use different definitions, schemas, reference data, policies, timeframes, operating assumptions, and local knowledge. Each participant may be internally consistent while a handoff between participants remains open to incompatible interpretations.

When information crosses a boundary without the references needed to evaluate its interpretation, a receiving participant may silently substitute local assumptions. When a required `Context Facet` lacks resolving evidence at a declared crossing, CGP records that facet as `DARK`.

`DARK` identifies a measurable absence of resolving evidence. It does not establish that a misinterpretation occurred; it establishes that fidelity between the immediate sender’s intent and the current reader’s interpretation cannot yet be verified at that crossing. `Dark Context` is the resulting unresolved state across the facets and crossings selected for observation.

The group treats this as an interoperability problem at the boundary between an immediate sender and reader. The protocol's purpose is to make that problem observable, measurable, routable, and auditable.

## 3. Source of Truth and Reference Precedence

This charter governs the Community Group's mission, scope, deliverables, and operating process. It is not the normative protocol specification.

## 4. Protocol Model at a Glance

This section is a charter-level summary. The Notebook Alpha controls the detailed definitions and executable behavior.

### 4.1 Context and the four facets

In this work, **Context** refers to the conditions required to interpret data coherently at a boundary.

A `Spike` contains four facets:

| Facet | Question answered | Illustrative examples | Typical Community Owners |
| --- | --- | --- | --- |
| `Data` | What information crossed the observed boundary? | a value, record, message, table column, or instruction | Data Engineer |
| `Meaning` | What does the Data signify? | Celsius or Fahrenheit; gross or net revenue | Semantic / Ontologists |
| `Structure` | How is the Data encoded, organized, generated, filtered, or validated? | schema version; date format; parser; rounding rule; constraint | API Engineers / Software Developers |
| `World` | To what external entity, event, population, place, time, authority, or situation does the Data refer? | a vessel; a jurisdiction; fiscal year 2026; an applicable policy version | Domain Experts |

A key operational benefit of **`tcxp`**—short for the Typed ConteXt Protocol—is that the contextual requirements contributed by different communities can be represented in one bundled data product rather than fragmented across separate artifacts.

At the gauge layer, CGP is scale-invariant and computationally cheap by design:

- every observed Spike has the same fixed structure: Data, Meaning, Structure, and World;
- each observation adds exactly three measurable Context Facet positions;
- structural overhead is constant per observed Spike; and
- aggregate observation work grows linearly with the number of declared crossings.

**`Context Gauge`**: One dataset or 10,000 changes the number of measurements—not the measurement. Domain-specific resolution and downstream decision analysis may introduce additional costs. The invariance three `Dark Context Facets` provides is treated as our formal gauge, for measuring concepts.

> Once Context has a standard shape, it becomes computable.

`Data` is present by observation: a `Spike` exists because Data crossed the boundary. `Meaning`, `Structure`, and `World` are the three **Context Facets** that can be `DARK` or `LIT`.

- A Context Facet is `DARK` when it carries no resolving `cgvu:` referent at the observed crossing.
- A Context Facet is `LIT` when it carries a resolving `cgvu:` referent.
- `LIT` does not imply agreement. When the immediate sender and current reader expose comparable referents, the gauge can report `MATCH` or `NO MATCH`.
- A `DARK` facet may happen to be interpreted correctly, but the protocol lacks the evidence needed to verify fidelity at that crossing.

A **Dark Facet** is one unresolved `Meaning`, `Structure`, or `World` facet at a declared crossing. **Dark Context** is the resulting unresolved state across the facets and crossings selected for observation. The protocol may report a Dark Facet count or a proportion over a declared observation set.

The Context Gauge does not decide which interpretation is correct or authoritative. That is a governance decision outside the gauge. The gauge makes the relevant conditions and comparisons explicit.

### 4.2 Core components and flow

| Component | Charter-level role |
| --- | --- |
| `Actor` | A human, model, service, application, or other participant that records intent, reads through an interpretation, or flags a condition. Sender and reader are roles in an event, not different kinds of participant. |
| Boundary `Interaction` | An observed `input`, `process`, or `output` event. |
| `Observatron` | A frameless evaluator anchored at a declared boundary. It observes crossings, mints `Spike`s, measures what is present or unresolved, and routes state for evaluation. It does not choose or bind an interpretation. |
| `Spike` | The four-facet object minted when Data crosses an observed boundary: `Data`, `Meaning`, `Structure`, and `World`. |
| `Context State` | The boundary events, Spikes, facets, values, and statuses available for evaluation. |
| `Intent Map` | The declared rulebook specifying what to observe, where to observe it, the relevant facet answer spaces, applicable conditions, and available routing instructions. |
| `Governor` | The routing decision model that evaluates Context State under the Intent Map and applicable policy. Its `Decidability Gate` evaluates whether the current Context fixes the relevant operational verdict despite any remaining unresolved facets. |
| Governor `Verdict` | Exactly one of `ACT`, `ASK`, or `HALT`. This routing verdict is distinct from an operational verdict such as approve/reject or `GO`/`NO-GO`. |
| `Decision Route` and `Handler` | The addressable route selected by the Governor and the registered behavior that performs it. Semantics live in the referenced behavior, not in the gate. |
| `Certificate` | A minimal set of answers that fixes an operational verdict regardless of the remaining Dark facets. The remaining facets may stay Dark while becoming inert for that decision. |
| `Decision Trace` | A durable record of the observed state, comparisons, applicable declarations, resolutions, routing verdict, invoked behavior, and relevant operational outcome. |

The minimal protocol flow is:

1. a declared boundary interaction occurs;
2. an Observatron observes the crossing and mints one or more Spikes as declared by an Intent Map;
3. the Context State records the four facets and their `DARK`/`LIT` status;
4. comparable sender and reader referents may be evaluated as `MATCH` or `NO MATCH`;
5. the Governor applies the Intent Map, Decidability Gate, and applicable policy;
6. the Governor returns `ACT`, `ASK`, or `HALT` and selects an addressable Decision Route;
7. the registered Handler performs the behavior and emits any resulting events; and
8. the decision and resolution evidence are recorded in a Decision Trace.

`HALT` is absorbing for a routing evaluation: once a declared halt condition fires, another input does not lower that evaluation to `ASK` or `ACT`. `ASK` may request a declared answer, support negotiation of a new locally scoped referent, or otherwise open a feedback loop. A suggested or inferred answer does not light a facet until the applicable policy's confirmation or binding requirement has been satisfied.

## 5. Scope

### 5.1 In scope

The Community Group may develop and evaluate:

1. **Core data model and vocabulary.** A model for boundary interactions, Spikes, the four facets, Context State, Dark/Lit status, Match/No Match comparisons, answer spaces, decisions, routes, handlers, certificates, and traces.
2. **Intent Map format.** A declarative, machine-consumable way to specify observation rules, finite candidate answer spaces, required facets, policies, and routing behavior.
3. **Governor and routing behavior.** Testable rules for `ACT`, `ASK`, and `HALT`, including Decidability Gate behavior, addressable routes, resolution handling, and halt witnesses.
4. **Addressing and serialization.** Interoperable identifiers, schemas, serialization formats, registries, and APIs for protocol artifacts, including experimental `cgp:` and `cgvu:` address spaces.
5. **Decision and resolution traces.** Formats for recording what was observed, compared, inferred, asked, confirmed, routed, and decided, with appropriate provenance.
6. **Composition across boundaries.** Methods for evaluating Context across loops, joins, chains, networks of Observatrons, and shared resolution records without silently propagating an interpretation.
7. **Measurement and benchmarking.** Reproducible measures of Dark Context, contextual alignment, decision relevance, resolution cost, regret, value of information, and related outcomes under explicitly declared assumptions.
8. **Interoperability mappings.** Guidance for using CGP with existing semantic, schema, provenance, policy, data-management, and agent technologies.
9. **Security, privacy, and human oversight.** Requirements and patterns for data minimization, consent, provenance, access control, retention, PII handling, safe stopping, and user confirmation in Context-aware systems.
10. **Use cases, implementation experience, and formalization.** Public use cases, reference implementations, conformance tests, finite executable witnesses, and candidate formal proofs.


## 6. Design Principles

The group's work should follow these principles:

- **Gauge, not judge.** Measure whether required interpretive references are present and comparable; do not silently select an authoritative interpretation.
- **Semantic sovereignty.** Preserve each participant's ability to declare and confirm its own interpretation. A cohort suggestion may prefill an `ASK`; it must not resolve the question on the participant's behalf unless a declared policy explicitly authorizes binding.
- **Boundary-local evidence.** State claims in relation to a declared sender, reader, crossing, task, and decision. Do not imply a universal perspective where only a local one was measured.
- **Explicit assumptions.** Declare candidate answer spaces, policies, weighting, costs, thresholds, and observation boundaries used by an experiment or implementation.
- **Deterministic and testable core.** Specify behaviors precisely enough to support repeatable conformance tests and audit replay. Inference may be used, but its role and outputs must remain visible.
- **Addressability and provenance.** Make declarations, routes, handlers, resolutions, and relevant evidence referable and traceable.
- **Composability without silent adoption.** Permit evidence and confirmed resolutions to travel across boundaries while retaining scope, provenance, version, and consent constraints.
- **Implement incrementally.** Permit useful deployment at a single boundary without requiring a universal ontology or centralized knowledge model.
- **Privacy and safety by design.** Minimize captured data, protect sensitive traces and resolutions, and include a safe stopping path where required Context cannot be resolved.

## 7. Deliverables

### 7.1 Group-level deliverables

The Community Group intends to produce the following public work products, one or more of which may be published as W3C Community Group Reports:

1. **Context Graph Protocol Alpha Specification** — the core vocabulary, lifecycle, state model, routing model, and conformance requirements aligned with the Notebook Alpha.
2. **Benchmarking White Paper** — an evidence-based report integrating committee work and evaluating CGP against relevant baseline approaches. Initial work will include a tax-preparation-services knowledge-task problem space and declared measures of cost, accuracy, quality, and the compounding effects of broken Context.
   
The Group Chair serves as lead editor for an integrated white paper and transition materials, with contributor attribution and publication subject to this charter, the applicable decision process, and W3C requirements. Steering Committees and participants may also publish separate materials consistent with the applicable contribution and licensing rules.

### 7.2 Maturity and claims

Every technical deliverable must state its maturity and distinguish among:

- design proposals;
- implemented behavior;
- finite or empirical test results;
- formally verified results; and
- open research questions.

## 8. Group Structure

### 8.1 Community Group Chair

Ron Itelman serves as the Founding Chair and current W3C Community Group Chair. The Chair coordinates the integrated roadmap, charter, specification work, committee structure, public decision record, and any preparation for a potential transition to a W3C Working Group.

### 8.2 Steering Committees

The group organizes focused work through Steering Committees. A Steering Committee Chair coordinates participation, public work items, and the deliverables in that subject area. The committees advise and contribute to the Community Group; they do not independently change this charter or supersede group-level technical definitions.

The initial committee portfolio is:

| Steering Committee | Remit | Initial Chair |
| --- | --- | --- |
| **Semantic Automata** | Develop alpha specification and reference-implementation work for machine-to-machine communication that preserves and exposes relevant Context Facets; investigate formal-language and related techniques for protocol state and routing. | Indranil Mukhopadhyay |
| **Applied Knowledge** | Develop user-centered measures and benchmarks for contextual alignment, missing information, decision impact, and reliance calibration over time. | Audrey Depeige |
| **Agentic Engineering** | Apply CGP across application layers and agent interactions, including PII detection and routing across backend artifacts, frontend inputs, and model outputs. | Alex Brown |
| **Serialization & Specification** | Develop clear, implementable serializations, schemas, APIs, conformance language, and high-quality protocol documentation. | Juan Cruz Viotti |
| **Business & Finance** | Supply real-world business problems, usage scenarios, benchmarks, and success criteria, including inputs to the integrated white paper. | Ajay Wanchoo |
| **Explainability** | Define how systems present relevant facets, evidence, assumptions, routes, and operational consequences so that people can understand and review Context-aware decisions. | Michael Barnett |

Affiliations and professional titles may be maintained in a separate, current roster. They are provided for identification only and do not imply endorsement by an employer or by W3C.

Steering Committee Chairs are appointed by the Community Group Chair. The Chair may appoint, replace, or leave vacant a Steering Committee Chair role based on participation, delivery, group needs, or alignment with the charter. Changes and their effective dates will be communicated respectfully and recorded publicly. Where practical, outgoing chairs should support a reasonable handoff of public work and records.

### 8.3 How participants contribute

Participation is open to anyone under the W3C Community Group process; W3C membership is not required and there is no participation fee. Participants may:

- contribute use cases and contextual-alignment requirements;
- review terminology, models, schemas, and protocol behavior;
- propose features and specification requirements;
- implement or test the alpha protocol;
- contribute benchmarks, test vectors, formalizations, and interoperability mappings; and
- participate in Steering Committees, public discussions, Calls for Consensus, and votes.

No participant is required to join a Steering Committee to contribute to the group.

As the group evolves, we will be actively working to integrate community-driven efforts to develop CGP into a working group.

## 9. Learning by Building and Staying Aligned

The group will learn through open discussion, executable demonstrations, implementation experience, and evidence from use. It will avoid adding process merely to anticipate situations that have not occurred. Additional operating practices may be introduced when experience shows they are needed.

Participants are encouraged to take CGP into new domains and to propose new research, specifications, implementations, and collaborations. To help the group remain aligned, each substantial effort should be able to answer four questions:

1. What contextual-misalignment problem does this work address?
2. How does it use, extend, or explicitly challenge the shared foundation?
3. What evidence, implementation, or test would allow others to evaluate it?
4. How could it contribute to protocol maturity, adoption, or Working Group readiness?

These are alignment questions, not gates on creativity. They provide a common way to connect diverse work back to the group's purpose.

The group will seek shared understanding and consensus. When more formal process is needed, the applicable W3C Community Group process provides the framework.

The destination is a W3C Working Group. The path toward it will be shaped by what the community learns.
