# Source Engineering -- Model

> The canonical conceptual and data model. What the discipline thinks in, what the architecture composes from, what the specifications specify against.

This repository holds the discipline's formal model. It defines the concepts the discipline reasons about (Source Universe, Evidence Construction, Decision Basis, Decision Record) and the relationships between them. The model is the bridge between the foundations repo (which says *what the discipline is*) and the specifications + architecture repos (which say *what the engineering contracts are*).

## What is in this repository

- `model.md` -- the canonical conceptual model. Five named stages, each with named entities, named relationships, and named questions the stage answers.
- `entities.md` -- the data model. The minimum set of entities and their relationships, expressed in a way that maps to JSON or to a typed schema.
- `integrity-questions.md` -- the five central integrity questions that drive the specifications. Each integrity question names a property the system must satisfy and the failure mode it prevents.

## How to engage

- **Read the model first.** Start at `model.md`. The model is the discipline's vocabulary; everything else in the polyrepo points back at it.
- **Then the entities.** `entities.md` is the data model. It is normative for the specifications repo.
- **Then the integrity questions.** `integrity-questions.md` is the bridge between the model and the specifications. Each integrity question becomes a normative requirement in `source-engineering-specifications`.

## Provenance

The conceptual model was first stated in `Source-Engineering/source-engineering-research/agenda.md` and elaborated in this repository. The five-stage model is informed by the discipline's founding essays and the canonical model established in the polyrepo consolidation.

## Organisation

The discipline is published as a set of repositories under the [Source-Engineering](https://github.com/Source-Engineering) GitHub organisation. One authoritative home per artefact:

| Repo | Owns |
|---|---|
| [Source-Engineering/.github](https://github.com/Source-Engineering/.github) | Org profile README |
| [Source-Engineering/project-governance](https://github.com/Source-Engineering/project-governance) | Project-level governance for the governance home |
| [Source-Engineering/source-engineering-governance](https://github.com/Source-Engineering/source-engineering-governance) | Org-wide discipline governance |
| [Source-Engineering/source-engineering-foundations](https://github.com/Source-Engineering/source-engineering-foundations) | Discipline definition, principles, terminology |
| [Source-Engineering/source-engineering-research](https://github.com/Source-Engineering/source-engineering-research) | Research agenda, experimental protocols |
| [Source-Engineering/source-engineering-model](https://github.com/Source-Engineering/source-engineering-model) (this repo) | Canonical conceptual and data model |
| [Source-Engineering/source-engineering-specifications](https://github.com/Source-Engineering/source-engineering-specifications) | Normative technical requirements; COTS spec |
| [Source-Engineering/source-engineering-architecture](https://github.com/Source-Engineering/source-engineering-architecture) | Reference architecture, blueprint stack |
| [Source-Engineering/source-engineering-reference-implementation](https://github.com/Source-Engineering/source-engineering-reference-implementation) | Working reference code |
| [Source-Engineering/source-engineering-conformance](https://github.com/Source-Engineering/source-engineering-conformance) | Conformance test suite |
| [Source-Engineering/source-engineering-tools](https://github.com/Source-Engineering/source-engineering-tools) | Reusable software utilities |
| [Source-Engineering/source-engineering-case-studies](https://github.com/Source-Engineering/source-engineering-case-studies) | Evaluated demonstrations |
| [Source-Engineering/source-engineering-practice](https://github.com/Source-Engineering/source-engineering-practice) | Practitioner guidance |
| [Source-Engineering/source-engineering-education](https://github.com/Source-Engineering/source-engineering-education) | Curriculum and learning resources |

## Status

This is the model slice (Phase 2). The conceptual model and the data model are normative for the specifications repo. The integrity questions drive the specifications and the conformance suite.

## License

Apache-2.0. See [LICENSE](LICENSE).