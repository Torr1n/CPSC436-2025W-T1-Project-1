# Project Design Rubric (Projects 1–3)

---

Scope: Design-only. No code or running system is required; prototypes are optional and not graded. Evaluation focuses on framing, specification, privacy/ethics/reciprocity, trade-offs, and a minimal evaluation plan.

This rubric covers design-only projects. See PIA_Grading_Rubric.md for detailed privacy scoring; this rubric references it.

## Weights (Project 1 solo → Projects 2/3 teams up to 3)

- Insight & Problem Framing: 20% → 15%
- Specification Clarity (target/horizon/features, API, metrics/SLA): 20% → 15%
- Baseline & Evaluation Plan (incl. SLA/cost): 15% → 15%
- Privacy/Ethics/Reciprocity (PIA + telemetry matrix + guardrails): 20% → 20%
- Architecture & Feasibility (diagram, trade-offs): 10% → 15%
- Risks & Mitigations + Measurement Plan: 10% → 10%
- Evolution & Communication (git history, memo, clarity): 5% → 10%

## Notes on team scale (Projects 2/3)

- Expect deeper trade-off analysis and broader alternatives per extra teammate.
- Collaboration evidence: division of roles, integrated design, coherent narrative.

## Scoring guidelines

### 1. Insight & Problem Framing

- A: Clear user/decision, compelling rationale, non-obvious insight(s) steering the design.
- B: User/decision clear; insight plausible but limited.
- C: Generic; weak link to decision; little insight.

### 2. Specification Clarity

- A: Target/horizon/features clear with no leakage; API schema coherent; metrics/SLA well-justified.
- B: Mostly clear; minor leakage risks; API okay; metrics adequate.
- C: Vague; leakage likely; API unclear; metrics misaligned.

### 3. Baseline & Evaluation Plan

- A: Strong baseline; minimal experiment defined; SLA + cost measured/estimable.
- B: Baseline present; experiment plausible; SLA hand-wavy.
- C: No baseline; evaluation unclear.

### 4. Privacy, Ethics, Reciprocity (PIA-linked)

- A: PIA specific; telemetry matrix thoughtful; guardrails (k-anon/jitter/opt-ins) integrated; reciprocity concrete.
- B: PIA present; some guardrails; reciprocity basic.
- C: Superficial PIA; guardrails missing; no reciprocity.

### 5. Architecture & Feasibility

- A: Diagram and components fit constraints; trade-offs and alternatives explicit.
- B: Reasonable; limited trade-off discussion.
- C: Hand-wavy; missing constraints.

### 6. Risks & Mitigations + Measurement Plan

- A: Top risks identified with concrete tests; clear acceptance criteria.
- B: Risks listed; tests partial.
- C: Risks vague; no tests.

### 7. Evolution & Communication

- A: Git history shows evolution; insight memo, assumption audit, Socratic log referenced; clean, concise doc.
- B: Some evolution; artifacts present; minor clarity issues.
- C: One-shot dump; artifacts missing; unclear.

## Submission checklist

- Project Spec (2–3 pages) using Project_Spec_Template.md
- Architecture diagram (image embedded or linked)
- PIA excerpt + telemetry decision matrix (link full PIA)
- Insight memo, assumption audit, Socratic log links
- Git hash (or tag/range) showing evolution of design
