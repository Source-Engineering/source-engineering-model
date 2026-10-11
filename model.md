# Source Engineering -- Conceptual Model

> The discipline's formal model. What the discipline thinks in.

This document is the canonical conceptual model. It names the five stages every Source Engineering decision goes through, the entities that inhabit each stage, the relationships between entities, and the questions each stage answers. The model is normative for the specifications repo and the architecture repo; both derive their contracts from this document.

The model is deliberately distinct from the model's internal reasoning. The discipline does not assume access to private chain-of-thought or that a generated explanation accurately describes internal model computations. The model is about inspectable sources, explicit evidence relationships, recorded transformations, and verifiable decision criteria.

## The five stages

The discipline's decision lifecycle has five named stages. Each stage takes the artefacts of the previous stage as inputs and produces artefacts for the next. The stages are not "phases" in time; they are structural stages that coexist in every COTS.

```
+-----------------------------------+
|  Stage 1 -- Source Universe         |
|  (the artefacts the decision cites) |
+-----------------------------------+
                |
                v
+-----------------------------------+
|  Stage 2 -- Qualification and       |
|  Contextualisation                 |
+-----------------------------------+
                |
                v
+-----------------------------------+
|  Stage 3 -- Evidence Construction   |
+-----------------------------------+
                |
                v
+-----------------------------------+
|  Stage 4 -- Decision Basis          |
+-----------------------------------+
                |
                v
+-----------------------------------+
|  Stage 5 -- Decision Record         |
+-----------------------------------+
```

## Stage 1 -- Source Universe

The Source Universe is the set of artefacts that can be cited as inputs to a decision. The discipline does not include the model's training data in the Source Universe at the row level (this is the foundational question Q-F.2 in `Source-Engineering/source-engineering-research/agenda.md`); the Source Universe is the set of inspectable artefacts the model was given at the call.

### Entities

- **Source artifact.** A discrete piece of content that can be cited. A document, a row in a database, a previous model's output, a calibration parameter set. Every source artifact has an identifier the discipline can address.
- **Origin.** The system that produced the source artifact. The origin's name, version, and configuration at the time of production.
- **Version.** A specific state of the source artifact. Versioning is mandatory: a source without a version is not addressable.
- **Authority.** The provenance authority of the source artifact. Authority carries the model's trust in the source's accuracy, freshness, and completeness; the discipline does not assess authority, the model operator does, but the discipline records it.
- **Temporal validity.** The window of time during which the source artifact is considered valid for the decision. A financial rule valid on day T is not valid on day T+1.

### What this stage answers

The stage answers the question: **What artefacts can be cited as the basis for this decision?** The Source Universe is a closed set at the call; the discipline cannot add to it after the fact without recording the addition as a transformation in Stage 3.

## Stage 2 -- Qualification and Contextualisation

The Qualification and Contextualisation stage assigns each source artifact to a position in the decision's context. The qualification is the model's evaluation of the source's fitness for the decision; the contextualisation is the record of the runtime parameters in effect when the model considered the source.

### Entities

- **Qualification.** The model's assessment of a source's fitness for the decision. Qualification captures the model's reasoning (in domain terms, not in chain-of-thought) about why this source is or is not relevant.
- **Context.** The runtime parameters in effect at the call: temperature, top-p, prompt cache state, tool budget, retrieval depth, model version. The context is a first-class data point back to the user.
- **Contextual constraint.** A constraint on the source imposed by the context (e.g. a recency window, a jurisdiction filter, a redaction policy). Contextual constraints are declared, not inferred.

### What this stage answers

The stage answers the question: **Under what conditions, and against what constraints, was each source considered?** The qualification and the context together form the audit surface for the model's "why this source" decision.

## Stage 3 -- Evidence Construction

The Evidence Construction stage builds the chain of evidence that supports each material claim in the response. The evidence is what a reviewer can examine, re-run, and challenge.

### Entities

