# ORTHO-X OrthoFlow + OrthoSkills + Case Capsule  
## Clinical User & Hospital IT Manual

**Version:** 0.1 — Development / Pilot Edition  
**Audience:** Orthopaedic surgeons, residents, physiotherapists, nurses, outcome teams, clinical informatics teams, PACS/EHR administrators, hospital IT, information security and clinical AI teams  
**Status:** Research and supervised pilot use. Not for autonomous clinical decision-making.

---

# 1. What ORTHO-X is

ORTHO-X is a vendor-neutral orthopaedic intelligence layer designed to accompany a patient longitudinally rather than answer isolated clinical questions.

Its core components are:

| Component | Function |
|---|---|
| **OrthoFlow** | Orchestrates the patient journey and determines which information, Skill or follow-up step is needed next |
| **OrthoSkills** | Domain-specific clinical reasoning workflows such as case intake, image-quality assessment, AO/OTA classification, differential reasoning, treatment mapping, outcomes and rehabilitation |
| **Case Capsule** | Persistent longitudinal clinical memory containing evidence references, structured assertions, clinician confirmations, interventions, events and outcomes |
| **MCP Case Capsule Service** | Allows AI agents and clinical applications to read and write Case Capsules using standardized tools |
| **FHIR Adapter** | Connects Case Capsules to authorized EHR/FHIR resources |
| **DICOMweb Adapter** | Connects Case Capsules to imaging studies in PACS/VNA |
| **SurgiCorder** | Optional intraoperative evidence and surgical execution layer |

The central idea is simple:

> **The AI model may change. The Case Capsule remains.**

A patient's radiographs, clinical observations, classification, treatment decision, operation, postoperative events, rehabilitation and outcomes are linked together in one inspectable longitudinal record.

---

# 2. What the system is — and is not

ORTHO-X is intended to help clinicians:

- structure clinical information;
- retrieve relevant evidence;
- apply orthopaedic reasoning workflows;
- record classifications and differential diagnoses;
- preserve the evidence behind AI-generated assertions;
- explicitly record clinician confirmation;
- link treatment and surgical events;
- follow postoperative recovery;
- capture functional and patient-centred outcomes;
- create higher-quality longitudinal data for quality improvement, PMCF, RWE and VBHC.

ORTHO-X is **not** intended to:

- replace the hospital EHR;
- replace PACS;
- make autonomous diagnoses;
- independently decide treatment;
- impersonate a clinician;
- silently change a previous clinical conclusion;
- treat model confidence as medical truth.

The authoritative source systems remain the hospital systems.

The Case Capsule records what evidence existed, what interpretation was made, by whom or by which Skill, whether it was confirmed, what subsequently happened, and what outcome followed.

---

# 3. The clinician's mental model

Think of the Case Capsule as the patient's **orthopaedic flight recorder**.

Instead of this:

```text
Open EHR
→ find notes
→ find X-rays
→ ask AI
→ receive answer
→ close chat
→ repeat next week
```

ORTHO-X works like this:

```text
Referral
   ↓
Case Capsule created
   ↓
Evidence registered
   ↓
OrthoSkill reasons
   ↓
Structured assertion
   ↓
Surgeon confirms / corrects
   ↓
Treatment recorded
   ↓
Surgery recorded
   ↓
Post-op events recorded
   ↓
Rehabilitation tracked
   ↓
Patient outcomes measured
```

Nothing clinically important should disappear simply because a later interpretation changes.

For example:

```text
Initial X-ray
→ AO/OTA 31-A2.2
→ confidence 0.87
→ pending confirmation

CT added
→ revised AO/OTA 31-A2.3
→ confidence 0.94
→ surgeon confirms

Result:
31-A2.2 remains visible as superseded history.
31-A2.3 becomes the current confirmed classification.
```

---

# 4. What does the user interface look like?

## 4.1 Current development interface

The current ORTHO-X Case Capsule implementation is **service-first rather than GUI-first**.

There is not yet a single proprietary ORTHO-X desktop window.

Clinicians may interact with the system through an authorized AI host such as:

- ChatGPT Work / Codex;
- Claude / Cowork;
- OrthoClaw;
- a hospital-local agent;
- a future ORTHO-X OrthoFlow web interface.

The AI host presents a normal conversational user interface.

Behind that interface, it can call tools such as:

