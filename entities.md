# Source Engineering -- Data Model

> The minimum set of entities and their relationships. Maps to JSON or a typed schema.

This document is the canonical data model. It names every entity the discipline records, every intermediate attribute the entity carries, and every relationship between entities. The data model is normative for the specifications repo: every JSON schema, every typed schema, every database table that claims to be Source Engineering conformant must include these entities and these relationships.

The data model is the projection of the conceptual model onto something a system can store and exchange. The conceptual model is normative for what the entities mean; the data model is normative for what the entities look like.

## Entity overview

```
+--------------------+
| SourceArtifact     |--+
|                    |  |
+--------------------+  |
                       |
                       | 1..n
                       v
+--------------------+         +--------------------+
| Version            |-------->| Qualification      |
|                    |         |                    |
+--------------------+         +--------------------+
                                          |
                                          | 1..n
                                          v
+--------------------+         +--------------------+
| Context            |-------->| Constraint         |
|                    |         |                    |
+--------------------+         +--------------------+

+--------------------+         +--------------------+
| Claim              |-------->| EvidenceItem       |
|                    |         |                    |
+--------------------+         +--------------------+
                                          |
                                          |
                                          v
+--------------------+         +--------------------+
| Uncertainty        |         | Provenance         |
|                    |         |                    |
+--------------------+         +--------------------+

+--------------------+         +--------------------+
| DecisionBasis      |-------->| Contradiction      |
|                    |         |                    |
+--------------------+         +--------------------+

+--------------------+         +--------------------+
| DecisionRecord     |-------->| AuditEntry         |
|                    |                    |
+--------------------+         +--------------------+
```

## Entities

### SourceArtifact

A discrete piece of content that can be cited.

```
SourceArtifact {
  id:                  URI (unique within the discipline)
  origin:              Origin
  version:             Version
  authority:           Authority
  temporal_validity:   TemporalValidity
  content_reference:   ContentReference
}
```

#### Origin

The system that produced the source artifact.

```
Origin {
  system_name:         string
  system_version:      string
  configuration_hash:  string
}
```

#### Version

A specific state of the source artifact. Versioning is addressed via content hash, not via numeric version. A source without a content hash is not addressable.

```
Version {
  content_hash:        string (SHA-256 or equivalent)
  issued_at:           ISO-8601 timestamp
}
```

#### Authority

The provenance authority of the source artifact. Authority is a statement from the model operator, not an assessment by the discipline.

```
Authority {
  asserted_by:        string (the model operator's identifier)
  trust_score:         number (the operator's trust score, on a calibrated scale)
}
```

#### TemporalValidity

The window of time during which the source artifact is considered valid.

```
TemporalValidity {
  valid_from:          ISO-8601 timestamp
  valid_until:         ISO-8601 timestamp | null (null means open-ended)
}
```

### Qualification

The model's assessment of a source's fitness for the decision.

```
Qualification {
  id:                  URI
  source_id:           URI (the SourceArtifact)
  qualification_text:  string (in domain terms)
  context_id:          URI (the Context under which the qualification was made)
}
```

### Context

The runtime parameters in effect at the call.

```
Context {
  id:                  URI
  model:               string (model identifier)
  model_version:       string
  parameters:          Parameters
  constraints:         Constraint[]
}
```

#### Parameters

The runtime parameters. All parameters are first-class data points back to the user.

```
Parameters {
  temperature:         number
  top_p:               number
  prompt_cache_id:     string | null
  tool_budget:         number
  retrieval_depth:     number
  additional:          map<string, string | number | boolean>  (extensible)
}
```

#### Constraint

A constraint on the source imposed by the context.

```
Constraint {
  id:                  URI
  type:                string (URI from a controlled vocabulary)
  scope:               string (what the constraint applies to)
  value:               string (the constraint's value)
}
```

### Claim

A material assertion in the response.

```
Claim {
  id:                  URI
  decision_record_id:  URI (the DecisionRecord the Claim belongs to)
  claim_text:          string (in domain terms)
  claim_type:          enum (factual, normative, predictive, evaluative)
  supporting_evidence: URI[]  (EvidenceItem ids)
  contradicting_evidence:   URI[]  (EvidenceItem ids)
}
```

### EvidenceItem

