<!-- Project1_Exemplar_Architecture_Evaluation.md 2025-09-13 -->

# Project 1 Exemplar: Architecture & Evaluation (SurgeRisk)

This exemplar shows the level of detail expected for the architecture diagram and minimal evaluation plan.Adapt the structure to your chosen idea.

## 1) User & Decision

·User: SRE/Engineer on-call

·Decision: Pre-warm cache / throttle burst for the next 5 minutes

## 2) Target & Horizon

·Target: P(&gt; X req/min) in next 5 minutes (binary)

·Horizon: 5 minutes

# 3) Features (No Leakage)

·Recent request counts: last 10 x 1-minute buckets (t-1..t-10)

·Time features: hour of day,day of week

·Exclude: future counts; any user identifiers (no PII)

# 4) Architecture Diagram (example)

flowchart LR

A[Client / Scheduler] --&gt;|/predict| B(API Gateway)

B --&gt; C[Lambda: Predictor]

C --&gt; D[(Features Store: counts t-1..t-10)]

C --&gt; E[Model Artifact (v1.0)]

C--&gt; F[(Observability: logs/metrics)]

subgraph Guardrails

G[k-anon: N/A (no user data)]

H[Telemetry matrix: keep only windowed counts]

I[Retention: raw logs TTL ≤ 14d]

end

F -. updates . -&gt; Guardrails

Notes:

·Keep diagram to ~6-8 boxes. Label where guardrails apply (telemetry minimization, retention TTL).

·If your idea involves public outputs (e.g., map tiles), show where k-anonymity/jitter is enforced.

# 5) Minimal Evaluation Plan

·Baseline: EWMA threshold on last 5 minute buckets → surge if EWMA &gt; K

<!-- 1/2 -->

<!-- Project1_Exemplar_Architecture_Evaluation.md 2025-09-13 -->

·Model: Logistic regression on last $5buckets+hour+day$ 

·Metric: AUC-PR (imbalanced positives)

·Offline test: simulate time series with bursts; label windows; compare baseline vs model AUC-PR

·SLA (p95 latency):&lt;50ms for /predict

。Measurement plan: synthetic client sends 1k requests; record response times; compute p95

·Cost envelope: free-tier minded (API Gateway + Lambda) at normal load

**6) Privacy/Ethics (PIA excerpt)**

·Data inventory: minute-level aggregate counts only; no IPs/UA

·Purpose: capacity planning / pre-warm decision

·Retention: raw logs TTL ≤ 14 days; aggregates 90-180 days

·Access: least-privilege; no backdoors

·Telemetry decision matrix: list each metric/log with value vs invasiveness vs effort; keep/drop

## 7) Risks & Tests

·Misfire risk (false positives): acceptance test-precision ≥ PO at operating threshold

·Tail latency risk: p95 measured $<=0$ ms under nominal load (acceptance)

·Cost drift: verify monthly cost under normal load stays within free tier

## 8) Tips for Creating Diagrams

·Tools: Excalidraw (hand-drawn style), draw.io/diagrams.net (export PNG), Mermaid (rendered in docs)

·Include: data flow, components, where guardrails apply, where retention is enforced, SLA target

·Exclude: low-level implementation details; keep to one page, readable

Checklist for your diagram

- [ ] User & decision

-[ ] Target & horizon label

- [ ] Data flow (boxes/arrows)

- [ ] Privacy guardrails shown on the path

- [ ] Retention/TTL markers

- [ ] Observability box

- [ ] SLA target noted

This level of specificity is sufficient for Project 1. Keep the focus on reasoning and guardrails; code is not required.

<!-- 2/2 -->