```text
casecapsule.current_state
casecapsule.register_evidence
casecapsule.register_fhir_resource
casecapsule.register_dicom_study
casecapsule.write_assertion
casecapsule.write_classification
casecapsule.confirm
casecapsule.reject
casecapsule.record_surgery
casecapsule.record_outcome
casecapsule.integrity_check
```

The clinician normally should **not need to type these tool names manually**.

Instead, the surgeon can say:

> Open the Case Capsule for this patient and summarize the current orthopaedic state.

or:

> Classify the proximal femur fracture using the AO/OTA Skill.

or:

> I agree with 31-A2.3. Confirm the classification.

The agent then calls the required Case Capsule tools.

---

# 5. The intended future OrthoFlow clinical UI

A dedicated ORTHO-X clinical interface can present the same backend as a longitudinal dashboard.

A typical screen should contain five regions.

## A. Patient / Case Header

Displays:

- pseudonymous or hospital-authorized patient identity;
- age;
- sex;
- affected side;
- diagnosis;
- admission / encounter;
- Case Capsule ID;
- current care phase.

Example:

```text
RIGHT PROXIMAL FEMUR FRACTURE

78 F
Case Capsule: cc-...
Current phase: Postoperative rehabilitation
Confirmed classification: AO/OTA 31-A2.3
```

---

## B. Longitudinal Timeline

A chronological timeline shows:

```text
Referral
Imaging
Classification
Treatment decision
Operation
Post-op wound event
Discharge
6-week follow-up
3-month outcome
```

Each item can be expanded to show:

- source evidence;
- AI interpretation;
- clinician confirmation;
- timestamp;
- Skill/model provenance.

---

## C. Current Clinical State

This is a concise projection of the current accepted state.

Example:

```text
Diagnosis
Right pertrochanteric proximal femur fracture

Classification
AO/OTA 31-A2.3
Clinician-confirmed

Treatment
Cephalomedullary nail fixation

Surgical-site outcome
Small seroma
No dehiscence
No documented infection
No haematoma

Mobility
Single walking aid outdoors

Pain
1/10 at 3 months

Recovery trajectory
Improving; residual limitation in longer community ambulation
```

The current-state view is **derived** from the Case Capsule.

It does not erase history.

---

## D. Evidence Panel

Shows the evidence supporting the current clinical state.

Examples:

- AP pelvis;
- lateral hip;
- CT;
- operative report;
- wound review;
- TUG result;
- PROM;
- physiotherapy report.

The source data should normally remain in the authoritative hospital system.

ORTHO-X stores or retrieves its reference.

---

## E. Actions / Skills

Depending on the care phase:

```text
Run Image Quality Check
Run AO/OTA Classification
Run Differential Reasoning
Review Treatment Options
Record Procedure
Record Surgical Site Outcome
Record Rehabilitation Status
Capture Outcome
Run Integrity Check
```

---

# 6. Starting a patient Case Capsule

A Case Capsule should normally be created when a patient enters an ORTHO-X-enabled pathway.

Examples:

- fracture clinic referral;
- ED orthopaedic consult;
- planned arthroplasty;
- postoperative complication;
- second opinion;
- rehabilitation episode.

The user might say:

> Create a Case Capsule for this proximal femur fracture patient.

OrthoFlow should then create a new Case Capsule.

Technically, the service performs:

```text
casecapsule.bootstrap
```

The resulting structure includes:

```text
manifest
evidence references
clinical assertions
outcomes
provenance
current-state projection
```

---

# 7. Workflow 1 — Referral and clinical intake

## Surgeon / medical-staff view

Example user request:

> Ingest the referral and structure the initial orthopaedic history.

The system may extract or request:

- mechanism of injury;
- affected side;
- symptoms;
- mobility;
- neurovascular status;
- pre-injury function;
- relevant comorbidities;
- red flags;
- patient goals.

The referral remains source evidence.

The structured interpretation becomes a Case Intake assertion.

These are deliberately separate.

## Example

Evidence:

```text
Referral note:
78-year-old woman
Fall from standing
Right hip pain
Unable to bear weight
Previously independently mobile
```

Structured assertion:

```text
Mechanism: low-energy fall
Side: right
Pre-injury mobility: independent
Current mobility: unable to bear weight
Patient goal: return to independent walking
```

---

