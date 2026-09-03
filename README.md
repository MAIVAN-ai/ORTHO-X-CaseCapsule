# ORTHO-X Case Capsule

**Persistent, provenance-aware longitudinal clinical memory for ORTHO-X OrthoFlow and OrthoSkills.**

ORTHO-X Case Capsule adapts the persistent LLM-maintained knowledge architecture implemented in
[`nanzhipro/Karpathy-llm-wiki-bootstrap-skill`](https://github.com/nanzhipro/Karpathy-llm-wiki-bootstrap-skill)
to longitudinal orthopaedic care.

A Case Capsule continuously compiles heterogeneous clinical evidence — referral information, imaging,
clinical findings, AI-assisted reasoning, treatment decisions, surgical events, postoperative events,
rehabilitation, functional measures and patient-reported outcomes — into a durable, inspectable and
agent-readable representation of a patient's journey.

It is designed to provide a common longitudinal memory and evidence contract on which **OrthoSkills**
operate across **ORTHO-X OrthoFlow**.

> [!IMPORTANT]
> **Research and development prototype. Not a medical device. Not for autonomous clinical decision-making.**
> Clinical outputs must be reviewed and confirmed by appropriately qualified healthcare professionals.

> [!IMPORTANT]
> **The Case Capsule is not intended to replace an EHR, PACS/VNA, FHIR server, registry or other
> authoritative clinical system.** Original clinical evidence remains hospital-controlled in its
> authoritative source system. A Case Capsule stores references, hashes, metadata, derived assertions,
> longitudinal interpretation, provenance and confirmation state.

---

## Why Case Capsules?

Most clinical AI workflows are effectively stateless:

```text
Clinical systems
      ↓
Retrieve documents
      ↓
Prompt a model
      ↓
Generate an answer
      ↓
Lose most intermediate structure
```

ORTHO-X Case Capsule introduces persistent clinical memory:

```text
Clinical evidence
      ↓
INGEST
      ↓
STRUCTURE
      ↓
REASON
      ↓
ASSERT
      ↓
CITE
      ↓
CONFIRM
      ↓
RETAIN
      ↓
UPDATE
      ↓
MEASURE OUTCOMES
```

The result is not merely a chat history, vector database or one-off summary. It is a longitudinal
clinical knowledge artifact that evolves with the patient's journey while preserving the distinction
between source evidence, machine-derived assertions and clinician-confirmed facts.

---

## Position in ORTHO-X

```text
EHR · FHIR · PACS/VNA · Imaging · Video · Labs · PROMs · Rehab
                              │
                              ▼
                    ORTHO-X CASE CAPSULE
                  longitudinal evidence memory
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
      OrthoSkills         SurgiCorder       Human clinicians
          │                   │                   │
          └───────────────────┼───────────────────┘
                              │
                              ▼
                 Outcome-attributed evidence
                              │
              ┌───────────────┼───────────────┐
              │               │               │
             PMCF            RWE             VBHC
                              │
                              ▼
              Quality improvement / learning
```

### Architectural roles

| Component | Primary role |
| --- | --- |
| **Authoritative clinical systems** | Original clinical source data and legal/operational record |
| **ORTHO-X Case Capsule** | Persistent longitudinal evidence, assertions, provenance and outcome linkage |
| **OrthoSkills** | Model-independent executable domain reasoning and workflow contracts |
| **ORTHO-X OrthoFlow** | Patient-pathway and agent orchestration |
| **SurgiCorder** | Intraoperative evidence capture and surgical execution intelligence |
| **Outcome layer** | Clinical, functional and patient-centred outcomes |
| **Data Commons / federated layer** | Permissioned multi-case evidence, PMCF, RWE and learning |

---

## Core design principles

### 1. Source evidence is authoritative

Clinical source material is never silently rewritten by an agent.

The Case Capsule references or stores immutable evidence objects and preserves their provenance.

Examples:

- FHIR resources
- DICOM studies and series
- radiology reports
- operative notes
- surgical video manifests
- laboratory results
- implant/device information
- physiotherapy assessments
- functional measurements
- PROMs and ICHOM-aligned outcomes

### 2. Assertions are separate from evidence

An AI-generated diagnosis, classification or recommendation is an **assertion**, not source evidence.

Every assertion should identify:

- the assertion itself
- type and terminology system
- originating OrthoSkill
- Skill version
- model/runtime
- source evidence references
- timestamp
- confidence
- human-confirmation state
- supersession state

### 3. History is not overwritten

When new evidence changes an interpretation, the previous assertion remains in the provenance chain.

Example:

```text
v1
AO/OTA 31-A2.2
source: AP/lateral radiographs
confidence: 0.87
status: superseded

        ↓ CT added

v2
AO/OTA 31-A2.3
source: radiographs + CT
confidence: 0.94
status: clinician-confirmed
supersedes: v1
```

### 4. Human confirmation is first-class data

Human review is not a UI afterthought. It is represented explicitly in the Case Capsule.

A confirmed assertion records who confirmed it, when, in what role and against which evidence state.

### 5. Outcomes close the loop

ORTHO-X Case Capsules are designed to connect:

```text
Patient intent
   ↓
Baseline
   ↓
Diagnosis
   ↓
Clinical reasoning
   ↓
Treatment decision
   ↓
Intervention / surgical execution
   ↓
Postoperative events
   ↓
Rehabilitation
   ↓
Function
   ↓
Patient-centred outcomes
   ↓
Outcome attribution
```

This enables clinical improvement, benchmarking, PMCF, RWE and value-based healthcare.

### 6. Models are replaceable

The Case Capsule is deliberately model-independent.

Claude, ChatGPT/Codex, Gemini, hospital-local models or future clinical agents should be able to
operate against the same durable Case Capsule.

**Models are replaceable.  
Clinical evidence is authoritative.  
OrthoSkills encode domain behaviour.  
Case Capsules preserve longitudinal memory and provenance.**

---

## Proposed repository structure

This first commit adds the ORTHO-X specification layer without forcing a destructive rename of the
upstream reference implementation.

```text
ORTHO-X-CaseCapsule/
├── README.md
├── NOTICE.md
├── docs/
│   ├── CASE-CAPSULE-ARCHITECTURE.md
│   ├── CLINICAL-PROVENANCE.md
│   └── ORTHOSKILLS-CONTRACT.md
├── schemas/
│   ├── case-capsule.schema.json
│   ├── clinical-assertion.schema.json
│   ├── provenance-event.schema.json
│   └── outcome-observation.schema.json
│
├── skill/                         # inherited upstream skill implementation
├── llm-wiki/                      # inherited upstream reference implementation
├── karpathy-llm-wiki-original.md  # inherited upstream source/reference
└── ...
```

A later ORTHO-X-specific implementation may evolve toward:

```text
case-capsule/
├── CASE-CAPSULE-SCHEMA.md
├── manifest.json
├── raw/
├── clinical/
├── skills/
├── outcomes/
├── provenance/
└── capsule/
```

---

## OrthoSkills interaction contract

Every OrthoSkill operating on a Case Capsule should follow this conceptual cycle:

```text
READ → REASON → ASSERT → CITE → CONFIRM → WRITE
```

An OrthoSkill:

1. reads only the minimum relevant Case Capsule state and authoritative references;
2. distinguishes evidence from prior interpretations;
3. reasons according to its Skill definition;
4. emits one or more structured assertions;
5. cites supporting source evidence;
6. declares uncertainty and missing information;
7. requests human confirmation when clinically required;
8. writes only through the Case Capsule contract;
9. never deletes contradictory or superseded history.

See [`docs/ORTHOSKILLS-CONTRACT.md`](docs/ORTHOSKILLS-CONTRACT.md).

---

## Capsule integrity checks

The upstream LLM Wiki pattern includes linting for contradictions, stale claims, missing links and
structural drift. In ORTHO-X this becomes a **Case Capsule Integrity Check**.

Examples include:

- contradictory active diagnoses
- multiple active classifications for the same target without reconciliation
- assertions without evidence
- missing or invalid provenance
- unconfirmed high-impact assertions
- superseded assertions still marked current
- missing baseline outcome measures
- missed follow-up/outcome windows
- unexplained recovery-trajectory deviation
- missing human confirmation where required
- source object hash mismatch
- broken source-system references

Integrity checks detect issues; they do not autonomously resolve clinically meaningful conflicts.

---

## Schema status

The JSON Schemas in `/schemas` are **v0.1 interface contracts**, not final interoperability standards.

They are intended to make the architecture testable while allowing future mappings to:

- HL7 FHIR
- DICOM / DICOMweb
- SNOMED CT
- LOINC
- ICD
- AO/OTA and other specialty classifications where licensed/permitted
- ICHOM outcome sets
- PROMIS and other outcome instruments
- device/implant identifiers
- PMCF/RWE export profiles

Schema changes should remain backward-aware and explicitly versioned.

---

## Data governance

This repository grants no rights to patient case data, clinical images, surgical videos, hospital
records, registry data or derived clinical evidence.

Deployment with real clinical data requires an appropriate legal basis and governance, including as
applicable:

- hospital authorization
- data-processing agreements
- patient consent or other lawful basis
- role-based access controls
- audit logging
- data minimization
- retention policies
- cross-border data-transfer controls
- security and privacy review

Case Capsules should support hospital-controlled and federated deployment patterns rather than require
central export of identifiable clinical data.

---

## Origin and attribution

This project is derived from the architectural pattern and code contained in
[`nanzhipro/Karpathy-llm-wiki-bootstrap-skill`](https://github.com/nanzhipro/Karpathy-llm-wiki-bootstrap-skill),
which operationalises Andrej Karpathy's LLM Wiki concept: durable, LLM-maintained knowledge artifacts
that improve through repeated ingest, query and lint cycles rather than reconstructing all useful
structure at query time.

ORTHO-X Case Capsule adapts that idea to longitudinal clinical care by adding:

- authoritative source-system separation
- clinical assertions
- explicit provenance
- human confirmation
- supersession and reconciliation
- treatment/intervention linkage
- postoperative events
- rehabilitation
- functional outcomes
- patient-centred outcomes
- PMCF/RWE-oriented evidence structures

See [`NOTICE.md`](NOTICE.md).

---

## Project status

**v0.1 — architecture/bootstrap stage**

Near-term goals:

1. define the Case Capsule and assertion contracts;
2. adapt the upstream bootstrap/ingest/query/lint workflow;
3. implement Case Capsule Integrity Checks;
4. connect OrthoSkills read/write operations to the schemas;
5. create a synthetic proximal-femur-fracture reference capsule;
6. test model-independent execution across at least two agent runtimes;
7. map selected objects to FHIR/DICOM and ICHOM-aligned outcomes.

---

## Maintainer

**MAIVAN.ai / ORTHO-X**  
MAIVAN.ai PBC by Bluenaut Matching Services AG  
Kilchberg, Zurich, Switzerland

Repository: `MAIVAN-ai/ORTHO-X-CaseCapsule`

---

## Safety

This software and documentation are intended for research, development, education and supervised
clinical workflow design. They do not constitute medical advice and must not be used as a substitute
for clinical judgment.

No autonomous diagnostic or treatment decision should be made solely from a Case Capsule or an
OrthoSkill output.
