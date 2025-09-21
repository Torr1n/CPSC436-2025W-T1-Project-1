# Project 1 (Design-Only): The Scalable

# Prediction API

Weeks 3-5

**Collaboration:** Solo work (establish individual baselines)

Acronyms? See the course Glossary & Acronyms page for definitions (e.g., PPrivacy Impact Assessment (PIA), Service-Level Agreement (SLA), Time-To-Live (TTL), AUC-PR, MAE).

# Project at a Glance

·Goal: Design (not implement) a scalable prediction API that can survive a viral spike and respect privacy.

**·Context:** Open-ended by design. You will define what to predict,for whom, and why it matters.

**·Skills** **Practiced:** Problem framing, target selection, metrics/SLA, privacy-by-design,cost thinking, architecture trade-offs, AI collaboration.

**·Deliverables:** Project spec (2-3 pages), PIA excerpt+telemetry matrix, architecture sketch, evaluation plan, insight artifacts, git hash showing design evolution.

# Design-Only

**No** **coding** **required.** Project 1 evaluates your intellectual work: problem framing, specifi-cation clarity, privacy/ethics guardrails, trade-off reasoning, and a minimal evaluation plan.Prototypes are optional and not graded. If you build anything, do not let it replace the required design artifacts.

# 1 Why This Matters

In real startups, architecture choices under pressure often determine survival. This project simulates that reality: you'll learn to balance cost, scalability, and security before the crisis hits.

# 2 Scenario: Your Viral Moment (Design Lens)

You will design a prediction API for a user and decision you choose. Assume usage can surge from 100 requests/day to 50,000 requests/hour. Your design should define what to predict,how to measure success, and how to keep users safe.

<!-- 1 -->

# 3 Learning Objectives

By completing this project, you will:

·Frame a prediction problem tied to a real decision and user.

·Specify a target and horizon without leakage; select metrics and an SLA.

·Propose a baseline and a simple model; define a minimal evaluation.

·Design privacy guardrails (k-anonymity,jitter/aggregation, retention, access) and reci-procity.

·Compare serverless vs. container approaches for unpredictable traffic and cost.

·Practice professional AI collaboration (Socratic) that changes your design.

# 4 Choosing Your Prediction Target

Use this 20-minuite recipe to scope your project:

1. **User** $\&$  **Decision:** Who uses the prediction? What decision does it inform?

2. **Target** $\&$  **Horizon:** Binary/numeric/ETA and when it is defined (e.g.,next 5 minutes,this session, by deadline).

3. **Features** **(no** **leakage):** Only what's available at prediction time; state exclusions.

4. **Baseline** **→** **Model:** A rule you can defend; one simple model and why it might help.

5. **Metrics** **&** **SLA:** Harms-aware metric $\mathrm {c}+\mathrm {p}9$ latency budget and cost envelope.

6. **Privacy** $\&$  **Reciprocity:** **PIA** excerpt, telemetry matrix, guardrails (k, jitter,retention),value returned.

7. **Architecture** **Sketch:** Components and data flow; trade-offs and alternatives.

**8.** **Minimal** **Experiment:** How you'll validate baseline vs. model (offline ok) and measure SLA.

Need inspiration? See projects/Project1_Ideas.md for ready-to-use targets (e.g., SafeR-oute, StudyBuddy,SurgeRisk, etc.).

<!-- 2 -->

# 5 AI Collaboration (Socratic)

This is a design project; AI is your thinking partner.

·Include a **Socratic** **Log:** 1 design-alternatives prompt, 1 red-team prompt, and the in-flection point (what changed and why).

·Document one poor AI suggestion and how you corrected it.

·Tie prompts to concrete design changes (metrics/SLA, privacy guardrails, architecture).

# 6 Resource & Cost Constraints

**·Free** **Tier** **Mindset:** Under normal load (100 req/day), your design should map to free-tier or near-free costs.

·Spikes: Explain how your design adapts to viral surges within budget (caching,pre-warm,degradation).

·Resource Guide: See Professional Cloud Development Toolkit.

·Trade-offs: Compare serverless vs. containers vs. hybrid for your case.

<!-- 3 -->

# 7 Deliverables (Design-Only)

What to Submit

**1) Project Spec (2-3 pages) using projects/Project_Spec_Template.md**

·User/decision,target/horizon, features (no leakage), baseline→model,metrics/SLA,API sketch.

**2)Privacy/Ethics/Reciprocity**

·PIA excerpt (link full PIA) + telemetry decision matrix.

·Guardrails: k-anonymity threshold, jitter/aggregation, retention, access; disclosure text.

**3) Architecture & Evaluation**

·One diagram with components and data flow; trade-offs and alternatives.

·Minimal experiment plan (baseline vs. model, offline ok); SLA/cost measurement plan.

**4) Insight Artifacts**

·Insight memo (3 insights), assumption audit, Socratic log references.

·A git hash (or tag/range) showing design evolution(commits -to README/spec/PIA/diagrams are sufficient; code commits are optional).

Diagram help: You can start from the course Architecture Diagram Starter (Project 1) page,which includes a Mermaid skeleton and a checklist.

# What Is Not Required

·Running code, deployed systems, or benchmarks (you may include mock measurements if helpful).

·Full model training; a clear baseline and simple model plan with an evaluation approach is sufficient.

# 8 Key Design Decisions

Address the following in your spec:

·Target **&** Metrics: No leakage; harms-aware metric; SLA and cost budget.

·Privacy: k-anonymity suppression, jitter/aggregation, retention, access boundaries, reci-procity.

<!-- 4 -->

·Compute: Serverless vs. containers vs. hybrid with cost and scaling trade-offs.

·Traffic: Rate limmits, caching tiers; degradation modes under surge.

·Auth: API keys/OAuth/IAM; align with privacy and cost.

·Observability: Minimal telemetry consistent with PIA.

# 9 Grading Criteria

See projects/Project_Design Rubric.md and PIA_Grading Rubric.md. In short, you will be assessed on:

·Insight $\&$  Framing: Clear user/decision; non-obvious insights.

·Specification Clarity:Target/horizon/features, API, metrics/SLA.

·Baseline & Evaluation **Plan**: Minimal experiment; SLA/cost plan.

·Privacy/Ethics/Reciprocity: PIA quality, guardrails integrated, reciprocity.

·Architecture $\&$  Feasibility: Diagram, trade-offs,alternatives.

·Risks $\&$  Measurement: Top risks with concrete tests.

·Evolution $\&$  Communication: Git evidence; clarity; insight artifacts.

# 10 Checkpoints

·Spec Clinic (in-class): 10-minute check on target/horizon, leakage, metrics/SLA.

·Bi-Weekly Check-in: Two questions on ethics impact and assumption tests.

·Final Submission: $Spec+artifacts+git$ hash as listed above.

<!-- 5 -->