# 8. Workflow 2 — Imaging

The surgeon can say:

> Register the AP pelvis and lateral hip study.

If PACS supports DICOMweb, ORTHO-X can register the Study Instance UID through:

```text
casecapsule.register_dicom_study
```

The Case Capsule records the Study reference.

It does not need to duplicate the entire DICOM study.

Conceptually:

```text
PACS/VNA
  │
  └── Study Instance UID
            │
            ▼
        Case Capsule
       ev-xray-001
```

When an authorized imaging Skill requires the actual images, it can retrieve them from PACS.

---

# 9. Workflow 3 — Image Quality Check

Before classification, the Image Quality Skill can examine whether the images are adequate.

Typical questions:

- AP view acceptable?
- lateral acceptable?
- rotation?
- full relevant anatomy?
- severe artefact?
- additional views needed?
- CT helpful?

The Skill should be able to return:

```text
Image quality: adequate for initial classification

Limitations:
Posteromedial comminution incompletely characterized

Recommendation:
Additional imaging may refine subgroup classification
```

This becomes another structured assertion.

---

# 10. Workflow 4 — AO/OTA classification

The surgeon can ask:

> Run the AO/OTA Classification Skill.

The reasoning is performed by the AO/OTA OrthoSkill.

The Case Capsule persistence layer does not independently classify the fracture.

An output might be:

```text
Proposed classification:
AO/OTA 31-A2.2

Confidence:
0.87

Evidence:
AP pelvis
lateral hip

Status:
Pending surgeon confirmation
```

The surgeon has several options.

## Confirm

> Confirm 31-A2.2.

The system records:

```text
status = confirmed
confirmed_by = authenticated surgeon
timestamp = ...
```

## Correct

> I think this is 31-A2.3 because of the posteromedial fragmentation.

The preferred workflow is:

```text
old assertion retained
      ↓
new assertion created
      ↓
new assertion supersedes old
      ↓
surgeon confirms new assertion
```

## Reject

> Reject this classification.

The assertion stays in the provenance record but is marked rejected.

---

# 11. Workflow 5 — New evidence changes the diagnosis

Suppose CT arrives later.

The surgeon says:

> Register the CT and rerun the classification.

The new Skill result may be:

```text
31-A2.3
confidence 0.94
```

ORTHO-X should show:

```text
CURRENT
31-A2.3
confirmed

HISTORY
31-A2.2
superseded
```

This is a fundamental ORTHO-X principle:

> **Clinical reasoning evolves, but the history of reasoning remains auditable.**

---

# 12. Workflow 6 — Treatment mapping

The surgeon can ask:

> Map this fracture to reasonable treatment options.

The Treatment Mapping Skill may consider:

- fracture morphology;
- patient physiology;
- functional baseline;
- relevant evidence;
- contraindications;
- institutional context.

The output is not automatically a treatment order.

It is an educational / decision-support assertion.

Example:

```text
Selected pathway after surgeon review:
Operative fixation

Selected generic device class:
Cephalomedullary nail

Status:
Clinician-confirmed
```

The final decision remains the surgeon's.

---

# 13. Workflow 7 — Surgery

After surgery, ORTHO-X can register the operative record.

The user might say:

> Record today's cephalomedullary nail fixation.

The Case Capsule may store:

```text
Procedure
Closed reduction and cephalomedullary nail fixation

Date/time
...

Device class
Cephalomedullary nail

Intraoperative events
No complication documented in operative record

Postoperative plan
Weight bearing as tolerated
```

The authoritative operative note remains in the EHR.

The Case Capsule retains its structured longitudinal interpretation and source reference.

---

# 14. Workflow 8 — SurgiCorder

If SurgiCorder is available, the operative episode can be enriched with intraoperative evidence.

Examples:

- procedure phase;
- guidewire placement;
- implant positioning;
- fluoroscopic evidence;
- surgical video;
- instrument usage;
- operative timing;
- execution events.

The Case Capsule can therefore connect:

```text
Preoperative reasoning
      ↓
Treatment decision
      ↓
Actual surgical execution
      ↓
Postoperative outcome
```

This is important for future surgical intelligence and outcome attribution.

---

# 15. Workflow 9 — Surgical-site outcomes / DISH

During postoperative review, the user can ask:

> Record the surgical-site outcome.

An ORTHO-X DISH-oriented Skill can record the four domains:

```text
Dehiscence
Infection
Seroma
Haematoma
```

Example:

```text
Dehiscence: not observed
Infection: not observed
Seroma: small, observed
Haematoma: not observed

Management:
Conservative observation
```

Observation and causality should remain separate.

The presence of a seroma does **not** automatically mean:

```text
caused by implant
```

or:

```text
caused by operative technique
```

Causal attribution requires a separate assertion and stronger evidentiary review.

---

# 16. Workflow 10 — Rehabilitation

The rehabilitation team can contribute to the same Case Capsule.

A physiotherapist might say:

> Record today's six-week mobility assessment.

Example:

```text
Outdoor mobility:
single walking aid

Indoor mobility:
short distances without aid

Therapy:
ongoing

Pain:
3/10

TUG:
21.4 seconds
```

This means the Case Capsule continues beyond surgery.

It becomes a longitudinal mobility record.

---

# 17. Workflow 11 — Outcomes

The system can capture:

- PROMs;
- ClinROMs;
- performance measures;
- functional measures;
- complications;
- return to work;
- return to sport;
- autonomy;
- pain;
- global health.

Example:

```text
Baseline
Pain 8/10
Unable to perform TUG because of acute injury

6 weeks
Pain 3/10
TUG 21.4 seconds

3 months
Pain 1/10
TUG 14.8 seconds
Independent basic activities of daily living
Residual limitation in longer outdoor walking
```

OrthoFlow can then compare the trajectory with:

- baseline;
- expected recovery;
- previous visits;
- patient goals.

---

# 18. ICHOM-aligned outcome measurement

Where configured, ORTHO-X can organize outcomes around ICHOM-aligned domains.

Examples include:

- pain;
- physical functioning;
- ambulatory function;
- autonomy / activities of daily living;
- global health;
- quality of life.

Hospitals implementing formal ICHOM Standard Sets should use the official current definitions, instruments, coding and timepoints applicable to their implementation.

ORTHO-X should not invent proprietary substitutions for an official measure.

---

# 19. Workflow 12 — Recovery trajectory

A surgeon can ask:

> How is this patient recovering compared with baseline?

OrthoFlow may answer:

```text
Substantial improvement since surgery.

Pain:
8 → 3 → 1

TUG:
unable acutely → 21.4 s → 14.8 s

ADL:
independent basic ADL

Residual limitation:
longer community ambulation

Proposed trajectory assessment:
on track with residual deficit

Status:
pending clinician confirmation
```

The trajectory interpretation is itself an assertion.

The underlying outcome measurements remain separate observations.

---

# 20. Workflow 13 — Case Capsule Integrity Check

The surgeon or IT staff can ask:

> Check the Case Capsule.

Technically:

```text
casecapsule.integrity_check
```

The integrity check can detect:

- missing evidence references;
- broken provenance;
- duplicate IDs;
- unconfirmed important assertions;
- superseded assertions incorrectly marked current;
- multiple contradictory active classifications;
- missing baseline outcomes;
- malformed data;
- manifest drift.

Example result:

```text
Integrity Check

ERRORS
None

WARNINGS
1 classification pending confirmation
3-month patient outcome due in 9 days

INFO
No 12-month outcome recorded yet
```

It does not autonomously decide which competing diagnosis is clinically correct.

---

# 21. Daily use for a surgeon

A typical user interaction might look like:

```text
Surgeon:
Open the Case Capsule and summarize where we are.

ORTHO-X:
78-year-old woman, right proximal femur fracture.
Confirmed AO/OTA 31-A2.3.
Treated with cephalomedullary nail fixation.
Small postoperative seroma; no infection recorded.
At six weeks: improving mobility, pain 3/10.
Three-month outcome pending.

Surgeon:
Show me why this is A2.3.

ORTHO-X:
Current classification is based on radiographs and CT.
The original A2.2 interpretation was superseded after CT demonstrated
additional fragmentation. The revised A2.3 assertion was confirmed by
the treating surgeon on [date].

Surgeon:
Record today's TUG: 14.8 seconds and pain 1/10.

ORTHO-X:
Recorded as three-month performance and patient-reported outcome
observations.

Surgeon:
Is recovery on track?

ORTHO-X:
I can generate a trajectory assessment from baseline, six-week and
three-month data. It will remain pending clinician confirmation.
```

