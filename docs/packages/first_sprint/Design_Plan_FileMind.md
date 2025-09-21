# Design Plan: FileMind Document Type Prediction API

## 1. Executive Summary & Project Context

FileMind is a scalable prediction API designed to enhance construction project workflows by predicting which document type a user will interact with next based on their recent file usage patterns. The system integrates with our existing FileMind document control agent that manages file systems for construction enterprises with repeated project folder structures.

**Core Challenge:** Design a system that learns from document usage patterns to predict workflow sequences, scales elastically during enterprise-wide deployments (100 req/day → 50,000 req/hour), maintains strict privacy standards with no PII exposure, and operates within cloud free-tier constraints during normal operations.

**Key Stakeholders:**
- Primary Users: Construction project managers and document controllers
- Secondary Users: FileMind agent for workflow automation
- Enterprise Benefit: Reduced context switching and faster document retrieval through predictive suggestions

## 2. Prediction API Objective & User Context

### 2.1 User & Decision Framework

- **Who:** Construction project teams using FileMind document control system
- **Decision:** Which document type to prepare or access next in their workflow
- **Value Proposition:** Augment job performance by predicting next required documents, eventually enabling workflow automation

### 2.2 Prediction Target & Horizon

- **Target:** Document type (categorical) - e.g., drawings, RFIs, specifications, reports, safety files, correspondence, contracts, submittals, quality estimates, cost reporting
- **Horizon:** Next file interaction within the current session (1 minute to several hours)
- **Output:** Probability distribution over all document type categories
- **Update Frequency:** Online learning with continuous parameter updates based on user feedback

### 2.3 Feature Engineering (No Leakage)

Features available at prediction time:
- Document type interaction counts within past hour window
- Temporal features: hour of day, day of week
- Action type distribution (read, modify, create operations)
- Session duration indicator

**Explicitly Excluded (Leakage Prevention):**
- Current directory location (could leak future intent)
- Specific file names or content
- Individual user identifiers
- Future file operations

## 3. Scaling Requirements & Viral Scenario

### 3.1 Traffic Patterns

| Load Scenario  | Request Rate    | Duration    | Frequency |
| -------------- | --------------- | ----------- | --------- |
| Normal         | 100 req/day     | Continuous  | Daily     |
| Pilot Phase    | 1,000 req/day   | 3 months    | Expected  |
| Enterprise     | 10,000 req/day  | After pilot | Growth    |
| Full Deploy    | 50,000 req/hour | Peak hours  | Potential |

### 3.2 Performance SLAs

- **Latency:** p95 < 200ms (non-critical service, some flexibility allowed)
- **Availability:** 99.5% (allows 3.6 hours downtime/month)
- **Throughput:** 15 requests/second sustained
- **Model Inference:** < 50ms per prediction
- **Cache Hit Rate:** >70% for common document type sequences

### 3.3 Degradation Strategy

Priority levels during overload:
1. Serve cached predictions for common patterns
2. Fall back to baseline model (most frequent document type)
3. Return top-3 most likely document types without personalization
4. Skip prediction, allow manual navigation

## 4. System Architecture & Components

### 4.1 Architecture Decision: Serverless with Container Model Serving

**Primary Choice: Hybrid Architecture**
- AWS Lambda for API and preprocessing
- ECS Fargate for model inference (consistency)
- Rationale: Balance cost efficiency with model serving stability

**Alternative Evaluated: Pure Serverless**
- Considered but rejected due to model loading overhead
- Cold starts problematic for 50MB+ model artifacts

### 4.2 Component Architecture
```
Client (FileMind Agent) → API Gateway → Lambda (Feature Extraction)
                                            ↓
                                    ECS Fargate (Model Server)
                                            ↓
                                    DynamoDB (Feature Store)
                                            ↓
                                    S3 (Model Artifacts & Logs)
```

### 4.3 Caching Strategy
1. **API Gateway Cache:** Common feature patterns, 5-minute TTL
2. **Lambda Memory:** Frequent document type sequences, 1-minute TTL
3. **DynamoDB:** User session features, 1-hour TTL

### 4.4 Data Storage Design
- **Feature Store (DynamoDB):** 1-hour rolling window of interactions
- **Training Data (S3):** Anonymized patterns for model retraining
- **Model Registry (S3):** Versioned model artifacts
- **Metrics (CloudWatch):** Performance and usage analytics

## 5. Privacy-by-Design & Security Guardrails

### 5.1 Privacy Principles
- **Data Minimization:** Only document types, never content
- **Immediate Anonymization:** Hash file paths to types immediately
- **No User Tracking:** Session-based only, no cross-session linking
- **Purpose Limitation:** Predictions only, no behavior profiling

### 5.2 Technical Guardrails
- **k-Anonymity:** Pool data across teams, k≥5 for any aggregation
- **Data Retention:** Raw events deleted after mapping to types
- **Session Isolation:** No user identifiers stored
- **Aggregated Training:** Global model trained on pooled data

### 5.3 Access Control
```
FileMind Agent: API Key + HMAC signature
Enterprise Admin: IAM roles for configuration
Development Team: Least privilege for maintenance
No Public Access: Internal service only
```

