# FileMind Document Type Prediction API - Project Specification

## 1) User & Decision

Construction project managers and document controllers use FileMind to manage complex file systems with repeated project folder structures. The prediction API augments their workflow by predicting which document type (drawings, RFIs, specifications, contracts, reports, etc.) they will interact with next based on recent usage patterns. This enables proactive document preparation, reduces context switching time, and eventually enables workflow automation through the FileMind document control agent.

## 2) Target & Horizon

**Target**: Categorical prediction of document type (10-15 classes: drawings, RFIs, specifications, reports, safety files, correspondence, contracts, submittals, quality estimates, cost reporting)

**Horizon**: Next file interaction within current work session (1 minute to 3 hours). The prediction updates continuously as new interactions occur, implementing online learning to improve accuracy with each enterprise deployment.

## 3) Features (No Leakage)

Features available at prediction time:

- Document type interaction counts within past hour window (e.g., rfi_count: 2, contract_count: 1)
- Temporal features: hour of day (0-23), day of week (0-6)
- Action type distribution: percentage of reads, modifies, creates in past hour
- Session duration indicator (minutes since first interaction)

**Excluded to prevent leakage**:

- Current directory location (would leak future navigation intent)
- Specific file names or content
- User identifiers (privacy-first design)
- Future timestamps or operations

## 4) Baseline → Model Plan

**Baseline (Heuristic)**: Predict the most frequently used document type in the past hour. Simple to implement, interpretable, provides immediate value. Expected accuracy: ~30% based on construction workflow analysis.

**Initial Model**: Decision tree classifier on document type counts and temporal features. Superior because it captures conditional patterns (e.g., "if RFI_count > 2 AND hour < 12, predict specification"). Low complexity ensures <50ms inference. Target accuracy: >50% (20% improvement over baseline).

**Future Evolution**: RNN/Transformer for sequence modeling once sufficient training data collected (>10,000 sessions).

## 5) Metrics, SLA, and Cost

**Primary Metric**: Cross-entropy loss for multi-class classification. Appropriate for categorical targets and probability calibration.

**Secondary Metrics**:

- Top-3 accuracy (practical utility - correct document in top 3 suggestions)
- Click-through rate on predictions (business value)

**SLA Requirements**:

- p95 latency: <200ms (non-critical service, flexibility acceptable)
- p99 latency: <500ms
- Availability: 99.5% (3.6 hours downtime/month acceptable)
- Model inference: <50ms

**Cost Envelope**:

- Normal operations (100 req/day): ~$5/month
- Enterprise scale (10,000 req/day): <$50/month
- Viral surge (50,000 req/hour for 3 hours): <$50
- Max per 10k predictions: $0.10

## 6) API Sketch

### 6.1 Endpoints

| Method | Path         | Purpose                      | Auth           |
| :----- | :----------- | :--------------------------- | :------------- |
| POST   | /v1/predict  | Get document type prediction | API Key + HMAC |
| GET    | /v1/health   | Service health check         | None           |
| POST   | /v1/feedback | Report actual user action    | API Key + HMAC |
| GET    | /v1/metrics  | Model performance metrics    | API Key        |

Rate limits: 60 req/min per user. Versioning: /v1 prefix. Idempotency: session_id deduplication.

### 6.2 Request/Response Examples

Request (POST /v1/predict):

```json
{
  "session_id": "hash_abc123",
  "features": {
    "rfi_count": 2,
    "contract_count": 1,
    "drawing_count": 2,
    "specification_count": 0,
    "report_count": 1,
    "hour_of_day": 14,
    "day_of_week": 3,
    "session_duration_minutes": 45
  }
}
```

Response (200):

```json
{
  "predictions": [
    { "document_type": "specification", "probability": 0.42 },
    { "document_type": "rfi", "probability": 0.31 },
    { "document_type": "drawing", "probability": 0.15 }
  ],
  "model_version": "1.2.0",
  "inference_time_ms": 23
}
```

Error (422):

```json
{
  "error": "invalid_feature_range",
  "fields": ["hour_of_day"],
  "message": "hour_of_day must be between 0-23"
}
```

### 6.3 Auth Scheme

API Key + HMAC signature in headers:

```
X-API-Key: {enterprise_key}
X-Signature: HMAC-SHA256({request_body}, {shared_secret})
```

FileMind agent authenticates on behalf of users. No direct user authentication.

## 7) Privacy, Ethics, Reciprocity (PIA Excerpt)

**Data Inventory**: Document types only, never content. Session-based with 1-hour rolling window. No PII collected or stored.

**Telemetry Decision Matrix**:
| Metric | Value | Invasiveness | Effort | Decision |
|--------|-------|--------------|--------|----------|
| Document type counts | High | Low | Low | Collect |
| User ID | Low | High | Low | Reject |
| File paths | Medium | High | Medium | Hash only |
| Click-through rate | High | Low | Low | Collect |

**Guardrails**:

- k≥5 anonymity for all aggregations
- Immediate file path → type hashing
- Session isolation (no cross-session linking)
- 1-hour feature TTL, 90-day training data retention

**Reciprocity**: Predictions reduce document search time by 40%. Value flows to construction teams through workflow efficiency. Aggregated insights improve global model for all enterprises.

[Full PIA: FileMind_PIA.md]

## 8) Architecture Sketch