---

# 22. Installation — hospital IT

## 22.1 Repository components

The ORTHO-X Case Capsule repository contains four conceptual layers.

### Commit 1 — Case Capsule specification

```text
schemas/
docs/
README
```

Defines:

- Case Capsule schema;
- ClinicalAssertion;
- ProvenanceEvent;
- OutcomeObservation;
- OrthoSkills contract.

### Commit 2 — synthetic reference case

```text
examples/synthetic-proximal-femur-001/
```

Contains no real patient data.

### Commit 3 — executable Case Capsule runtime

```text
case-capsule-skill/
```

Contains:

```text
SKILL.md
scripts/capsule_runtime.py
workflow documentation
tests
```

### Commit 4 — interoperability service

```text
case-capsule-service/
```

Contains:

```text
FHIR adapter
DICOMweb adapter
MCP server
backend facade
security guidance
tests
```

---

# 23. System requirements

Development installation:

- Linux, macOS or suitable Windows environment;
- Python 3.11+ recommended;
- Git;
- access to the repository;
- optionally an MCP-compatible agent host;
- optionally FHIR endpoint;
- optionally DICOMweb endpoint.

For the MCP server:

```bash
pip install "mcp[cli]>=2,<3"
```

Production environments should use:

- virtual environment or container;
- controlled dependency lockfile;
- CI/CD;
- vulnerability scanning;
- service identity;
- secret manager.

---

# 24. Local development installation

Clone:

```bash
git clone https://github.com/MAIVAN-ai/ORTHO-X-CaseCapsule.git
cd ORTHO-X-CaseCapsule
```

Create virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install MCP dependency:

```bash
pip install "mcp[cli]>=2,<3"
```

Test the runtime:

```bash
python3 -m unittest case-capsule-skill/tests/test_runtime.py -v
```

Test the adapters:

```bash
python3 case-capsule-service/tests/test_adapters.py
```

---

# 25. Case Capsule storage directory

Configure:

```bash
export CASECAPSULE_ROOT=/var/lib/ortho-x/case-capsules
```

Development:

```bash
export CASECAPSULE_ROOT=./var/case-capsules
```

Configure runtime:

```bash
export CASECAPSULE_RUNTIME=./case-capsule-skill/scripts/capsule_runtime.py
```

Recommended production permissions:

```text
ORTXO-X service user
        │
        ├─ read/write Case Capsule store
        ├─ read authorized source systems
        └─ no unrestricted host filesystem access
```

---

# 26. Running the MCP server

## Local stdio mode

Recommended for local development and agent-host integration:

```bash
python3 case-capsule-service/mcp/server.py \
  --transport stdio
```

In this mode the agent host starts the service process directly.

---

# 27. MCP client configuration

Conceptual example:

```json
{
  "mcpServers": {
    "ortho-x-case-capsule": {
      "command": "python3",
      "args": [
        "case-capsule-service/mcp/server.py",
        "--transport",
        "stdio"
      ],
      "env": {
        "CASECAPSULE_ROOT": "./var/case-capsules",
        "CASECAPSULE_RUNTIME": "./case-capsule-skill/scripts/capsule_runtime.py"
      }
    }
  }
}
```

The exact MCP-host configuration may differ between ChatGPT/Codex, Claude, OrthoClaw and other runtimes.

---

# 28. Streamable HTTP deployment

For a hospital service architecture:

```bash
python3 case-capsule-service/mcp/server.py \
  --transport streamable-http \
  --host 127.0.0.1 \
  --port 8765
```

Do not expose the reference HTTP service directly to the public Internet.

A production deployment should sit behind:

```text
Hospital identity
     ↓
API gateway / reverse proxy
     ↓
TLS
     ↓
authorization
     ↓
MCP service
     ↓
Case Capsule backend
```

---

# 29. FHIR configuration

Configure:

```bash
export FHIR_BASE_URL=https://hospital.example/fhir
```

Authentication:

```bash
export FHIR_BEARER_TOKEN=...
```

Do not store real bearer tokens in:

- Git;
- `.env.example`;
- source files;
- public repository settings.

Use:

- hospital secret manager;
- vault;
- workload identity;
- OAuth/service credentials appropriate to the implementation.

---

# 30. How FHIR evidence is handled

Example:

```text
FHIR server

Condition/123
Procedure/456
Observation/789
QuestionnaireResponse/321

        │
        ▼

Case Capsule

source_identifier:
Condition/123

version:
3

last_updated:
...

hash:
...
```

ORTHO-X therefore knows which version of which hospital resource supported an assertion.

---

# 31. DICOMweb configuration

Configure:

```bash
export DICOMWEB_BASE_URL=https://pacs.example/dicom-web
```

Authentication:

```bash
export DICOMWEB_BEARER_TOKEN=...
```

The adapter may use:

- QIDO-RS for searching/verifying Studies;
- WADO-RS paths for authorized retrieval.

The default Case Capsule behavior is to record the Study reference rather than copy pixel data.

---

# 32. DICOM reference model

Example:

```text
PACS

StudyInstanceUID
1.2.840....

        │
        ▼

Case Capsule

evidence_id:
ev-xray-001

source_system:
hospital-pacs

source_identifier:
1.2.840....

kind:
dicom-study-reference
```

This allows a Skill to say:

> My classification used `ev-xray-001`.

The evidence can then be resolved back to PACS.

---

# 33. Identity and human confirmation

This is one of the most important hospital IT requirements.

The MCP call:

```text
casecapsule.confirm(
  assertion_id,
  actor_id,
  role
)
```

records confirmation.

It does **not by itself authenticate the human**.

Production architecture must therefore be:

```text
Surgeon
   ↓
Hospital login / SSO
   ↓
Verified identity
   ↓
Authorized agent session
   ↓
Case Capsule confirmation
```

A language model must never be able to impersonate a doctor merely by submitting:

```text
actor_id="Dr Smith"
```

The host platform must bind the confirming identity to an authenticated human session.

---

# 34. Recommended access roles

## Orthopaedic surgeon

May:

- view patient Capsule;
- register clinical confirmation;
- correct/supersede assertions;
- record treatment;
- record surgery;
- approve outcome interpretation.

## Resident / fellow

May:

- create draft assertions;
- use OrthoSkills;
- review evidence;
- propose classifications.

Confirmation rules can require attending approval.

## Physiotherapist

May:

- enter rehabilitation data;
- functional measurements;
- mobility status;
- selected outcomes.

## Outcomes coordinator

May:

- administer PROM workflows;
- record standardized outcomes;
- monitor missing timepoints.

## Hospital IT

May:

- administer service;
- configure endpoints;
- manage access;
- monitor system health.

Should not automatically receive clinical content access beyond operational necessity.

---

# 35. Audit requirements

Production deployments should log:

- who accessed which Case Capsule;
- when;
- which source evidence was retrieved;
- which Skill executed;
- which model/version was used;
- which assertion was written;
- which human confirmed/rejected it;
- exports;
- integrity checks.

The Case Capsule provenance log is part of this history.

Hospital security logging may additionally capture infrastructure-level events.

---

# 36. Data minimization

Avoid putting unnecessary patient identifiers into Case Capsule filenames or IDs.

Preferred:

```text
cc-a83f91...
```

Avoid:

```text
smith-mary-1947-hip-fracture
```

Mapping between Case Capsule identity and real patient identity should remain inside authorized hospital infrastructure.

---

# 37. Backup and disaster recovery

The Case Capsule repository contains clinically valuable derived evidence.

Hospital deployment should define:

- backup frequency;
- encryption;
- restore testing;
- disaster recovery;
- retention period;
- archival rules;
- handling after patient deletion/retention requests;
- relationship with EHR retention policy.

Because the Case Capsule references external authoritative systems, backup strategy should also consider whether referenced resources remain resolvable historically.

---

# 38. Integrity-check operations for IT

Hospital IT may schedule periodic:

```text
casecapsule.integrity_check
```

Possible monitoring metrics:

- Capsules with errors;
- broken PACS references;
- broken FHIR references;
- unconfirmed assertions older than threshold;
- missing provenance;
- invalid schema;
- duplicate IDs;
- failed writes.

These can become operational dashboards.

---

# 39. Security architecture

Minimum recommended production controls:

- TLS;
- authenticated MCP clients;
- authorization by user/service;
- patient/case access scoping;
- role-based permissioning;
- service isolation;
- input validation;
- restricted filesystem;
- secrets management;
- logging;
- anomaly detection;
- dependency scanning;
- backup;
- penetration testing;
- hospital security review.

