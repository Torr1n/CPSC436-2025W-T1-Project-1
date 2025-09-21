# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a CPSC 436 Cloud Computing course repository focused on designing scalable cloud architectures. The current focus is Project 1: a design-only exercise for creating a scalable prediction API that can handle viral traffic spikes while maintaining privacy and cost constraints.

## Project Context

### Project 1: Scalable Prediction API (Design-Only)
- **Goal**: Design (not implement) a prediction API that can scale from 100 requests/day to 50,000 requests/hour
- **Focus**: Architecture, privacy-by-design, cost modeling, and trade-off analysis
- **No coding required**: This is a design and specification project

## Key Design Considerations

### Architecture Components
- API Gateway/Ingress
- Compute (serverless vs. containers vs. hybrid)
- Data Store
- Auth Provider
- Observability/Telemetry
- Cache layers for surge handling

### Privacy & Ethics Requirements
- Implement k-anonymity thresholds
- Data retention policies
- Access boundaries
- Telemetry decision matrix (value vs. invasiveness)
- Reciprocity mechanisms

### Cost Constraints
- Design for free tier under normal load (100 req/day)
- Handle viral surges within budget through:
  - Caching strategies
  - Pre-warming
  - Degradation modes
  - Rate limiting

## Required Deliverables

1. **Project Spec** (2-3 pages using `Project_Spec_Template.md`)
   - User/decision definition
   - Target/horizon specification
   - Features (no leakage)
   - Baseline → model progression
   - Metrics/SLA definition
   - API sketch

2. **Privacy Impact Assessment (PIA)**
   - Use `pia_template.md` as guide
   - Include telemetry decision matrix
   - Define guardrails

3. **Architecture Diagram**
   - Show component relationships
   - Document trade-offs
   - Include data flow

4. **Insight Artifacts**
   - Insight memo (`insight_memo.md`)
   - Assumption audit (`assumption_audit.md`)
   - Socratic log (`socratic_log.md`)

## Project Ideas Reference

Available pre-defined project ideas in `Project1_Ideas.md`:
- SafeRoute: Walking safety predictor
- StudyBuddy: Assignment submission predictor
- SurgeRisk: Traffic surge predictor
- QueueETA: Queue wait time estimator
- And others...

## Evaluation Criteria

Projects are assessed on:
- Problem framing and user clarity
- Specification completeness
- Privacy/ethics integration
- Architecture feasibility
- Risk identification
- Documentation quality

## Resource Toolkit

Reference `cloud_toolkit.txt` for:
- GitHub Student Developer Pack ($13,000+ in credits)
- AWS/GCP/Azure free tiers
- UBC CS computing resources
- Budget-conscious alternatives

## Important Notes

- This is a solo project to establish individual baselines
- Focus on design decisions and trade-offs, not implementation
- AI collaboration should be documented in the Socratic log
- Git history should show design evolution