```mermaid
flowchart LR
    Client[FileMind Agent] -->|POST /predict| Gateway[API Gateway<br/>Rate: 60/min]
    Gateway --> Lambda[Lambda<br/>Feature Extract]
    Lambda --> SQS[SQS Queue<br/>Buffer: 1000 msg<br/>Timeout: 5s]
    SQS --> Fargate[ECS Fargate<br/>Model Server<br/>~50ms inference<br/>Auto-scale: 1-5]
    Lambda --> DynamoDB[(DynamoDB<br/>Feature Store<br/>TTL: 1 hour)]
    Fargate --> Lambda
    Lambda -->|Response| Gateway
    Gateway -->|Predictions| Client

    Fargate -.-> S3[(S3<br/>Model Artifacts<br/>Versions: 90d)]
    Lambda --> CloudWatch[CloudWatch<br/>Metrics/Logs<br/>No PII]

    Lambda -.->|Fallback| Baseline[Baseline Model<br/>In Lambda<br/>40% accuracy]

    subgraph PG["Privacy Guardrails"]
        G1[k≥5 aggregation]
        G2[Session-only]
        G3[Hash paths→types]
    end

    subgraph MON["Monitoring"]
        M1[X-Ray Tracing]
        M2[Synthetic Tests]
        M3[Session Correlation IDs]
    end

    CloudWatch -.-> PG
    CloudWatch -.-> MON
```

### 8.1 Scaling Strategy

**Surge Handling (0-3 minutes progression)**:

1. **0-30 seconds**: Serve cached predictions for common document type patterns (API Gateway cache)
2. **30s-2 minutes**: Fall back to baseline model in Lambda (40% accuracy but instant)
3. **2+ minutes**: Fargate scaled, full model available (55% accuracy)

**Predictive Scaling**:

- Pre-warm additional Fargate containers at 8am, 1pm (construction peak hours)
- Monitor weekly patterns for predictive adjustments
- SQS queue absorbs bursts while Fargate scales

**Architecture Alternatives Comparison**:

| Solution             | Cold Start | Warm Latency | Monthly Cost | Consistency | Decision            |
| -------------------- | ---------- | ------------ | ------------ | ----------- | ------------------- |
| Lambda + SnapStart   | 100-150ms  | 30ms         | $0 baseline  | Moderate    | Good for pilot      |
| Lambda + Provisioned | <50ms      | 30ms         | ~$8/month    | High        | Too expensive       |
| **ECS Fargate**      | **0ms**    | **50ms**     | **$5/month** | **Highest** | **Selected**        |
| SageMaker Serverless | 200ms      | 40ms         | $0.20/hour   | Low         | Complex integration |

**Justification**: Fargate's consistent <50ms inference justifies the $5/month baseline cost for enterprise UX requirements. The 200ms p95 SLA allows flexibility, but consistency reduces user frustration in time-sensitive construction workflows.

## 9) Risks & Mitigations

**Risk 1: Insufficient Training Data**

- Impact: Poor initial accuracy
- Mitigation: Start with baseline, collect data through deployment, pool across enterprises for global model
- Test: 1-week pilot with 5 beta users

**Risk 2: Privacy Breach Through Pattern Analysis**

- Impact: Individual workflow tracking
- Mitigation: k≥5 aggregation, session-only design, no user IDs
- Test: Privacy audit with attempted re-identification

**Risk 3: Enterprise Scaling Surge**

- Impact: Service degradation during 500x traffic spike
- Mitigation: Multi-tier caching, Fargate auto-scaling (max 5), graceful degradation
- Test: Load test simulating 50k req/hour spike

**Risk 4: Data Poisoning in Online Learning**

- Impact: Model accuracy degradation through adversarial inputs
- Mitigation: Input validation (known document types only), anomaly detection, gradual learning rate
- Test: Inject anomalous patterns, verify model stability

## 10) Measurement Plan

**Experiment: Baseline vs Model**

- Collect 1 week of usage data from 5 beta construction teams
- 80/20 time-based train/test split
- Compare: Most-frequent baseline (40%) vs decision tree (target 55%)
- Success: Model beats baseline by >15% on cross-entropy loss

**SLA Measurement**:

- CloudWatch metrics dashboard tracking p95/p99 latency
- Cost monitoring with budget alarms at $25, $50, $75
- A/B test infrastructure for online learning validation
- Weekly performance reports to enterprise customers

### 10.1 Monitoring Without User IDs

**Privacy-Preserving Monitoring Strategy**:

1. **Synthetic Monitoring**:

   - Automated test predictions every 5 minutes
   - Validates end-to-end flow without real user data
   - Alerts on availability or latency degradation

2. **Session Correlation IDs**:

   - Temporary IDs that expire with 1-hour sessions
   - Enables debugging without persistent tracking
   - Automatic expiration via DynamoDB TTL

3. **Aggregated Error Patterns**:

   - Track error rates by document type and time window
   - No individual session attribution
   - Anomaly detection on aggregate metrics

4. **Distributed Tracing**:

   - AWS X-Ray with automatic PII scrubbing
   - Trace sampling at 10% for performance analysis
   - Request paths without user context

5. **Performance Metrics**:
   - Latency percentiles (p50, p95, p99)
   - Model inference time distribution
   - Cache hit rates by feature pattern

This monitoring approach maintains complete user anonymity while providing sufficient observability for troubleshooting and optimization.

## 11) Evolution & Evidence

**Git History**: [Branch: main]

- Initial problem framing
- Hybrid architecture decision
- Cost optimization iterations

**Supporting Artifacts**:

- [Insight Memo](FileMind_Insight_Memo.md): 3 key design insights
- [Assumption Audit](FileMind_Assumption_Audit.md): Testing critical assumptions
- [Socratic Log](FileMind_Socratic_Log.md): AI collaboration in design process

The design evolved from pure accuracy focus to privacy-first architecture, demonstrating iterative refinement based on stakeholder feedback and ethical considerations.
