# Privacy Impact Assessment - FileMind Document Type Prediction API

## 1. Overview

**Project Name**: FileMind Document Type Prediction API

**Purpose**: Predict the next document type a construction project manager will interact with based on recent file usage patterns, enabling workflow augmentation and reduced context switching.

**Scope**: Enterprise construction teams using FileMind document control system. Processes document interaction metadata to generate predictions without accessing file content or tracking individual users.

**Data Processing Summary**:
- **Collection**: Document type metadata from file interactions (read/modify/create)
- **Processing**: Hash file paths to types, aggregate into hourly counts
- **Sharing**: Predictions to FileMind agent only, no third parties
- **Retention**: 1-hour feature window, 90-day anonymized training data

## 2. Data Inventory

| Field | Purpose | Lawful Basis | Minimization | Retention | Access |
|-------|---------|--------------|--------------|-----------|---------|
| session_id (hashed) | Request deduplication | Contract | Hash only | 1 hour | System only |
| document_type_counts | Feature for prediction | Contract | Types only, no content | 1 hour | Model server |
| hour_of_day | Temporal pattern | Contract | Coarse time | 1 hour | Model server |
| day_of_week | Workflow pattern | Contract | Day only | 1 hour | Model server |
| action_type | Usage pattern | Contract | Aggregated % | 1 hour | Model server |
| session_duration | Engagement metric | Contract | Minutes only | 1 hour | Model server |
| prediction_result | Model output | Contract | Probabilities | Not stored | Client only |
| click_through | Feedback signal | Contract | Binary | 90 days | Training pipeline |

**Derived Fields**:
- Document type frequencies (computed from counts)
- Prediction confidence scores (computed from model)
- Aggregated accuracy metrics (no individual tracking)

## 3. Linkability & Identifiability

**Session Linkability**:
- Events linked only within 1-hour session window via hashed session_id
- No cross-session linking possible
- Session_id regenerated hourly

**User Identifiability**:
- No user IDs collected or stored
- No quasi-identifiers (IP, user agent, location) retained
- File paths immediately hashed to document types
- Re-identification risk: **Negligible** - no persistent identifiers

**Enterprise Isolation**:
- Data pooled across enterprise for global model
- Individual enterprise patterns not distinguishable
- k≥5 aggregation for any analytics

## 4. Purpose Limitation & Secondary Use

**Declared Purposes**:
1. Generate document type predictions for workflow augmentation
2. Improve model accuracy through aggregated feedback
3. Provide performance metrics to enterprise admins

**Process for New Purposes**:
- Requires PIA amendment
- Enterprise contract modification
- User notification through FileMind agent

**Function Creep Protection**:
- Technical: Automatic data expiration (1-hour TTL)
- Policy: Quarterly purpose audit
- Contractual: Explicit use limitations in enterprise agreements

## 5. Minimization & Retention

**Aggregation Strategies**:
- Document interactions → type counts (loss of sequence)
- Timestamps → hour of day (loss of precision)
- File paths → document types (loss of specifics)

**Sampling**:
- Training data: Random 10% of sessions for model updates
- Metrics: Hourly aggregates only

**Anonymization**:
- Immediate hashing of file paths
- No reverse mapping maintained
- Session IDs non-reversible

**Retention Schedule**:
| Data Type | Raw TTL | Aggregate TTL | Deletion Method |
|-----------|---------|---------------|-----------------|
| Features | 1 hour | N/A | DynamoDB TTL |
| Session data | 1 hour | N/A | Automatic expiry |
| Training samples | N/A | 90 days | S3 lifecycle |
| Model artifacts | N/A | 180 days | Manual cleanup |
| Metrics | N/A | 365 days | CloudWatch expiry |

## 6. Access Control & Governance

**Roles & Permissions**:
| Role | Access Level | Audit |
|------|--------------|-------|
| FileMind Agent | Predict API only | All requests logged |
| Model Server | Feature store read | CloudTrail |
| ML Engineers | Aggregated metrics | IAM + MFA |
| Enterprise Admin | Performance dashboard | Contract-based |
| DevOps | System health only | Least privilege |

**Third Parties**: None. All processing internal to FileMind infrastructure.

**Audit Trail**:
- API access logs (no request body)
- Model version changes
- Configuration modifications
- Quarterly access reviews

