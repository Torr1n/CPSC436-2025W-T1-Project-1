To: Claude, AI Cloud Architect
From: Lead Design Strategist
Subject: Architecture Brief - FileMind Document Type Prediction API Design

You are tasked with designing a scalable prediction API for construction document workflow optimization that must survive enterprise-wide deployment spikes while maintaining strict privacy standards and operating within minimal cost constraints.

**Context:** FileMind provides real-time predictions of the next document type a construction project manager will interact with based on recent file usage patterns. The system must scale from 100 requests/day to 50,000 requests/hour during enterprise deployments, while maintaining complete anonymization and p95 latency <200ms.

**Core Resources:**
- Design Templates: `Project_Spec_Template.md`, `pia_template.md`
- Architecture Guide: `Architecture_Diagram_Starter_Project_1.txt`
- Cost Toolkit: `cloud_toolkit.txt`
- Grading Rubrics: `Project_Design_Rubric.md`, `PIA_Grading_Rubric.md`

### Phase 1: Problem Definition & Requirements Analysis

Your first objective is to clearly define the document prediction problem and validate requirements for construction workflow optimization.

**Tasks:**

1. Define the user and decision context using the 20-minute spec recipe:
   - Users: Construction project managers and document controllers
   - Decision: Which document type to prepare/access next
   - Value: Reduced context switching, faster document retrieval
2. Specify the prediction target:
   - Categorical prediction over document types (drawings, RFIs, specifications, etc.)
   - Session-based predictions with 1-hour feature window
3. Document features available at prediction time (no leakage):
   - Document type interaction counts (past hour)
   - Temporal features (hour of day, day of week)
   - Action type distribution (read/modify/create)
   - Session duration indicators
4. Establish baseline: Predict most frequently used document type in past hour
5. Propose initial model: Decision tree or logistic regression on count features
6. Define online learning approach for continuous improvement
7. Validate requirements with construction workflow patterns

**Deliverables:**
- Completed Sections 1-5 of `Project_Spec_Template.md`
- Feature engineering documentation with leakage analysis
- Baseline vs. model comparison with expected accuracy gains
- API contract definition

== DESIGN REVIEW CHECKPOINT ==
STOP and await review of problem framing. Verify:
- Clear workflow augmentation value proposition
- No information leakage in features
- Practical baseline implementation
- Realistic accuracy expectations

### Phase 2: Architecture Design & Scaling Strategy

Design the system architecture optimized for enterprise deployment scenarios and viral adoption.

**Tasks:**

1. Design hybrid architecture components:
   - API Gateway with rate limiting (60/min per user)
   - Lambda functions for API and feature extraction
   - ECS Fargate for model serving (avoid cold starts)
   - DynamoDB for feature store (1-hour TTL)
   - S3 for model artifacts and training data
2. Create scaling strategy for 500x traffic surge:
   - Auto-scaling policies for ECS tasks (max 5 instances)
   - Multi-tier caching: API Gateway → Lambda → DynamoDB
   - Session-based feature caching (1-hour windows)
   - Graceful degradation to baseline model
3. Document cost projections:
   - Normal load: ~$5/month (Fargate minimum)
   - Pilot phase: ~$15/month
   - Enterprise scale: ~$50/month
   - Surge scenario: <$50 for 3-hour spike
4. Design online learning pipeline:
   - Feedback endpoint for actual user actions
   - Incremental model updates
   - A/B testing infrastructure
5. Implement monitoring and observability:
   - CloudWatch metrics for latency and errors
   - Model performance tracking
   - Usage pattern analytics

**Deliverables:**
- Architecture diagram with hybrid serverless/container design
- Scaling strategy with specific auto-scaling triggers
- Cost model with three scenarios (normal/growth/surge)
- API specification with predict and feedback endpoints
- Monitoring dashboard design

== DESIGN REVIEW CHECKPOINT ==
STOP and await architecture review. Verify:
- Hybrid architecture justification
- Model serving latency <50ms
- Cost within $100/month cap
- Online learning feasibility

### Phase 3: Privacy Impact Assessment & Security Design

Implement privacy-by-design principles for enterprise document workflows.

**Tasks:**