### 5.4 Compliance & Audit
- NDA-protected enterprise data
- Contract-based data usage rights
- Audit logs for all model updates
- Quarterly privacy reviews

## 6. Cost Optimization & Free-Tier Strategy

### 6.1 Free-Tier Utilization (Normal Load)
| Service | Free Tier Limit | Usage at 100 req/day | Buffer |
|---------|----------------|---------------------|---------|
| Lambda | 1M requests/month | 3,000 requests | 99.7% |
| API Gateway | 1M calls/month | 3,000 calls | 99.7% |
| DynamoDB | 25 WCU/RCU | 1 WCU/RCU | 96% |
| ECS Fargate | None (paid) | ~$5/month min | N/A |

### 6.2 Cost Projections
- **Normal Operations:** ~$5/month (Fargate minimum)
- **Pilot Phase (1K req/day):** ~$15/month
- **Enterprise (10K req/day):** ~$50/month
- **Viral Event (3 hours):** ~$30-50
- **Monthly Budget Cap:** $100

### 6.3 Cost Control Mechanisms
- CloudWatch billing alarms at $25, $50, $75
- Auto-scaling limits on ECS tasks (max 5)
- DynamoDB on-demand pricing with limits
- S3 lifecycle policies for log rotation

## 7. Baseline Model & Validation Strategy

### 7.1 Model Progression

**Baseline (Heuristic):**
- Predict most frequently used document type in past hour
- Simple, interpretable, immediate deployment
- Expected accuracy: ~40%

**Initial Model:**
- Decision tree on document type counts
- Low complexity, fast inference
- Target accuracy: >55%

**Advanced Model (Future):**
- Sequence models (LSTM/Transformer)
- Capture workflow patterns
- Target accuracy: >70%

### 7.2 Evaluation Metrics
- **Primary:** Cross-entropy loss (classification task)
- **Secondary:** Top-3 accuracy (practical utility)
- **Business:** Click-through rate on suggestions

### 7.3 Experimental Validation
1. Collect 1 week of usage data from beta users
2. 80/20 train/test split (time-based)
3. Compare baseline vs. decision tree
4. A/B test with control group

### 7.4 Success Criteria
- [ ] Model beats baseline by >15% accuracy
- [ ] p95 latency maintained under load
- [ ] No PII in logs or storage
- [ ] Cost within budget envelope
- [ ] User acceptance >60%

## 8. Risk Assessment & Mitigation

### 8.1 Technical Risks
| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Insufficient training data | Poor predictions | High | Start with baseline, collect data |
| Model drift | Degraded accuracy | Medium | Online learning, regular retraining |
| Latency spikes | User frustration | Low | Caching, fallback strategies |

### 8.2 Ethical Risks
| Risk | Impact | Mitigation |
|------|--------|------------|
| Job automation concerns | User resistance | Transparency, augmentation focus |
| Workflow surveillance | Privacy violation | Anonymization, no individual tracking |
| Data leakage | Competitive disadvantage | Encryption, access controls |

### 8.3 Operational Risks
- **Downtime:** Non-critical service, graceful degradation
- **Cost overrun:** Budget alerts and caps
- **Integration failure:** Fallback to manual workflow

## 9. Implementation Phases (Design Focus)

**Phase 1: Requirements & Specification (Week 1)**
- Complete Project Spec Template
- Define API contract
- Identify data sources

**Phase 2: Architecture Design (Week 2)**
- Component selection
- Cost modeling
- Performance benchmarking

**Phase 3: Privacy Assessment (Week 3)**
- Complete PIA
- Implement guardrails
- Security review

**Phase 4: Validation Planning (Week 4)**
- Design experiments
- Define success metrics
- Documentation completion

## 10. API Specification

### 10.1 Endpoints

| Method | Path | Purpose | Auth |
|--------|------|---------|------|
| POST | /v1/predict | Get next document type prediction | API Key |
| GET | /v1/health | Service health check | None |
| POST | /v1/feedback | Report actual user action | API Key |

### 10.2 Request/Response Examples

**Request (POST /v1/predict):**
```json
{
  "session_id": "hash_abc123",
  "features": {
    "rfi_count": 2,
    "contract_count": 1,
    "drawing_count": 2,
    "hour_of_day": 14,
    "day_of_week": 3
  }
}
```

**Response (200):**
```json
{
  "predictions": [
    {"document_type": "specification", "probability": 0.42},
    {"document_type": "rfi", "probability": 0.31},
    {"document_type": "drawing", "probability": 0.15}
  ],
  "model_version": "1.2.0",
  "inference_time_ms": 23
}
```

## 11. Deliverables Checklist

- [ ] Project Specification (using template)
- [ ] Privacy Impact Assessment (PIA)
- [ ] Architecture Diagram (Mermaid)
- [ ] Telemetry Decision Matrix
- [ ] API Documentation
- [ ] Insight Memo (3 key insights)
- [ ] Assumption Audit
- [ ] Socratic Log
- [ ] Git history showing evolution
- [ ] Cost model spreadsheet

## 12. Next Steps

1. Review design with stakeholders
2. Conduct architecture review with Codex consultant
3. Validate privacy approach with legal team
4. Begin pilot user recruitment
5. Set up monitoring infrastructure