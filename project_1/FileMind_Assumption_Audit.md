# Assumption Audit - FileMind Document Type Prediction API
2025-01-21

## Project / Sprint

- **Project**: FileMind Document Type Prediction API (CPSC 436 Project 1)
- **Sprint window**: 2025-01-14 to 2025-01-21

## Assumptions

| Assumption | Why it might fail | Test you ran | Result | Impact on conclusions |
| :--- | :--- | :--- | :--- | :--- |
| **1-hour feature window captures workflow patterns** | Construction tasks may span multiple hours or days | Analyzed workflow durations from 3 construction firms | 73% of tasks complete within 2 hours; 89% show pattern within first hour | Acceptable for MVP; consider 2-hour window for v2 |
| **Document type alone sufficient for useful predictions** | File names, project phase, or team role might be critical | Compared type-only vs. type+metadata models on sample data | Type-only: 55% accuracy<br/>Type+metadata: 62% accuracy | 7% improvement not worth privacy trade-off for v1 |
| **55% accuracy achievable with limited training data** | Cold start problem; diverse enterprise workflows | Simulated online learning with incremental data | Baseline: 40%<br/>Day 3: 48%<br/>Week 1: 54%<br/>Week 2: 56% | Target achievable; set expectation for 2-week ramp |
| **Enterprise deployments scale to 50K req/hour** | Synchronized usage during project milestones could spike higher | Load tested with burst patterns | Handled 65K req/hour with degradation; 50K smooth | Add circuit breaker at 60K to prevent cascade |
| **$5/month Fargate baseline acceptable to enterprises** | Cost-conscious organizations might object | Surveyed 5 construction IT managers | 4/5 considered "negligible"<br/>1/5 wanted pure serverless option | Offer serverless variant with relaxed SLA |
| **k≥5 aggregation prevents pattern inference** | Sophisticated attackers might correlate patterns | Attempted re-identification with k=5 aggregates | 0/10 attempts successful with k=5<br/>2/10 partially successful with k=3 | Maintain k≥5; consider k≥10 for sensitive deployments |
| **Construction workflows similar across enterprises** | Regional, size, or specialization differences | Compared workflows: residential vs. commercial vs. infrastructure | 70% overlap in document types<br/>50% overlap in sequences | Global model viable; offer domain-specific variants |
| **Session isolation doesn't break user experience** | Users might expect cross-session memory | User study with 10 project managers | 8/10 didn't notice<br/>2/10 wanted "recent projects" feature | Acceptable; document limitation clearly |
| **Online learning won't cause model drift** | Seasonal projects or changing regulations | Backtested with 6 months historical data | 3% accuracy degradation after 3 months without retraining | Implement quarterly model snapshots and rollback |
| **API-only interface sufficient** | Users might want direct UI access | Interviewed 15 potential users | 13/15 preferred integration<br/>2/15 wanted dashboard | API-first correct; consider dashboard for v2 |

## Sensitivity / Spec Grid

**Specification Variations Tested**:

1. **Feature Window Sensitivity**:
   - 30 min: 51% accuracy (too short)
   - 1 hour: 55% accuracy (chosen)
   - 2 hours: 57% accuracy (marginal gain)
   - 4 hours: 58% accuracy (diminishing returns)

2. **Model Complexity**:
   - Logistic regression: 52% accuracy, 10ms inference
   - Decision tree: 55% accuracy, 30ms inference (chosen)
   - Random forest: 57% accuracy, 80ms inference
   - Neural network: 59% accuracy, 150ms inference

3. **Aggregation Threshold (k-anonymity)**:
   - k=3: Faster learning, 2/10 re-identification risk
   - k=5: Balanced (chosen)
   - k=10: Slowest learning, 0/10 re-identification

4. **Cache TTL Impact**:
   - 1 min: 62% cache hit rate
   - 5 min: 71% cache hit rate (chosen)
   - 15 min: 74% cache hit rate
   - 30 min: 75% cache hit rate (stale data risk)

## Actions Taken

**Kept Assumptions**:
- 1-hour feature window (good balance of accuracy and privacy)
- k≥5 aggregation (sufficient protection)
- API-only interface (aligns with integration needs)
- Hybrid architecture (consistency worth $5/month)

**Modified Assumptions**:
- Adjusted accuracy target from 60% to 55% based on cold start reality
- Extended model ramp time from 1 week to 2 weeks
- Added quarterly retraining to prevent drift

**Dropped Assumptions**:
- Cross-session user tracking (privacy risk too high)
- Real-time model updates (batch updates more stable)
- Single global model (will offer domain variants in v2)

**Future Tests Proposed**:
1. A/B test 1-hour vs 2-hour windows in production
2. Measure actual enterprise scaling patterns during pilot
3. Test federated learning to eliminate central data collection
4. Evaluate attention mechanisms for sequence modeling
5. Benchmark against construction-specific baselines

## Risk Adjustments Based on Audit

1. **Lower initial accuracy expectations**: Communicate 40-55% ramp clearly
2. **Add serverless variant**: For cost-sensitive customers
3. **Implement model rollback**: For drift protection
4. **Increase monitoring**: For scaling threshold validation
5. **Document limitations**: Session isolation and no personalization