---

# 40. Privacy architecture

Depending on jurisdiction, deployments should address:

- lawful basis for processing;
- patient consent where required;
- role of hospital/data controller;
- role of MAIVAN.ai / other processors if applicable;
- data-processing agreement;
- cross-border transfers;
- pseudonymisation;
- data minimization;
- retention;
- secondary use;
- research authorization.

No open-source software licence grants rights to patient data.

---

# 41. Recommended pilot architecture

A sensible first hospital pilot is:

```text
Hospital network
│
├── EHR / FHIR
│
├── PACS / DICOMweb
│
├── ORTHO-X MCP Service
│       │
│       └── Case Capsule Store
│
└── Authorized clinician AI client
        │
        ├── OrthoSkills
        └── ORTHO-X Case Capsule tools
```

The first pilot should preferably use:

- one clinical pathway;
- one institution;
- a small surgeon group;
- synthetic data first;
- retrospective/de-identified cases second;
- prospective supervised mode only after governance approval.

---

# 42. Recommended first clinical pilot

The synthetic reference implementation uses:

**Proximal femur fracture**

because the pathway is naturally longitudinal:

```text
ED / referral
↓
radiographs
↓
AO/OTA classification
↓
treatment decision
↓
fixation
↓
postoperative course
↓
rehabilitation
↓
functional outcome
```

This makes it ideal for testing Case Capsule persistence.

---

# 43. Silent Mode

Before ORTHO-X becomes part of routine workflow, hospitals should consider a silent validation phase.

ORTHO-X:

- reads authorized cases;
- produces classifications and assertions;
- does not expose them to treatment decisions;
- compares them retrospectively with clinician conclusions.

Metrics may include:

- agreement;
- error patterns;
- uncertainty calibration;
- missing-data behavior.

---

# 44. Supervised Mode

Next:

```text
AI proposal
     ↓
clinician review
     ↓
confirm / modify / reject
```

Every clinically material output remains human-controlled.

The Case Capsule becomes especially valuable here because confirmation and disagreement can be studied systematically.

---

# 45. Certified / production workflows

Any move beyond supervised decision support should be based on:

- intended-use definition;
- validated performance;
- quality-management processes;
- regulatory assessment;
- site-specific deployment controls;
- post-market monitoring;
- prospective outcome evidence.

The Case Capsule architecture supports those processes but does not automatically establish regulatory clearance.

---

# 46. Common clinical use cases

## Case review

> Summarize the current orthopaedic state.

## Classification

> Apply the AO/OTA Skill and show supporting evidence.

## Second opinion

> Compare the current diagnosis and treatment pathway with the evidence in the Capsule.

## MDT

> Prepare the case for today's trauma meeting.

## Postoperative review

> What events have occurred since surgery?

## Outcome review

> Compare current function with baseline.

## Complication review

> Show the surgical-site event history and related outcomes.

## PMCF

> Identify eligible confirmed cases with the required outcome follow-up.

## Quality improvement

> Which patients deviated most from their expected recovery trajectory?

---

# 47. Common staff use cases

## Physiotherapist

> Enter today's TUG and walking-aid status.

## Nurse

> Record wound review findings.

## Outcome coordinator

> Show patients missing their three-month PROM.

## Registrar

> Draft the classification for attending review.

## Clinical researcher

> Create a de-identified cohort of confirmed cases meeting defined criteria.

Secondary uses must respect hospital governance and data rights.

---

# 48. Troubleshooting — clinician

## "The AI gives the wrong classification."

Do not overwrite it silently.

Use:

```text
reject
```

or create a revised assertion that supersedes it.

This preserves useful validation data.

---

## "The CT changed my opinion."

Register the CT.

Rerun the relevant Skill.

Create a new assertion.

Supersede the old assertion.

Confirm the new one.

---

## "The current-state view looks wrong."

Run:

```text
integrity check
```

Then inspect:

- accepted assertions;
- superseded assertions;
- pending confirmation;
- evidence references.

---

## "The model seems very confident."

Confidence is not confirmation.

The surgeon must still review clinically material assertions.

---

# 49. Troubleshooting — IT

## FHIR resource cannot be retrieved

Check:

- endpoint URL;
- network routing;
- OAuth/token;
- resource ID;
- permissions;
- FHIR server logs.

---

