# Suggested third commit

## Commit message

```text
feat: add executable Case Capsule bootstrap and update skill

- add ortho-x-case-capsule SKILL.md with intent router and hard safety rules
- add workflow references for referral, evidence, classification, confirmation,
  surgery, postoperative events, outcomes, current state and integrity checks
- add dependency-light Python reference persistence runtime
- enforce immutable evidence IDs and append-only provenance
- preserve superseded assertion history
- add explicit clinician confirmation and rejection operations
- add derived current-state projection
- add structural/provenance integrity checker
- add OrthoFlow integration pattern
- add runtime unit tests and executable demo
```

## Recommended repository paths

Copy this package into the repository root so it adds:

```text
case-capsule-skill/
docs/ORTHOFLOW-RUNTIME-INTEGRATION.md
```

The existing `schemas/` and `examples/` from commits 1 and 2 remain unchanged.

## Smoke test

```bash
python3 -m unittest case-capsule-skill/tests/test_runtime.py -v
bash case-capsule-skill/scripts/demo.sh
```