## 7. Transparency & Choice

**Communication Plan**:
- Enterprise notification: Contract appendix detailing data usage
- User awareness: FileMind agent displays "AI-powered suggestions"
- Documentation: Public API docs exclude implementation details

**Transparency Measures**:
- Prediction explanation: Top factors shown (e.g., "Based on recent RFI usage")
- Model card: Accuracy metrics published quarterly
- Data usage dashboard: Aggregate statistics for enterprise admins

**Choice Mechanisms**:
- Enterprise level: Opt-in via contract
- User level: Disable predictions in FileMind settings
- Graceful degradation: Manual workflow continues without predictions

## 8. Security

**Threats & Mitigations**:
| Threat | Mitigation |
|--------|------------|
| API abuse | Rate limiting (60 req/min), API key rotation |
| Model extraction | Response fuzzing, confidence score rounding |
| Data poisoning | Input validation, anomaly detection |
| Side-channel attacks | Constant-time operations, noise injection |
| Enterprise data leakage | Tenant isolation, encrypted storage |

**Technical Controls**:
- Transport: TLS 1.3 minimum
- Storage: AES-256 encryption at rest
- Secrets: AWS Secrets Manager, 90-day rotation
- Network: Private VPC, no internet egress for model server

**Monitoring**:
- Anomaly detection on request patterns
- Alert on bulk data access
- Quarterly penetration testing

## 9. Compliance & Policy Alignment

**Course Policy**: Privacy-by-design principles implemented throughout

**Legal Frameworks**:
- GDPR: Not applicable (no EU personal data)
- CCPA: Not applicable (no California consumer data)
- PIPEDA: Compliant via consent and minimization

**Industry Standards**:
- ISO 27001: Security controls aligned
- SOC 2 Type II: Audit readiness maintained

**Contractual**:
- NDA protection for enterprise data
- Data usage rights explicitly defined
- Liability limitations for predictions

## 10. Residual Risks & Trade-offs

**Residual Risks**:

| Risk | Likelihood | Impact | Rationale for Acceptance | Contingency |
|------|------------|--------|--------------------------|-------------|
| Pattern inference | Low | Medium | k≥5 aggregation sufficient | Increase k threshold |
| Model bias | Medium | Low | Construction workflows similar | Regular bias audits |
| Feature drift | Medium | Low | Online learning adapts | Manual retraining option |

**Trade-offs Made**:
1. **Accuracy vs Privacy**: Chose session-only design (55% accuracy) over user tracking (70% potential)
2. **Cost vs Consistency**: Accepted $5/month Fargate baseline for reliable inference
3. **Functionality vs Anonymity**: No personalization to maintain complete anonymization

**Business Rationale**:
- 40% efficiency gain justifies accuracy limitations
- Enterprise contracts value privacy over marginal accuracy gains
- Regulatory compliance more important than perfect predictions

## Telemetry Decision Matrix

| Metric | Value | Invasiveness | Effort | Decision |
|--------|-------|--------------|--------|----------|
| Document type counts | High | Low | Low | **Collect** |
| User identifier | Low | High | Low | **Reject** |
| File paths | Medium | High | Medium | **Hash only** |
| Click-through rate | High | Low | Low | **Collect** |
| Session duration | Medium | Low | Low | **Collect** |
| Prediction confidence | High | Low | Low | **Collect** |
| Individual accuracy | Medium | Medium | Medium | **Reject** |
| Aggregate accuracy | High | Low | Medium | **Collect** |
| Error patterns | High | Low | High | **Collect** |
| Feature importance | High | Low | Medium | **Collect** |

## Attachments

- [Architecture Diagram](FileMind_Architecture.mermaid): Shows privacy guardrails placement
- [Data Flow Diagram](docs/data_flow.md): Details transformation steps
- [Telemetry Matrix](above): Complete value/invasiveness analysis
- [Audit Log Schema](docs/audit_schema.md): Defines logged events

## Review & Approval

**PIA Completed**: 2025-01-21
**Review Schedule**: Quarterly
**Next Review**: 2025-04-21
**Approved By**: [Pending Enterprise Privacy Officer]

## Amendments Log

| Date | Change | Reason | Approver |
|------|--------|--------|----------|
| 2025-01-21 | Initial PIA | Project inception | Design Team |