## DICOM Study cannot be found

Check:

- Study Instance UID;
- DICOMweb endpoint;
- QIDO-RS capability;
- bearer token;
- PACS routing.

---

## MCP tools do not appear

Check:

- MCP server started;
- host configuration;
- executable path;
- Python environment;
- MCP SDK installed;
- stderr logs.

---

## Case Capsule integrity fails

Run:

```bash
python3 case-capsule-skill/scripts/capsule_runtime.py \
  integrity-check <capsule-path>
```

Do not manually delete clinical assertions to make the checker pass.

Resolve the provenance or lifecycle problem explicitly.

---

# 50. What should never be done

Do not:

- put real patient data into the public GitHub example;
- treat GitHub as a clinical database;
- expose the MCP service unauthenticated;
- store bearer tokens in source control;
- allow AI to self-confirm clinical assertions;
- delete disagreement history;
- treat model confidence as ground truth;
- copy whole PACS/EHR datasets into the Case Capsule without necessity;
- infer causality merely because event B occurred after event A;
- use a development build for unsupervised clinical care.

---

# 51. The key workflows in one page

```text
1. CREATE
Referral → Case Capsule

2. REGISTER
FHIR / DICOM / documents → Evidence references

3. REASON
Evidence → OrthoSkill

4. ASSERT
OrthoSkill → Structured assertion

5. CONFIRM
Surgeon → confirm / modify / reject

6. TREAT
Decision → intervention

7. RECORD
Surgery / postoperative events / rehab

8. MEASURE
PROMs / ClinROMs / functional outcomes

9. INTERPRET
Recovery trajectory

10. VERIFY
Integrity + provenance

11. LEARN
Quality improvement / PMCF / RWE / VBHC
```

---

# 52. The ORTHO-X architecture in one page

```text
                         ORTHO-X OrthoFlow
                               │
                         care orchestration
                               │
              ┌────────────────┼────────────────┐
              │                │                │
         OrthoSkills       SurgiCorder       Outcomes
              │                │                │
              └────────────────┼────────────────┘
                               │
                              MCP
                               │
                               ▼
                      ORTHO-X Case Capsule
                               │
                    longitudinal clinical
                    memory + provenance
                               │
                ┌──────────────┼──────────────┐
                │              │              │
              FHIR         DICOMweb       Other APIs
                │              │
                ▼              ▼
              EHR           PACS/VNA
```

---

# 53. The principle that should guide every user

When using ORTHO-X, always ask:

> **What is the evidence?**

> **What is the interpretation?**

> **Who or what created that interpretation?**

> **Has a clinician confirmed it?**

> **Has it subsequently been superseded?**

> **What happened to the patient afterward?**

The Case Capsule exists to preserve those relationships.

---

# 54. Short version for surgeons

If you remember only five things:

1. **Ask ORTHO-X to open the Case Capsule before reasoning about the patient.**
2. **Let the relevant OrthoSkill analyze the evidence.**
3. **Confirm, correct or reject important AI assertions.**
4. **Keep recording surgery, postoperative events, rehabilitation and outcomes into the same Capsule.**
5. **Never confuse the model's answer with the authoritative clinical record.**

The result is a longitudinal record in which clinical intelligence can improve without losing provenance.

---

# 55. Short version for hospital IT

If you remember only five things:

1. **EHR and PACS remain authoritative systems.**
2. **Case Capsules store structured references, derived assertions, provenance and outcomes.**
3. **FHIR and DICOMweb connect the clinical evidence layer.**
4. **MCP is the model-neutral agent-facing tool interface.**
5. **Authentication, authorization, human identity and clinical governance must be supplied by the hospital deployment environment.**

---

# 56. Strategic end-state

The technical goal is not simply to create a better orthopaedic chatbot.

It is to establish a longitudinal clinical intelligence architecture in which:

```text
evidence
→ reasoning
→ decision
→ execution
→ events
→ recovery
→ patient outcome
```

remain connected.

That makes the Case Capsule potentially useful not only for individual patient care, but also for:

- clinical quality improvement;
- outcome benchmarking;
- ICHOM-aligned measurement;
- PMCF;
- real-world evidence;
- clinical research;
- federated learning;
- value-based healthcare.

The durable asset is therefore not the chat conversation.

**The durable asset is the provenance-linked longitudinal Case Capsule.**