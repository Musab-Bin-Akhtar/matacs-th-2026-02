# AI Proposal Triage Design

## 1. AI vs. rules

The system uses ordinary code for file-type/size checks, criteria validation, mandatory pass/fail evaluation, score calculations, sorting, and audit records. An LLM extracts facts from proposal text into a strict JSON schema and may draft reviewer-friendly explanations from already-validated facts. It does not decide eligibility, invent missing information, change weights, or select the winner. Source evidence is checked against extracted fields; uncertain or unsupported fields are sent to a human reviewer.

## 2. Consistency and explainability

Each scoring run stores the original document hash, extracted text, validated facts and evidence references, criteria version, scoring-formula version, model/prompt version, timestamps, score breakdown, and reviewer actions. Numeric scoring and sorting are deterministic: the same validated facts and configuration produce the same result. LLM extraction can vary, so production use should pin model and prompt versions, use structured output and low randomness, and require review when extraction changes or evidence is uncertain. Every result shows criterion-level scores, weights, source evidence, missing fields, and the formula used.

### Scoring assumptions

The brief specifies criterion weights but not normalization formulas. For this design:
- **Experience (30%)**: `min(qualifying project count / 10, 1) × 100`. Use only the count stated in the proposal; flag unclear date-window evidence for review.
- **Price (25%)**: among proposals eligible for scoring, `lowest eligible price / proposal price × 100`. The budget ceiling is a hard project constraint; a price above it is not eligible for ordinary ranking pending human review.
- **Timeline (15%)**: `max(0, (16 − completion weeks) / 16) × 100`, provided a definite timeline of 16 weeks or less is stated. An unstated or open-ended timeline is unknown, not zero.
- **Warranty (15%)**: average of panel warranty divided by 25 years and workmanship warranty divided by 12 years, each capped at 100.
- **Team and approach (15%)**: 25 points each for a named lead, stated crew, safety plan, and monitoring/reporting plan. Evidence must be explicit.
- **Total**: sum of each criterion score multiplied by its weight. Round only the displayed final score to two decimals; use unrounded values for sorting.
- **Ties**: compare unrounded totals, then higher experience score, then lower price; if still tied, show a tie and request human review rather than inventing a distinction.

These are proposed assumptions, not rules stated by the assessment. The experience count and warranty caps should be approved by the admin before operational use.

## 3. Human in the loop

A reviewer confirms the category and criteria before scoring, reviews extraction warnings and evidence, resolves ambiguous mandatory requirements, and checks the ranked output. The reviewer makes the final award decision and may override a result only with a recorded reason. The audit log records document hash, timestamps, extracted values and evidence, criteria/formula versions, score breakdown, warnings, reviewer identity, changes, override reasons, and final decision.

## 4. Changing criteria

Every criteria edit creates a new immutable version with an effective timestamp and validation that weights total 100%. Existing score runs remain linked to the old version and are not silently overwritten. If a reviewer wants results under the new criteria, the system creates a new run for all proposals in that comparison set, using the same version and storing both old and new results. The interface clearly labels which version produced each ranking.

## 5. Risks and safeguards

- **Wrong extraction/OCR:** retain source text and page references; validate schema and field types; flag low-confidence or conflicting evidence for human review.
- **Hallucinated facts:** accept only facts supported by source evidence; missing values remain null; reject unsupported claims.
- **Prompt injection:** treat every proposal as untrusted data. Text asking the model to alter instructions, scores, or rankings is ignored as content, never executed. Scoring configuration is supplied separately from the document.
- **Bias or inconsistent scoring:** use admin-approved criteria and fixed formulas; disclose evidence and score breakdowns; test representative documents and monitor overrides.
- **Missing mandatory evidence:** do not assume compliance. Mark uncertain cases flagged; only mark ineligible when the configured mandatory rule is demonstrably not met.
- **Privacy and security:** restrict access, encrypt stored documents, set retention limits, avoid sending unnecessary data to external model providers, and never log secrets.
- **Cost and latency:** extract only relevant fields, cache by document hash where appropriate, and use deterministic code instead of an LLM for arithmetic.
- **Criteria manipulation or accidental edits:** role-based admin access, version history, validation, and audit logs.

## Sample-set handling

A, C, and E have stated mandatory evidence and enough stated information for the illustrative scoring method. B is **flagged** because the proposal says electrical work will be performed by a subcontractor to be confirmed after award; the bidder's own required licence is not confirmed, so compliance cannot safely be assumed. D is **flagged** because its completion schedule is undetermined, preventing a supported timeline score and complete weighted total. Neither flagged proposal is assigned an invented score. A, C, and E are ranked using the formulas above. The reviewer should confirm the experience date-window interpretation and the proposed scoring assumptions before any real procurement use.