1. Complete Privacy Impact Assessment:
   - Data inventory: document types only, no content
   - Purpose limitation: workflow prediction only
   - Retention: 1-hour feature window, 90-day training data
   - Access control: Enterprise contract-based
2. Implement technical guardrails:
   - Immediate file path → type hashing
   - No user identifiers stored
   - Session isolation (no cross-session linking)
   - k≥5 for any data aggregation
   - Global model training (pooled data)
3. Design security architecture:
   - API key + HMAC signatures for FileMind agent
   - IAM roles for enterprise admins
   - Least privilege for development team
   - No public endpoint exposure
4. Create compliance framework:
   - NDA requirements for enterprise data
   - Contract templates for data usage
   - Quarterly privacy audits
   - Right to deletion process
5. Design telemetry matrix:
   - Model performance metrics (no PII)
   - System health monitoring
   - Aggregated usage patterns
   - Cost tracking

**Deliverables:**
- Completed PIA using `pia_template.md`
- Telemetry decision matrix (value vs. invasiveness)
- Security architecture documentation
- Compliance checklist for enterprise contracts
- Data governance policies

== DESIGN REVIEW CHECKPOINT ==
STOP and await privacy review. Verify:
- No PII collection or storage
- Enterprise data protection
- Ethical considerations addressed
- Transparency mechanisms

### Phase 4: Validation Planning & Documentation

Define comprehensive validation strategy for design and model performance.

**Tasks:**

1. Design evaluation experiments:
   - Baseline: Most frequent document type (40% accuracy)
   - Initial model: Decision tree (target 55% accuracy)
   - Metric: Cross-entropy loss for classification
   - Secondary: Top-3 accuracy for practical utility
2. Create data collection plan:
   - 1-week beta test with volunteer users
   - Time-based train/test split (80/20)
   - Cross-validation across different projects
   - Feedback loop implementation
3. Define SLA measurement:
   - Latency: p95 <200ms, p99 <500ms
   - Availability: 99.5% uptime
   - Model inference: <50ms
   - Cache hit rate: >70%
4. Design load testing scenarios:
   - Gradual ramp: 100 → 10,000 req/day
   - Spike test: Instant 50,000 req/hour
   - Sustained load: 5,000 req/hour for 24 hours
   - Failure recovery testing
5. Document acceptance criteria:
   - All Project_Spec_Template sections complete
   - PIA approved with all guardrails
   - Architecture handles scaling requirements
   - Cost within budget constraints
   - Model beats baseline by >15%

**Deliverables:**
- Comprehensive evaluation plan
- Data collection protocol
- Load testing scenarios and scripts
- Complete project documentation package
- Git history showing iterative design

== FINAL DESIGN VALIDATION ==
Design package complete. Submit for final review with:
- All 11 sections of Project Spec completed
- Architecture validated for scale and cost
- Privacy guardrails implemented and documented
- Validation experiments clearly defined
- Evolution demonstrated through git commits

### Success Criteria Summary

Your design will be evaluated against these criteria:

1. **Problem Clarity (20%)**: Clear user value, non-obvious insights
2. **Specification Quality (20%)**: No leakage, coherent API, justified metrics
3. **Privacy Excellence (20%)**: Comprehensive PIA, thoughtful guardrails
4. **Architecture Feasibility (15%)**: Scalable, cost-effective, explicit trade-offs
5. **Validation Rigor (15%)**: Clear experiments, measurable success
6. **Documentation (10%)**: Clear communication, git evolution

### Key Design Constraints

Remember these non-negotiable constraints:
- **Scaling:** 500x surge capability (100/day → 50,000/hour)
- **Privacy:** No PII, session-only, k≥5 anonymity
- **Cost:** <$100/month cap, ~$5 normal operations
- **Performance:** p95 <200ms, model inference <50ms
- **Ethics:** Transparent about automation potential

### Final Notes

This is a design-only project. Focus on:
- Clear architectural decisions with rationale
- Explicit privacy and ethical considerations
- Realistic cost and performance trade-offs
- Comprehensive validation planning
- No implementation code required

Consult SubAgents strategically:
- Use context-synthesizer for requirements validation
- Use api-docs-synthesizer for pattern verification
- Use codex-consultant for architecture trade-offs

Your success depends on demonstrating thoughtful design decisions, not implementation complexity.