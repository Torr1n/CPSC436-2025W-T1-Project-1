# Project 1: Privacy Impact Assessment Rubric

---

## CPSC 436C - 2025 Winter Term 1

## Purpose

This rubric operationalizes ethical considerations into concrete, gradeable deliverables that demonstrate critical thinking about surveillance implications in technical architecture.

---

## Section 1: Data Inventory (25 points)

### Excellent (23-25 points)

- Complete enumeration of ALL data collected (including metadata)
- Explicit identification of PII vs pseudonymous vs aggregated data
- Clear data flow diagram showing collection → storage → processing → access
- Identifies "hidden" data collection (e.g., CloudWatch logs, ALB access logs)
- Documents data that COULD be derived from collected data

### Good (18-22 points)

- Lists primary data fields collected
- Distinguishes between required and optional data
- Basic flow from collection to storage
- Acknowledges some metadata collection

### Insufficient (0-17 points)

- Vague descriptions ("user data", "analytics")
- Missing critical data types (IP addresses treated as "anonymous")
- No consideration of derived data

**Red Flag Check:** If student claims "no PII collected" while storing IPs, timestamps, and user agents → automatic deduction

---

## Section 2: Architectural Decisions & Trade-offs (30 points)

### Excellent (28-30 points)

```
# Student demonstrates alternatives like:
# Option A: Store raw IP – full tracking capability
# Option B: Hash IP with daily salt – session tracking without identification
# Option C: Country-only storage – regional insights without individual tracking
# Selected: Option B because [specific justification]
```

- Multiple alternatives presented WITH code snippets
- Clear rationale for each choice linking to requirements
- Acknowledges what surveillance capability each option enables
- Documents what business value is preserved/lost with each option

### Good (21-27 points)

- Presents at least one privacy-preserving alternative
- Explains trade-offs in technical terms
- Shows understanding of data minimization
- Links choices to free-tier constraints

### Insufficient (0-20 points)

- No alternatives considered
- Claims "had no choice" without exploring options
- Treats all requirements as non-negotiable

**Bonus Recognition:** Implementing differential privacy, k-anonymity thresholds, or client-side aggregation

---

## Section 3: Threat Modeling (25 points)

### Excellent (23-25 points)

Identifies specific threat scenarios:

- **Linkage attacks:** "IP + timestamp + referrer can re-identify users even without names"
- **Function creep:** "Analytics DB could be repurposed for user profiling"
- **Third-party leakage:** "GeoIP service gets user behavior patterns"
- **Insider threats:** "Any developer with DB access can extract user histories"
- **Retention risks:** "Logs kept 'for debugging' become surveillance archive"

Provides specific mitigations for each threat.

### Good (18-22 points)

- Identifies 2-3 concrete threats
- Understands basic re-identification risks
- Proposes some mitigations
- Considers data breach implications

### Insufficient (0-17 points)

- Generic threats ("hackers might steal data")
- No consideration of authorized but unethical use
- Ignores re-identification risks

---

## Section 4: Implementation Evidence (20 points)

### Excellent (19-20 points)

Git commits show:

```
commit: "Add data retention policy - auto-delete after 30 days"
commit: "Replace IP storage with hashed identifiers"
commit: "Implement query-level access controls"
commit: "Add data export functionality for GDPR compliance"
```

- Code demonstrates actual privacy features implemented
- Configuration shows non-default privacy settings
- README documents privacy decisions prominently

### Good (15-18 points)

- Some privacy features in code
- Basic documentation of choices
- Evidence of considering alternatives in commits

### Insufficient (0-14 points)

- No evidence of privacy considerations in code
- Default settings everywhere
- Claims without implementation

---

## Grading Anti-Patterns to Avoid

### ☐ Performative Compliance

"We value user privacy" → No points without specific implementation

### ☐ Impossibility Claims

"Can't do X because of free tier" → Deduction without exploring alternatives

### ☐ Checkbox Mentality

Listing GDPR articles → No points without applying to specific architecture

### ☐ What We're Looking For

- Struggling with real trade-offs
- Documenting uncomfortable compromises
- Showing work even when perfect privacy isn't achievable
- Creative solutions within constraints

---

## Bonus Opportunities (up to 10% additional)

### Privacy-First Implementation (+10%)

Successfully implement the API with minimal data collection while meeting ALL functional requirements. Document in detail what you chose NOT to collect and why the system still works.

### Resistance Documentation (+5%)

Include a file called `PRIVACY_STANCE.md` that:

- Documents every place you pushed back against surveillance
- Shows alternative designs you proposed
- Explains compromises you had to make and why
- Identifies who benefits from each data collection decision

### Ethics Gate Implementation (+5%)

Add a GitHub Action that:

- Fails the build if new data collection is added without updating PIA
- Checks for PII in logs
- Validates data retention policies are configured
- Requires privacy review approval for certain file changes

---

## Key Principle

We're not grading your ability to recite privacy principles.

**We're grading your ability to recognize surveillance infrastructure you're building and make conscious, documented decisions about it.**

The best submissions will show genuine struggle with these trade-offs, not smooth compliance theater.

---

## Sample Grading

**Submission A (85/100):**

- Thorough data inventory including derived data
- Three architectural alternatives with clear trade-offs
- Identified linkage attacks and insider threats
- Implemented 30-day retention and IP hashing
- Missing: Third-party leakage analysis

**Submission B (65/100):**

- Basic data inventory but missed metadata
- One alternative mentioned but not explored
- Generic threat model
- Claims privacy focus but code shows `SELECT *`
- No evidence of struggling with trade-offs

**Submission C (95/100 + 10% bonus):**

- Complete data inventory with flow diagrams
- Five alternatives with implementation sketches
- Sophisticated threat model including function creep
- Implemented privacy-first version with client-side aggregation
- `PRIVACY_STANCE.md` documents resistance points
- Git history shows iterative privacy improvements

---

## Discussion During Grading

TAs should ask themselves:

1.  Does this student understand what they built?
2.  Can they articulate who benefits and who pays?
3.  Did they explore alternatives or just accept requirements?
4.  Is there evidence of critical thinking vs compliance theater?
5.  Would they build this differently if it was tracking them?

This rubric rewards genuine engagement with ethical implications over performative compliance.
