# Assumption Audit - FileMind Document Type Prediction API

2025-09-22

## Project / Sprint

- **Project**: FileMind Document Type Prediction API (CPSC 436 Project 1)
- **Sprint window**: 2025-01-14 to 2025-01-22

## Assumptions

|                                                           | Why it might fail                                               | Test to run                                                      | Assumptions (Need to be validated)                                                                    | Impact on conclusions                                                            |
| :-------------------------------------------------------- | :-------------------------------------------------------------- | :--------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------- |
| **1-hour feature window captures workflow patterns**      | Construction tasks may span multiple hours or days              | Analyze workflow durations from 3 construction firms             | ~75% of tasks & work sessions complete within 2 hours; 90%+ show meaningful pattern within first hour | Acceptable for MVP; consider 2-hour window for v2                                |
| **Document type alone sufficient for useful predictions** | File names, project phase, or team role might be critical       | Compare type-only vs. type+metadata models on sample data        | Type-only: ~50% accuracy, Type+metadata: more likely to overfit                                       | 7% improvement not worth privacy trade-off for v1                                |
| **50+% accuracy achievable with limited training data**   | Cold start problem; diverse enterprise workflows                | Simulate online learning with incremental data                   | Baseline: 30%, Day 3: 40%, Week 1: 45%, Week 2: 50+%                                                  | Target achievable; set expectation for 2-week ramp to see meaningful predictions |
| **Enterprise deployments scale to 50K req/hour**          | Synchronized usage during project milestones could spike higher | Load test with burst patterns                                    | Handled 70K+ req/hour with degradation; 50K smooth                                                    | Add circuit breaker at 65K to prevent cascade                                    |
| **$5/month Fargate baseline acceptable to enterprises**   | Cost-conscious organizations might object                       | Survey 5 construction IT managers                                | 4/5 considered "negligible" 1/5 wanted pure serverless option                                         | Offer serverless variant with relaxed SLA                                        |
| **Lambda cold starts unacceptable for model serving**     | New Lambda features might eliminate cold starts                 | Test Lambda SnapStart and provisioned concurrency                | SnapStart: 100-150ms (better but not sufficient), Provisioned: <50ms but $8/month (more than Fargate) | Fargate remains best option for consistency at $5/month                          |
| **Construction workflows similar across enterprises**     | Regional, size, or specialization differences                   | Compare workflows: residential vs. commercial vs. infrastructure | ~75% overlap in document types, 50% overlap in sequences                                              | Global model viable; offer domain-specific variants                              |
| **Session isolation doesn't break user experience**       | Users might expect cross-session memory                         | User study with 10 project managers                              | 7/10 didn't notice, 3/10 wanted more advanced features                                                | Acceptable; clearly a document limitation and potential feature                  |
| **Online learning won't cause model drift**               | Seasonal projects or changing regulations                       | Backtest with 6 months historical data                           | ~10% accuracy degradation after 3 months without retraining                                           | Implement quarterly model snapshots and rollback or model blends                 |
| **API-only interface sufficient**                         | Users might want direct UI access                               | Interview 15 potential users                                     | 13/15 preferred integration<br/>2/15 wanted dashboard                                                 | API-first correct; consider dashboard for v2                                     |

## Sensitivity / Spec Grid

**Specification Variations to be Tested**:

1. **Expected Feature Window Sensitivity**:

   - 30 min: 45% accuracy (too short)
   - 1 hour: 50% accuracy (chosen)
   - 2 hours: 52% accuracy (marginal gain)
   - 4 hours: 53% accuracy (diminishing returns)

2. **Expected Returns on Model Complexity**:

   - Logistic regression: 44% accuracy, 10ms inference
   - Decision tree: 50% accuracy, 30ms inference (chosen)
   - Random forest: 52% accuracy, 80ms inference
   - Neural network: 53% accuracy, 150ms inference

3. **Expected Aggregation Threshold (k-anonymity)**:

   - k=3: Faster learning, 2/10 re-identification risk
   - k=5: Balanced (chosen)
   - k=10: Slowest learning, 0/10 re-identification

4. **Expected Cache TTL Impact**:

   - 1 min: 62% cache hit rate
   - 5 min: 71% cache hit rate (chosen)
   - 15 min: 74% cache hit rate
   - 30 min: 75% cache hit rate (stale data risk)

5. **Architecture Alternatives**:
   | Solution | Cold Start | Warm Latency | Monthly Cost | Consistency Score | Decision |
   |----------|------------|--------------|--------------|-------------------|----------|
   | Lambda Only | 200-400ms | 30ms | $0 | 3/10 | Too inconsistent |
   | Lambda + SnapStart | 100-150ms | 30ms | $0 | 5/10 | Better but insufficient |
   | Lambda + Provisioned | <50ms | 30ms | ~$8 | 8/10 | Too expensive |
   | **ECS Fargate** | **0ms** | **50ms** | **$5** | **10/10** | **Optimal balance** |
   | SageMaker Serverless | 200ms | 40ms | $0.20/hour | 4/10 | Complex, inconsistent |

## Actions Taken

**Kept Assumptions**:

- 1-hour feature window (good balance of accuracy and privacy)
- k≥5 aggregation (sufficient privacy protection)
- API-only interface (aligns with integration needs)
- Hybrid architecture (consistency worth $5/month)

**Modified Assumptions**:

- Adjusted accuracy target from 60% to 50% based on cold start reality
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
4. Evaluate attention or transformer mechanisms for sequence modeling (large data requirements)
5. Benchmark against construction-specific baselines

## Risk Adjustments Based on Audit

1. **Lower initial accuracy expectations**: Communicate ~30-50% ramp clearly
2. **Add serverless variant**: For cost-sensitive customers
3. **Implement model rollback**: For drift protection
4. **Increase monitoring**: For scaling threshold validation
5. **Document limitations**: Session isolation and no personalization
