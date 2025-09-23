<!-- Project1_Ideas.md 2025-09-13 -->

# Project 1 (Solo)- Guided Design Ideas

Use the 20-minute spec recipe below, then pick one idea to design. You may adapt an idea or propose your own; clarity and reasoning matter more than breadth.

# 20-Minute Spec Recipe

·User & decision → Target & horizon → Features (no leakage) → Baseline → Simple model → Metrics & SLA→ Privacy/Ethics/Reciprocity → Architecture sketch → Risks & minimal experiment.

# Ready-to-Use Ideas (choose one)

1. SafeRoute-Walking Safety Predictor (Claude)

·Users/decision: pedestrians choose safer routes/times; ops plan lighting.

·Target/horizon: P("unsafe" cell) next 60 min (200m grid).

·Features: grid cell, hour/day, lighting index, traffic proxy, historical calls-for-service (aggregated),weather,events.

·Metric/SLA: $AUC-PR$ ; p95 &lt;100 ms; $k\geq 1$ jitter on public view.

·Baseline → model: rules → logistic/GBM.

·Ethics: suppress sparse cells; no residential precision; publish disclosure.

# 2. NextBinge- Show Recommendation (Claude)

·Users/decision: reorder top-N shows for the next session.

·Target/horizon: P(click/watch) next session among candidates.

·Features: recent watches (hashed), hour/day, device type; no cross-context IDs.

·Metric/SLA: offline AUC; $\text {p95<80}$ ms.

·Baseline→model: popularity per segment → logistic.

·Ethics: minimal logs; "no personalization" fallback.

# 3. CalmCast-Anxiety Level Predictor (Claude)

·Users/decision: user well-being nudges (never employer/insurer).

·Target/horizon: stress level (low/med/high) this hour.

·Features: on-device app switches/min, typing/scroll rates (bucketed); no content/location.

·Metric/SLA: F1 for "high"; $\text {p95<20}$  ms (local).

·Baseline→ model: rules → tiny tree/logistic.

·Ethics: on-device only; opt-in; explicit non-misuse policy.

# 4. FoodMood-Restaurant Busy Tonight (Claude)

·Users/decision: diners pick times; venues staff appropriately.

·Target/horizon: occupancy band 6-9pm today (venue/area).

·Features: weekday/season, weather, events, historical patterns, transit proxy.

·Metric/SLA: MAPE; $\text {p95<}120$  ms.

·Baseline→model: seasonal naive → GBM.

·Ethics: aggregates only; disclose economic impact risks.

<!-- 1/3 -->

<!-- Project1_Ideas.md 2025-09-13 -->

# 5. StudyBuddy - On-time Submission Risk (Claude)

·Users/decision: student reminders; instructor triage (supportive only).

·Target/horizon: P(submit before deadline) next 48h.

·Features: course-level aggregates + student's own history (opt-in); no hidden monitoring.

·Metric/SLA: AUC-PR; fairness checks within course; p95 &lt;100 ms.

·Baseline→model: rules → logistic.

·Ethics: supportive use policy; opt-out; publish harms.

# 6. SurgeRisk-Traffic Surge Predictor

·Users/decision: pre-warm cache/scale for next 5 min.

·Target/horizon:P(&gt; X req/min) next 5 min.

·Features: recent counts, hour/day; no future info.

·Metric/SLA: AUC-PR; p95&lt; 50ms.

·Baseline→model: EWMA threshold → logistic.

·Ethics: minimal telemetry; disclose rate-limit policies.

# 7.SlowReq-Slow Request Risk

·Users/decision: route to cached/simplified path.

·Target/horizon: P(TTFB &gt; 500 ms) for this request.

·Features: endpoint, payload size, device/geo bucket.

·Metric/SLA: AUC; p95&lt;10ms.

·Baseline→model: rules → stump/tree.

·Ethics: no PII; no raw IP storage.

# 8. QueueETA-Queue Wait-TTime

·Users/decision: show ETA, throttle heavy jobs.

·Target/horizon: ETA minutes.

·Features: queue depth, arrival rate, service time.

·Metric/SLA: MAE; p95 &lt;15 ms.

·Baseline→model: Little's Law → GBM.

·Ethics: transparency of uncertainty (CI bands).

# 9. ModGuard-Content Moderation Risk

·Users/decision: warn/gate; human in the loop.

·Target/horizon: P(violation) for text/image.

·Features: text length/lang; image metadata; no payload logging.

·Metric/SLA: AUC-PR; p95 &lt;100 ms.

·Baseline→model: rules → small classifier or API.

·Ethics: appeals; false-positive budget; minimal data.

## Deliverables (design-only)

·Spec (2-3 pages) using Project_Spec_Template.md.

·Architecture diagram + API sketch.

·PIA excerpt + telemetry decision matrix (link full PIA).

<!-- 2/3 -->

<!-- Project1_Ideas.md 2025-09-13 -->

·Insight memo, assumption audit, Socratic log links.

·Git hash (or range/tag) showing design evolution.

Note**: No implementation or code is required for Project 1.** Prototypes are optional and not graded; focus on the design and reasoning.

Rubric

·See projects/Project_Design_Rubric.md and PIA_Grading_Rubric.md.

<!-- 3/3 -->