A discrete unit of evidence supporting, contradicting, or contextualising a claim.

```
EvidenceItem {
  id:                  URI
  claim_id:            URI (the Claim this EvidenceItem addresses)
  source_id:           URI (the SourceArtifact the evidence derives from)
  transformation:      Transformation
  uncertainty:         Uncertainty
  polarity:            enum (supports, contradicts, contextualises)
}
```

#### Transformation

The operation the model performed on the source to produce the evidence.

```
Transformation {
  operation:           string (a named operation from a controlled vocabulary)
  inputs:              string[] (the inputs to the transformation)
  output:              string (the output of the transformation)
  recorded_at:         ISO-8601 timestamp
}
```

#### Uncertainty

The model's stated uncertainty about the evidence.

```
Uncertainty {
  calibrated_score:    number (on a calibrated scale, e.g. 0.0 to 1.0)
  basis:               string (in domain terms; what makes this uncertain)
}
```

### Provenance

The chain that links an evidence item back to its source.

```
Provenance {
  evidence_item_id:    URI (the EvidenceItem)
  source_id:           URI (the SourceArtifact)
  chain:               Transformation[] (the chain of transformations from source to evidence)
}
```

### DecisionBasis

The aggregation of evidence into a decision.

```
DecisionBasis {
  id:                  URI
  decision_record_id:  URI
  supporting_evidence: URI[]  (EvidenceItem ids)
  counterevidence:     URI[]  (EvidenceItem ids)
  constraints:         Constraint[]
  known_gaps:          string[]  (in domain terms)
  rationale:           string (the decision's rationale)
}
```

### DecisionRecord

The persistent artefact that captures the decision.

```
DecisionRecord {
  id:                  URI (unique within the discipline)
  decision_text:       string (the decision itself)
  basis_id:            URI (the DecisionBasis)
  evidence_chain_ref:  URI (the Provenance chain)
  validation_status:    ValidationStatus
  responsible_authority: ResponsibleAuthority
  audit_history:       AuditEntry[]
  created_at:          ISO-8601 timestamp
}
```

#### ValidationStatus

The state of the decision's validation.

```
ValidationStatus {
  status:              enum (pending, validated, contested, appealed, superseded)
  last_changed_at:     ISO-8601 timestamp
  last_changed_by:     string (the authority that changed the status)
}
```

#### ResponsibleAuthority

The named authority that owns the decision.

```
ResponsibleAuthority {
  authority_id:        string (the authority's identifier)
  authority_type:      enum (human, organisational, automated)
}
```

### AuditEntry

An append-only log entry for the decision record.

```
AuditEntry {
  id:                  URI
  decision_record_id:  URI
  action:              enum (created, appealed, rerun, corrected, escalated, superseded)
  performed_by:        string
  performed_at:        ISO-8601 timestamp
  notes:               string
}
```

## What the data model is not

- **Not a database schema.** The data model is the projection of the conceptual model; it can be expressed as JSON, as a typed schema, or as a database schema. The database schema is implementation-specific.
- **Not a wire format.** The data model is the entity and relationship set; the wire format is the serialisation (JSON, YAML, Protobuf) and the version negotiation.
- **Not a UI schema.** The data model is what the discipline records; the UI schema is how a human reads it.

## What is normative

- The **entities** and their **attributes** are normative.
- The **relationships** between entities are normative.
- The **polarity enum** in `EvidenceItem` is normative (the discipline does not permit evidence to be neither supports nor contradicts nor contextualises).
- The **claim_type enum** is normative.
- The **ValidationStatus** values are normative.
- The **action** values in `AuditEntry` are normative.

What is not normative:

- The **type** of `id` fields (the discipline requires uniqueness but does not require URIs).
- The **format** of timestamps (ISO-8601 is recommended but not required).
- The **scale** of `calibrated_score` (a calibrated scale is required; the specific scale is implementation-specific).

## Related documents

- `model.md` -- the conceptual model.
- `integrity-questions.md` -- the integrity questions the data model is designed to support.
- `Source-Engineering/source-engineering-specifications` -- the JSON / YAML / TOML schema (forthcoming, this repo's headline sub-artefact).
- `Source-Engineering/source-engineering-architecture` -- the four-layer stack that implements this data model.