- **Claim.** A material assertion in the response. Every claim that the user might need to verify is an evidence-bearing claim; cosmetic claims (greetings, framing language) are not.
- **Evidence item.** A discrete unit of evidence supporting, contradicting, or contextualising a claim. An evidence item has a source (from the Source Universe), a transformation (what the model did to the source to produce the evidence), and an uncertainty (the model's confidence in the evidence).
- **Provenance.** The chain that links an evidence item back to its source. Provenance is mandatory: an evidence item without a provenance is not an evidence item, it is a claim.
- **Contradiction.** An evidence item that contradicts another evidence item, or contradicts a claim. Contradictions are first-class; the discipline does not assume the evidence is internally consistent.
- **Uncertainty.** The model's stated uncertainty about the evidence. Uncertainty is a number on a calibrated scale, not a verbal hedge.

### What this stage answers

The stage answers the question: **What supports this claim, and how uncertain is the support?** The evidence chain is the discipline's defence against the structurally-valid-but-wrong failure mode.

## Stage 4 -- Decision Basis

The Decision Basis stage aggregates the evidence into a decision. The Background "given the evidence, is this the right call?" is the question the discipline separates from "given what was considered, is this the right call?" (which is Stage 3).

### Entities

- **Supporting evidence.** The subset of evidence items that supports the decision.
- **Counterevidence.** The subset of evidence items that contradicts the decision or contradicts a claim the decision relies on. Counterevidence is mandatory: a decision without counterevidence is not a Source Engineering decision.
- **Constraints.** The decision constraints in effect (policy rules, jurisdictional rules, budget constraints, latency constraints). Constraints are declared; the discipline does not infer them.
- **Known gaps.** The explicit list of things the model does not know and the decision does not consider. Known gaps are first-class; the discipline records them.

### What this stage answers

The stage answers the question: **Given the evidence, why is this the right call, and what could be wrong with it?** The decision basis is the surface the user reviews when they ask "should I act on this?"

## Stage 5 -- Decision Record

The Decision Record is the persistent artefact that captures the decision, the evidence chain, and the validation status. It is the unit the discipline keeps; not the unit it discards.

### Entities

- **Decision.** The decision itself. The decision is a discrete statement, not a probability; the probability is in the decision's uncertainty, not in the decision's truth value.
- **Evidence-chain reference.** The identifier of the evidence chain that supports the decision. The evidence chain is reconstructed by walking the decision record back through the decision basis to the evidence items.
- **Validation status.** The state of the decision's validation: pending, validated, contested, appealed, superseded.
- **Responsible authority.** The named human or organisational authority that owns the decision. The authority is the reviewer; the discipline does not assign authority, it records it.
- **Audit history.** The append-only log of changes to the decision record: appeals, re-runs, corrections, escalations.

### What this stage answers

The stage answers the question: **What was decided, what is its evidence, and what has happened to it since?** The decision record is the unit of accountability.

## The five integrity questions

The model gives rise to five integrity questions, each tied to one stage. The integrity questions become normative requirements in `source-engineering-specifications`. They are:

- **Source integrity** (Stage 1). Can each source and relevant version be identified and retrieved?
- **Contextual integrity** (Stage 2). Was the evidence valid for the decision's scope and point in time?
- **Evidential integrity** (Stage 3). Can each material claim be traced to supporting or contradicting evidence?
- **Decision integrity** (Stage 4). Can a reviewer reconstruct the declared basis for the decision and identify unresolved gaps?
- **Lifecycle integrity** (Stage 5). Can subsequent source changes, corrections, and validation failures be traced to affected decisions?

The integrity questions are derived from the founding essay's third principle ("judgment is separate from evidence") and the 2026 reframing's identification of the judgment gap. See `integrity-questions.md` for the full prose.

## What the model is not

- **Not a workflow.** The five stages are not phases in time. A decision record carries all five stages as inspectable artefacts. The discipline does not require the stages to happen sequentially at runtime.
- **Not a chain-of-thought.** The model is not the model's internal reasoning; it is what the discipline records about the model's interaction with inspectable artefacts.
- **Not a complete ontology.** The model names what the discipline needs to be inspectable. New entities may be added as the discipline grows; deprecated entities are marked as such but retained for one release.
- **Not a critique of any model.** The model is the discipline's response surface. The model does not say which model to use; the model says how to make the model's output inspectable.

## Related documents

- `entities.md` -- the data model. The minimum set of entities and their relationships, in a form that maps to JSON or to a typed schema.
- `integrity-questions.md` -- the five integrity questions in full prose.
- `Source-Engineering/source-engineering-foundations/docs/00-manifesto.md` -- the manifesto's three principles the model implements.
- `Source-Engineering/source-engineering-research/agenda.md` -- the discipline's open questions, including the foundational questions Q-F.1, Q-F.2, Q-F.3 that the model addresses.
- `Source-Engineering/source-engineering-architecture` -- the four-layer stack that implements this model.