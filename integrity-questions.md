# Source Engineering -- Integrity Questions

> The five central integrity questions. Each becomes a normative requirement in the specifications repo.

The integrity questions are the bridge between the conceptual model and the specifications. Each question names a property the system must satisfy and a failure mode it prevents. The integrity questions are normative for the specifications repo and for the conformance suite.

## Source integrity

**Question.** Can each source and relevant version be identified and retrieved?

**What it requires.** Every SourceArtifact in the discipline has a stable identifier, an addressable version (via content hash, not numeric version), an Origin that produced it, an asserted Authority, and a TemporalValidity window. A reviewer can resolve the identifier to the actual content.

**What failure it prevents.** A reference to a source that the reviewer cannot resolve. The failure mode is "the model cited a thing that doesn't exist, or cited a thing that has changed, or cited a thing the reviewer cannot get." Without source integrity, the entire evidence chain is unverifiable.

## Contextual integrity

**Question.** Was the evidence valid for the decision's scope and point in time?

**What it requires.** Every Qualification is made under a recorded Context. The Context carries the runtime parameters (temperature, top-p, prompt cache state, tool budget, retrieval depth), the model identifier and version, and the constraints in effect. The TemporalValidity of the source is checked against the decision's scope and timestamp. A source used outside its temporal validity is flagged.

**What failure it prevents.** A decision made on the basis of a source that was valid in 2024 but not in 2026. The failure mode is "the model cited something that used to be true" or "the model used a parameter setting that is no longer appropriate." Without contextual integrity, the decision's audit surface is unmoored from the conditions of its production.

## Evidential integrity

**Question.** Can each material claim be traced to supporting or contradicting evidence?

**What it requires.** Every Claim in the response carries at least one EvidenceItem, and every EvidenceItem carries a Provenance that links it back to a SourceArtifact. Contradictions are first-class: a Claim's EvidenceItems include both the supporting evidence and the contradicting evidence. The Provenance chain is reconstructable from the EvidenceItem to the source via the recorded transformations.

**What failure it prevents.** A confident answer that the model cannot back up. The failure mode is "the model asserted X without referencing any input that produced X" or "the model asserted X but the cited evidence does not actually support X." Without evidential integrity, the discipline's central claim (that the response is auditable) is hollow.

## Decision integrity

**Question.** Can a reviewer reconstruct the declared basis for the decision and identify unresolved gaps?

**What it requires.** Every Decision carries a DecisionBasis that lists the supporting evidence, the counterevidence, the constraints in effect, and the known gaps. The rationale is in domain terms, not in chain-of-thought. A reviewer can read the DecisionBasis and answer two separate questions: "did the model consider the right things?" and "given what was considered, is this the right call?"

**What failure it prevents.** A decision that is structurally valid but wrong. The failure mode is "the model returned a decision that fits every declared option but the option the user needs was not in the declared set" or "the model did not consider the counterevidence that would have changed the call." Without decision integrity, the discipline's judgment-gap closure is not engineering, it is rhetoric.

## Lifecycle integrity

**Question.** Can subsequent source changes, corrections, and validation failures be traced to affected decisions?

**What it requires.** Every DecisionRecord carries an AuditHistory that records changes to the record: appeals, re-runs, corrections, escalations, supersessions. When a SourceArtifact is updated (new version, expired validity, withdrawn authority), the discipline can identify every DecisionRecord that cited it. When a DecisionRecord is corrected, the discipline can identify the previous state and the rationale for the correction.

**What failure it prevents.** A decision that quietly goes stale without surfacing. The failure mode is "the model returned a decision based on a source that has since been corrected, but the user is still acting on the old decision." Without lifecycle integrity, the discipline cannot support re-evaluation, appeal, or supersession.

## How the integrity questions are operationalised

Each integrity question becomes a normative requirement in `Source-Engineering/source-engineering-specifications`. The requirements are testable: the conformance suite in `Source-Engineering/source-engineering-conformance` runs them.

| Integrity question | Becomes in `specifications/` | Becomes in `conformance/` |
|---|---|---|
| Source integrity | `source-addressability.md` -- the schema requirement that every SourceArtifact is addressable | A conformance test that takes a COTS and verifies that every SourceArtifact id resolves to a content hash that matches the source |
| Contextual integrity | `context-record.md` -- the schema requirement that every Qualification is made under a recorded Context | A conformance test that takes a COTS and verifies that the TemporalValidity of every cited source is checked against the decision's timestamp |
| Evidential integrity | `evidence-chain.md` -- the schema requirement that every Claim carries EvidenceItems with Provenance | A conformance test that takes a COTS and verifies that every Claim has at least one EvidenceItem and that every EvidenceItem's Provenance resolves to a SourceArtifact |
| Decision integrity | `decision-basis.md` -- the schema requirement that every Decision carries a DecisionBasis with supporting evidence, counterevidence, constraints, and known gaps | A conformance test that takes a COTS and verifies that every Decision carries a DecisionBasis |
| Lifecycle integrity | `audit-history.md` -- the schema requirement that every DecisionRecord carries an AuditHistory | A conformance test that takes a COTS and verifies that the AuditHistory is append-only and complete |

## What the integrity questions are not

- **Not a compliance checklist.** The integrity questions are not a yes/no checklist. Each question is a property the system satisfies to a calibrated degree. A conformance failure is not "the system fails to be Source Engineering"; it is "the system's evidence chain for this decision has a specific gap that the user can see."
- **Not a substitute for review.** The integrity questions make the decision auditable; they do not guarantee the decision is correct. Review is still required; the questions make the review possible.
- **Not a critique of any model.** The integrity questions are properties the discipline's surface satisfies, not properties of the model itself. A model that returns a COTS with high integrity is auditable; the discipline does not judge the model.
- **Not static.** The integrity questions are a v0.1 set. New questions can be added via an RFC, with the same lifecycle as any other normative requirement.

## Related documents

- `model.md` -- the conceptual model the integrity questions are derived from.
- `entities.md` -- the data model that operationalises the integrity questions.
- `Source-Engineering/source-engineering-specifications` -- the normative requirements derived from the integrity questions.
- `Source-Engineering/source-engineering-conformance` -- the test suite that runs the integrity questions.