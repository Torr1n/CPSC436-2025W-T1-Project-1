# Insight Memo - FileMind Document Type Prediction API

2025-09-22

## Project / Sprint

- **Project**: FileMind Document Type Prediction API (CPSC 436 Project 1)
- **Sprint window**: 2025-01-14 to 2025-01-21
- **Team**: Solo design project

## Top 3 Insights (each ≤ 200 words)

### 1. Hybrid Architecture Optimizes Model Serving Consistency Over Pure Cost

**Insight**: After testing multiple alternatives, the hybrid Lambda/Fargate architecture delivers 10/10 consistency score at \$5/month, compared to Lambda+SnapStart (5/10 consistency, \$0) or Lambda+Provisioned (8/10 consistency, $8/month). The 2-3 minute Fargate scale-out is mitigated by SQS queue buffering and baseline model fallback.

**Evidence**: Architecture comparison in `FileMind_Assumption_Audit.md:52-59` shows Fargate eliminates cold starts entirely. Lambda SnapStart reduces cold starts to 100-150ms but still causes p95 violations. SQS queue (`FileMind_Architecture.mermaid`) absorbs bursts during the 2-minute scale window. Surge handling timeline shows graceful degradation: cache (0-30s) → baseline model (30s-2m) → full model (2m+).

**Why it matters**: Construction project managers work in time-sensitive environments. The \$5/month ensures consistent sub-50ms inference for 99% of requests, while the surge handling strategy prevents SLA violations during enterprise deployments. The $3/month savings vs provisioned concurrency funds monitoring infrastructure.

**Limits**: Assumes 2-3 minute scale time acceptable with degradation strategy. Instant 50k req/hour spike would see 30s-2min of ~30% accuracy predictions.

**Next question**: Could predictive scaling based on construction work patterns (8am, 1pm peaks) eliminate the degradation window entirely?

### 2. Session-Based Design Enables Complete Anonymization Without Sacrificing Utility

**Insight**: By limiting predictions to 1-hour session windows and regenerating session IDs hourly, we achieve complete user anonymization while maintaining 50%+ prediction accuracy—only 15% below the theoretical maximum with full user tracking.

**Evidence**: Analysis of construction workflow patterns (`Raw_Transcript_FileMind.md:25-30`) shows document sequences cluster within work sessions. Session isolation prevents cross-session linking (`FileMind_PIA.md:Section 3`), eliminating re-identification risk entirely.

**Why it matters**: Enterprise contracts often prohibit any form of employee tracking. This design enables deployment in privacy-sensitive organizations without exemptions or legal review delays. The 15% accuracy trade-off is acceptable given the efficiency gain over manual workflows.

**Limits**: Assumes construction workflows don't span multiple sessions frequently. Multi-day projects might benefit from longer windows.

**Next question**: Could federated learning allow model improvement without any data centralization?

### 3. Online Learning Solves the Cold Start Problem for Enterprise Deployments

**Insight**: Starting with a heuristic baseline (predict most frequent document type) and implementing online learning allows immediate deployment with 40% accuracy, improving to 55%+ within days as the model adapts to specific enterprise workflows.

**Evidence**: Construction workflows vary significantly between enterprises (`Design_Validation_Strategy_FileMind.md:14-20`). The feedback loop (`/v1/feedback` endpoint) enables continuous model updates. Progressive accuracy: 40% (baseline) → 48% (day 3) → 55% (week 1) based on pilot projections.

**Why it matters**: Eliminates the chicken-and-egg problem of needing training data before deployment. Enterprises see immediate value (slightly better than random) while the system improves automatically. This reduces deployment friction and accelerates adoption.

**Limits**: Requires sufficient daily volume (>100 interactions) for meaningful learning. Smaller teams might need pooled models.

**Next question**: How do we prevent model drift when construction project types change seasonally?

## Attachments

- **Evidence pack**:

  - Architecture comparison: `FileMind_Architecture.mermaid` (enhanced with SQS and monitoring)
  - Privacy analysis: `FileMind_PIA.md` (Section 8.1 monitoring strategy)
  - Cost projections: `FileMind_Project_Spec.md:Section 5`
  - Lambda alternatives testing: `FileMind_Assumption_Audit.md:52-59`
  - Surge handling strategy: `FileMind_Project_Spec.md:Section 8.1`
  - Privacy-preserving monitoring: `FileMind_Project_Spec.md:Section 10.1`

- **Pointers to code**:
  - Design evolution: `docs/packages/first_sprint/Design_Plan_FileMind.md`
  - Validation strategy: `docs/packages/first_sprint/Design_Validation_Strategy_FileMind.md`
  - Architecture rationale: `docs/packages/first_sprint/Architecture_Brief_FileMind.md:74-94`
  - External review learnings: `FileMind_Socratic_Log.md:56-88` (Inflection Points 4-5)
