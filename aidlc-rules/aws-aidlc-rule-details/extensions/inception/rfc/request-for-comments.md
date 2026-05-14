# Request for Comments (RFC) - Detailed Steps

## Purpose
**Structured deliberation for major product and architectural decisions on distributed teams**

Request for Comments focuses on:
- Framing high-impact decisions before commitments are made
- Surfacing and evaluating alternatives with explicit trade-offs
- Capturing stakeholder input and dissent in a durable, reviewable form
- Producing a decision record that downstream stages (Application Design, Units Generation, Construction) can rely on
- Reducing rework by aligning on direction *before* design and code are produced
- **Enabling asynchronous deliberation** so distributed and virtual teams can reach durable alignment without depending on synchronous meetings

**Note**: An RFC is a deliberation artifact, not an implementation artifact. It captures *what was decided and why*, so later stages do not relitigate settled questions.

## Primary Qualifying Criteria

An RFC is most valuable when **both** of the following are true:

1. **The decision is major** — high-impact, hard-to-reverse, cross-cutting, or otherwise meets one of the High Priority criteria below.
2. **The team is distributed or virtual, and synchronous communication is difficult** — stakeholders span time zones, work asynchronously, or cannot reliably gather in real time to deliberate.

When both conditions hold, the RFC stage is the primary alignment mechanism: it replaces the synchronous meeting with a durable, reviewable artifact that every stakeholder can engage with on their own time.

If only condition 1 holds (major decision, but the team is co-located and meets synchronously), an RFC is still recommended but the team may rely more on meeting notes and lightweight decision records.

If only condition 2 holds (distributed team, but the decision is low-stakes), an RFC is overkill — use a lightweight chat thread or doc comment.

## Extension Mode

This extension supports two enabled modes (set during opt-in and recorded in `aidlc-docs/aidlc-state.md` under `## Extension Configuration`):

- **Yes (gating)**: The RFC stage MUST execute for every qualifying decision identified during workflow planning. Qualifying decisions are those meeting the Primary Qualifying Criteria or the High Priority criteria below. Skipping the RFC stage for a qualifying decision is a blocking finding.
- **On-demand**: RFC rules are loaded and available, but the stage executes only when the user explicitly requests it for a specific decision. The model SHOULD proactively suggest invoking the RFC stage when it detects a qualifying decision, but MUST NOT block progress without user request.

If a qualifying decision is detected and the extension is in **On-demand** mode, the model MUST surface the recommendation to run the RFC stage and capture the user's response in `aidlc-docs/audit.md` before proceeding.

## When to Execute (Adaptive)

**See [depth-levels.md](../../../common/depth-levels.md) for adaptive depth explanation**

### High Priority Execution (Qualifying Decisions)
- **Architectural Decisions**: Choosing frameworks, datastores, runtime models, integration patterns, or service boundaries that are costly to reverse
- **Cross-System Changes**: Decisions spanning multiple systems, teams, or organizational boundaries
- **Strategic Product Direction**: Significant pivots in product scope, target users, business model, or roadmap
- **Public API Design**: Externally consumed contracts where breaking changes carry high cost
- **Compliance / Security Posture Changes**: Decisions with regulatory, legal, or security implications
- **Migration & Replacement**: Replacing core technologies, datastores, or platforms
- **Distributed Team Alignment**: Major decisions where stakeholders are distributed across time zones, work asynchronously, or cannot easily gather synchronously to deliberate

### Medium Priority Execution (Assess Reversibility & Blast Radius)
- **Internal API or Schema Changes** with multiple consumers
- **New Component Introduction** that establishes long-term patterns
- **Tooling or Process Decisions** that affect multiple teams
- **Performance / Scalability Strategy** when multiple viable approaches exist

### Skip Only For Low-Stakes Cases
- Decisions fully reversible within a single sprint
- Implementation details contained inside one component
- Bug fixes, refactors, or cleanups with no architectural implications
- Decisions already settled by an existing, current RFC
- Co-located teams making routine decisions that can be resolved in a single synchronous meeting

### Default Decision Rule
**When in doubt, write the RFC** — especially on a distributed or virtual team. A short RFC for a borderline decision is far cheaper than relitigating a major choice mid-construction or untangling a misalignment that crossed time zones for a week. See [overconfidence-prevention.md](../../../common/overconfidence-prevention.md) — overconfidence in skipping deliberation produces brittle decisions.

## Prerequisites
- Workspace Detection must be complete
- Reverse Engineering must be complete (if brownfield)
- Requirements Analysis recommended (provides problem context)
- User Stories optional (can inform RFCs scoped to user-facing decisions)

## Blocking RFC Finding Behavior

A **blocking RFC finding** means:
1. The finding MUST be listed in the stage completion message under an "RFC Findings" section with the RFC rule ID and description
2. The stage MUST NOT present the "Continue to Next Stage" option until all blocking findings are resolved
3. The model MUST present only the "Request Changes" option with a clear explanation of what needs to change
4. The finding MUST be logged in `aidlc-docs/audit.md` with the RFC rule ID, description, and stage context

If an RFC rule is not applicable to the current decision (e.g., RFC-04 when no migration is involved), mark it as **N/A** in the compliance summary — this is not a blocking finding.

---

# RULES

## Rule RFC-01: Decision Qualification Assessment

**Rule**: Before drafting an RFC, the decision under consideration MUST be assessed against the Primary Qualifying Criteria and the High Priority / Medium Priority criteria above. The assessment MUST be documented in `aidlc-docs/inception/plans/rfc-assessment.md` using the template below.

```markdown
# RFC Assessment

## Decision Under Consideration
- **Decision Statement**: [Single sentence describing the decision]
- **Triggering Request**: [Original user request or context]

## Impact Analysis
- **Blast Radius**: [Single Component / System / Multi-System / Organization]
- **Reversibility**: [Easily Reversible / Costly to Reverse / Effectively Permanent]
- **Stakeholders**: [List affected teams, roles, or external parties]
- **Time Horizon**: [How long will this decision constrain future work?]

## Team Communication Context
- **Team Distribution**: [Co-located / Hybrid / Fully Distributed / Fully Remote-Async]
- **Time Zone Spread**: [Single TZ / Adjacent TZs / Wide spread (>6 hours)]
- **Synchronous Communication Feasibility**: [Easy / Possible with effort / Difficult / Effectively impossible]
- **Why This Matters**: [Brief note on how communication context affects the need for an asynchronous deliberation artifact]

## Assessment Criteria Met
- [ ] Primary: Major decision AND distributed/async team — [Yes/No, with detail]
- [ ] High Priority: [List applicable criteria]
- [ ] Medium Priority: [List applicable criteria with reversibility justification]
- [ ] Benefits: [Expected value from running an RFC process]

## Decision
**Execute RFC**: [Yes/No]
**Reasoning**: [Detailed justification, including how team distribution influenced the decision]
**RFC Owner**: [Person responsible for driving the RFC to decision]
```

**Verification**:
- An `rfc-assessment.md` exists for the decision
- Blast radius and reversibility are explicitly stated
- Team distribution and synchronous communication feasibility are explicitly assessed
- An RFC owner is named
- If "No" is selected, the reasoning explicitly addresses each High Priority criterion that *does not* apply AND why the team's communication context does not warrant an async deliberation artifact

---

## Rule RFC-02: Mandatory Alternatives Analysis

**Rule**: Every RFC MUST analyze at least two real alternatives plus a "Do Nothing" option. Single-option framing is not permitted — an RFC that presents only one path has skipped the deliberation it exists to perform.

For each alternative, the analysis MUST capture:
- Description
- Pros (specific, not generic)
- Cons (specific, not generic)
- Cost (implementation, operational, opportunity)
- Risk (key failure modes)
- Reversibility
- Verdict (Recommended / Viable fallback / Rejected — with reason)

**Verification**:
- `alternatives.md` contains at least two non-trivial alternatives plus "Do Nothing"
- Each alternative has all required fields populated
- "Pros" and "Cons" reference specific aspects of the decision context, not generic platitudes
- The "Do Nothing" alternative documents the cost of inaction

---

## Rule RFC-03: Explicit Trade-off Acknowledgment

**Rule**: The RFC's recommended direction MUST explicitly state what is being given up. Every decision has costs — RFCs that present only benefits are incomplete.

**Verification**:
- `rfc.md` contains a "Trade-offs Accepted" section that names specific costs of the recommendation
- Trade-offs are concrete (e.g., "higher operational complexity", "vendor lock-in to X", "increased latency for Y workflow") — not generic
- The trade-offs are tied to the alternative(s) that would have avoided them

---

## Rule RFC-04: Reversibility and Migration Plan

**Rule**: Every RFC MUST document reversibility analysis and, where applicable, a back-out or migration plan.

- **Reversibility classification**: Easily Reversible / Costly to Reverse / Effectively Permanent
- **Back-out plan**: How the decision would be undone if it proves incorrect
- **Migration path**: If replacing an existing approach, how the transition is staged

If the decision is greenfield with no prior approach to migrate from, the migration path is N/A — but reversibility classification and back-out plan are still required.

**Verification**:
- `rfc.md` contains a "Reversibility & Migration" section
- Reversibility is classified
- A back-out plan is documented (or rationale for why none is feasible — and that itself is a risk to call out)
- Migration path is documented if replacing an existing approach

---

## Rule RFC-05: Risks, Unknowns, and Revisit Triggers

**Rule**: Every RFC MUST enumerate:
- **Risks**: What could go wrong, with likelihood and impact
- **Unknowns**: What is not yet known and how the team plans to learn
- **Open Questions**: Questions deferred to implementation or follow-up RFCs
- **Revisit Triggers**: Specific events or signals that should cause this decision to be reopened

Revisit triggers are mandatory — a decision without a revisit condition becomes permanently entrenched even when conditions change.

**Verification**:
- `rfc.md` contains "Risks & Unknowns" and "Reversibility & Migration" sections
- At least one revisit trigger is documented in `decision-record.md`
- Risks include both likelihood and impact (not just lists of bad outcomes)

---

## Rule RFC-06: Decision Record and Dissent Preservation

**Rule**: Every accepted, rejected, or superseded RFC MUST have a `decision-record.md` capturing:
- RFC ID and title
- Decision (single sentence)
- Decision date
- Decision owner
- Approvers and their roles (and time zones, where the team is distributed — this is useful audit context)
- **Dissenting views**: Any reviewer who disagreed, with their reasoning preserved honestly — do not summarize dissent away
- Conditions of approval (if any)
- Revisit triggers

Dissent preservation is non-negotiable. RFCs that erase minority views lose their value as a historical record. On distributed teams, where dissent may surface asynchronously and never in a single meeting, faithfully recording it is especially important.

**Verification**:
- `decision-record.md` exists with all required fields
- If any reviewer dissented, their views are captured verbatim or in a faithful summary they would endorse
- Status is one of: Draft / In Review / Accepted / Rejected / Superseded

---

## Rule RFC-07: Asynchronous Review Window

**Rule**: When the assessment in Rule RFC-01 indicates a distributed or asynchronous team, the RFC MUST allow an explicit review window of at least 48 hours (or longer if stakeholders span very wide time zones) before the decision is finalized. The window MUST be:

- Stated in the RFC metadata as the "Review Closes" date
- Communicated to all reviewers at the start of the window
- Long enough that every named reviewer has at least one full working day within their local time zone to engage

Decisions finalized faster than the review window denies async stakeholders their voice and undermines the purpose of using an RFC.

For co-located teams able to deliberate synchronously, this rule may be marked **N/A** in the compliance summary if the rationale (sync deliberation already occurred and is documented) is captured in `decision-record.md`.

**Verification**:
- `rfc.md` metadata includes "Review Closes" date
- The review window is at least 48 hours from the date the RFC entered "In Review" status
- For wide time-zone spread (>6 hours), the window is at least 5 working days
- If marked N/A, `decision-record.md` documents why an async window was not required

---

## Rule RFC-08: Artifact Consistency

**Rule**: The three RFC artifacts (`rfc.md`, `alternatives.md`, `decision-record.md`) MUST be consistent.

**Verification**:
- The recommendation in `rfc.md` matches the decision in `decision-record.md`
- Every alternative summarized in `rfc.md` appears with full analysis in `alternatives.md`
- The decision date in `decision-record.md` is on or after the RFC creation date in `rfc.md` and on or after the "Review Closes" date (if Rule RFC-07 applies)
- Status in `decision-record.md` matches the metadata block in `rfc.md`

---

# EXECUTION

## Stage Activation

When this extension is enabled in **Yes (gating)** mode, the RFC stage executes during the Inception phase after Requirements Analysis (or after User Stories, if executed) and before Workflow Planning. In **On-demand** mode, the stage executes when explicitly invoked.

The RFC stage has two parts: Planning and Generation.

---

## PART 1: PLANNING

### Step 1: Validate RFC Need (MANDATORY)

Apply Rule RFC-01. Create `aidlc-docs/inception/plans/rfc-assessment.md` and confirm the decision qualifies. If it does not qualify, document the reasoning and STOP — return control to the user.

### Step 2: Create RFC Plan
- Assume the role of a technical lead or principal engineer driving alignment
- Generate a comprehensive plan with step-by-step execution checklist for the RFC
- Each step and sub-step should have a checkbox []
- Focus on framing the problem, identifying alternatives, and structuring the deliberation
- For distributed teams, design the plan for asynchronous engagement — questions and review windows must accommodate stakeholders who cannot meet synchronously

### Step 3: Generate Context-Appropriate Questions

**DIRECTIVE**: Analyze the decision context to generate questions that surface the trade-offs, constraints, and assumptions a high-quality RFC must address. Use the categories below as guidance. When in doubt about whether a question is needed, ask it — premature confidence in a decision direction produces shallow RFCs.

**See [question-format-guide.md](../../../common/question-format-guide.md) for question formatting rules**

- EMBED questions using [Answer]: tag format
- Focus on ANY ambiguities, missing constraints, or unstated assumptions
- Generate questions wherever user input would change the set of viable alternatives or the evaluation criteria
- **When in doubt, ask the question** — overconfidence leads to weak RFCs

**Question categories to evaluate** (consider ALL categories):
- **Problem Framing** — What problem is being solved? What forces it now? What happens if we do nothing?
- **Goals & Non-Goals** — What outcomes must this decision produce? What is explicitly out of scope?
- **Constraints** — Technical, organizational, regulatory, budget, timeline, team-skill constraints
- **Stakeholders & Reviewers** — Who must approve? Who must be consulted? Who is informed? What time zones do they span?
- **Evaluation Criteria** — How will alternatives be compared (cost, risk, time-to-value, reversibility, operational burden)?
- **Alternatives Space** — What candidate approaches exist? What was rejected before reaching this RFC?
- **Risks & Unknowns** — What could go wrong? What do we *not* know yet?
- **Reversibility & Migration** — How would we back out? What is the cost of changing this later?
- **Success Signals** — How will we know the decision was correct (or wrong) after the fact?
- **Review Window** — How long should the async review remain open? Are there stakeholders on PTO or out of office?

### Step 4: Include Mandatory RFC Artifacts in Plan

- **ALWAYS** include these mandatory artifacts in the RFC plan:
  - [ ] Generate `rfc.md` containing the full RFC document (problem, alternatives, decision, consequences)
  - [ ] Generate `alternatives.md` capturing each option considered with trade-off analysis (Rule RFC-02)
  - [ ] Generate `decision-record.md` capturing the final decision, decision owner, reviewers, and dissent (Rule RFC-06)
  - [ ] Capture explicit non-goals
  - [ ] Capture risks, unknowns, and open questions (Rule RFC-05)
  - [ ] Capture reversibility analysis and migration/back-out plan (Rule RFC-04)
  - [ ] Define and communicate the asynchronous review window (Rule RFC-07)
  - [ ] Validate consistency across artifacts (Rule RFC-08)

### Step 5: Present Alternative-Evaluation Approaches

Include candidate approaches for structuring the alternatives analysis in the plan document:
- **Comparative Matrix**: Score each alternative against weighted evaluation criteria
- **Pros/Cons Per Option**: Narrative trade-off analysis without numeric scoring
- **Decision Tree**: Branch alternatives by key forcing constraints
- **Spike-Driven**: Identify alternatives that require a time-boxed prototype before deciding
- **Hybrid**: Combine matrix scoring with narrative trade-offs

Explain trade-offs of each. Allow hybrid approaches with clear rationale.

### Step 6: Store RFC Plan
- Save the complete RFC plan with embedded questions in `aidlc-docs/inception/plans/`
- Filename: `rfc-plan.md`
- Include all [Answer]: tags for user input
- Ensure plan covers problem framing, alternatives, evaluation, and decision capture

### Step 7: Request User Input
- Ask user to fill in all [Answer]: tags directly in the RFC plan document
- Emphasize that thin or hand-waved answers produce weak RFCs and downstream rework
- Provide clear instructions on completing the [Answer]: tags

### Step 8: Collect Answers
- Wait for user to provide answers to all questions using [Answer]: tags in the document
- Do not proceed until ALL [Answer]: tags are completed
- Review the document to ensure no [Answer]: tags are left blank

### Step 9: ANALYZE ANSWERS (MANDATORY)

Before proceeding, you MUST carefully review all user answers for:
- **Vague or ambiguous responses**: "mix of", "somewhere between", "not sure", "depends", "maybe"
- **Undefined criteria or terms**: References to concepts without clear definitions
- **Contradictory answers**: Responses that conflict with each other or with stated constraints
- **Missing decision details**: Answers that omit specific guidance needed to write the RFC
- **Single-option framing**: Responses that present a preferred path without engaging alternatives — push back and require alternatives (Rule RFC-02)
- **Unstated assumptions**: Answers that assume facts not in evidence (load, team capacity, vendor support)

### Step 10: MANDATORY Follow-up Questions

If the analysis in Step 9 reveals ANY ambiguous answers, you MUST:
- Add specific follow-up questions to the plan document using [Answer]: tags
- DO NOT proceed to RFC drafting until ALL ambiguities are resolved
- **CRITICAL**: An RFC with weak inputs produces a weak decision — be thorough
- Examples of required follow-ups:
  - "You named one alternative — what other approaches were considered, even briefly, and why were they ruled out?"
  - "You mentioned 'industry standard' — which specific standard, and which sources or peers establish it as standard for this context?"
  - "You said 'low risk' — what failure modes did you evaluate, and what is the recovery cost if each occurs?"
  - "You indicated 'we'll migrate later if needed' — what would trigger migration, and what is the estimated cost?"
  - "You chose option A — what evidence would change your mind toward option B?"

### Step 11: Avoid Premature Decisions
- Focus on framing, alternatives, and evaluation criteria — not advocacy
- Do not let the RFC plan presuppose the conclusion
- If a decision feels obvious, document *why* alternatives still need to be evaluated to validate that intuition
- Defer the recommendation until Part 2

### Step 12: Log Approval Prompt
- Before asking for approval, log the prompt with timestamp in `aidlc-docs/audit.md`
- Include the complete approval prompt text
- Use ISO 8601 timestamp format

### Step 13: Wait for Explicit Approval of Plan
- Do not proceed until the user explicitly approves the RFC plan and scope
- Approval must be clear and unambiguous
- If user requests changes, update the plan and repeat the approval process

### Step 14: Record Approval Response
- Log the user's approval response with timestamp in `aidlc-docs/audit.md`
- Include the exact user response text
- Mark the approval status clearly

---

## PART 2: GENERATION

### Step 15: Load RFC Plan
- [ ] Read the complete RFC plan from `aidlc-docs/inception/plans/rfc-plan.md`
- [ ] Identify the next uncompleted step (first [ ] checkbox)
- [ ] Load the context, answers, and constraints for that step

### Step 16: Draft the RFC Document

Create `aidlc-docs/inception/rfc/rfc.md` with the following sections:

```markdown
# RFC: [Title]

## Metadata
- **RFC ID**: [RFC-NNNN]
- **Status**: [Draft / In Review / Accepted / Rejected / Superseded]
- **Author(s)**: [Name(s)]
- **Created**: [YYYY-MM-DD]
- **Last Updated**: [YYYY-MM-DD]
- **Review Closes**: [YYYY-MM-DD] (per Rule RFC-07; mark N/A if co-located/sync team)
- **Reviewers**: [List of reviewers with roles and time zones]
- **Supersedes**: [Prior RFC ID, if any]

## Summary
[One paragraph: what is being decided and the recommended direction]

## Problem Statement
[What problem are we solving? What forces this decision now? What is the cost of inaction?]

## Goals
- [Specific, measurable outcome 1]
- [Specific, measurable outcome 2]

## Non-Goals
- [Explicitly out of scope item 1]
- [Explicitly out of scope item 2]

## Constraints & Assumptions
- **Constraints**: [Technical, organizational, regulatory, timeline, budget]
- **Assumptions**: [Stated assumptions the decision rests on]

## Alternatives Considered
[Reference `alternatives.md` and summarize each option here. For each: brief description, pros, cons, key risks.]

## Recommendation
[Recommended alternative with explicit rationale tied to the evaluation criteria]

## Trade-offs Accepted
[What we are giving up by choosing this path. Be honest — every decision has costs. (Rule RFC-03)]

## Risks & Unknowns
- **Risks**: [What could go wrong; likelihood and impact]
- **Unknowns**: [What we do not yet know; how we plan to learn]
- **Open Questions**: [Questions deferred to implementation or follow-up RFCs]

## Reversibility & Migration
- **Reversibility**: [Easily Reversible / Costly to Reverse / Effectively Permanent]
- **Back-out Plan**: [How we would undo this decision if needed]
- **Migration Path**: [If replacing existing approach, how we transition]

## Success Signals
- [Observable indicators that the decision is working]
- [Indicators that would suggest revisiting the decision]

## Implementation Implications
[High-level impact on Application Design, Units Generation, and Construction phases. Do NOT design the implementation here — just note what this decision constrains downstream.]

## References
- [Links to prior art, related RFCs, requirements docs, external research]
```

### Step 17: Draft the Alternatives Document

Create `aidlc-docs/inception/rfc/alternatives.md` per Rule RFC-02:

```markdown
# Alternatives Considered

## Alternative 1: [Name]
- **Description**: [What this approach entails]
- **Pros**: [Specific benefits]
- **Cons**: [Specific drawbacks]
- **Cost**: [Implementation cost, operational cost, opportunity cost]
- **Risk**: [Key failure modes]
- **Reversibility**: [How hard to change later]
- **Verdict**: [Recommended / Viable fallback / Rejected — with reason]

## Alternative 2: [Name]
[Same structure]

## Alternative N: Do Nothing
[Always include this option. Document the cost of inaction.]
```

### Step 18: Draft the Decision Record

Create `aidlc-docs/inception/rfc/decision-record.md` per Rule RFC-06:

```markdown
# Decision Record

- **RFC**: [RFC ID and title]
- **Decision**: [Single-sentence statement of what was chosen]
- **Decision Date**: [YYYY-MM-DD]
- **Decision Owner**: [Person accountable for the decision]
- **Approvers**: [Who signed off, with role and time zone]
- **Dissenting Views**: [Any reviewers who disagreed and their reasoning — preserve dissent honestly]
- **Conditions of Approval**: [Any conditions, follow-ups, or revisit triggers]
- **Revisit Triggers**: [Events or signals that should cause this decision to be reopened]
```

### Step 19: Validate RFC Consistency

Apply Rule RFC-08. Before presenting for approval, verify:
- [ ] `rfc.md` recommendation matches `decision-record.md` decision
- [ ] All alternatives in the RFC summary appear in `alternatives.md` with full analysis
- [ ] "Do Nothing" alternative is documented (Rule RFC-02)
- [ ] Non-goals are explicit, not implied
- [ ] At least one revisit trigger is defined (Rule RFC-05)
- [ ] Dissenting views are captured if any reviewer disagreed (Rule RFC-06)
- [ ] Asynchronous review window is documented or marked N/A with rationale (Rule RFC-07)
- [ ] Content validation rules from [content-validation.md](../../../common/content-validation.md) are satisfied (Mermaid syntax, ASCII diagrams, escaping)

### Step 20: Update Progress
- [ ] Mark completed steps as [x] in the RFC plan
- [ ] Update `aidlc-docs/aidlc-state.md` current status
- [ ] Save all generated artifacts under `aidlc-docs/inception/rfc/`

### Step 21: Log Approval Prompt
- Before asking for approval, log the prompt with timestamp in `aidlc-docs/audit.md`
- Include the complete approval prompt text
- Use ISO 8601 timestamp format

### Step 22: Present Completion Message

Present completion message in this structure:

1. **Completion Announcement** (mandatory): Always start with this:

```markdown
# 📜 Request for Comments Complete
```

2. **AI Summary** (optional): Provide structured bullet-point summary of the RFC
   - Format: "RFC has been drafted for [decision area]:"
   - State the decision under consideration
   - List alternatives evaluated (bullet points)
   - State the recommended direction and key trade-offs
   - Note reversibility, revisit triggers, and the async review window if applicable
   - DO NOT include workflow instructions ("please review", "let me know", "proceed to next phase")
   - Keep factual and content-focused

3. **RFC Compliance Section** (mandatory): Include a compliance summary listing each RFC rule (RFC-01 through RFC-08) as compliant, non-compliant, or N/A. Any non-compliant rule is a blocking finding — follow Blocking RFC Finding Behavior above.

4. **Formatted Workflow Message** (mandatory): Always end with this exact format:

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the RFC artifacts at: `aidlc-docs/inception/rfc/rfc.md`, `aidlc-docs/inception/rfc/alternatives.md`, and `aidlc-docs/inception/rfc/decision-record.md`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the RFC, alternatives analysis, or decision record  
> 🗣️ **Add Reviewers** - Identify additional stakeholders whose input is required before deciding  
> ⏳ **Open Async Review** - Begin the asynchronous review window per Rule RFC-07  
> ❌ **Reject Decision** - Mark the RFC as Rejected and document the reasoning  
> ✅ **Approve & Continue** - Approve the RFC and proceed to **[Workflow Planning / Application Design]**

---
```

### Step 23: Wait for Explicit Approval of RFC
- Do not proceed until the user explicitly approves the RFC
- Approval must be clear and unambiguous
- If the user requests changes, update the artifacts and repeat the approval process
- If the user rejects the decision, update the status in `decision-record.md` to `Rejected`, capture the reasoning, and STOP — do not proceed to subsequent stages
- If the user opens an async review window (Rule RFC-07), wait until the window closes and reviewer input is captured before requesting final approval

### Step 24: Record Approval Response
- Log the user's approval response with timestamp in `aidlc-docs/audit.md`
- Include the exact user response text
- Mark the approval status clearly (Accepted / Rejected / Changes Requested)

### Step 25: Update Progress
- Mark Request for Comments stage complete in `aidlc-docs/aidlc-state.md`
- Update the "Current Status" section
- If accepted, prepare for transition to next stage
- If rejected, return control to the user to determine next steps

---

# CRITICAL RULES

## Planning Phase Rules
- **CONTEXT-APPROPRIATE QUESTIONS**: Only ask questions relevant to this specific decision
- **MANDATORY ANSWER ANALYSIS**: Always analyze answers for ambiguities before drafting
- **NO PROCEEDING WITH AMBIGUITY**: Resolve all vague answers before generating the RFC
- **EXPLICIT APPROVAL REQUIRED**: User must approve the RFC plan before drafting begins

## Generation Phase Rules
- **NO HARDCODED CONCLUSIONS**: Do not write the RFC to justify a predetermined choice
- **ALTERNATIVES ARE MANDATORY**: At least two real alternatives plus "Do Nothing" must be analyzed (Rule RFC-02)
- **TRADE-OFFS ARE MANDATORY**: Every recommendation must explicitly state what is being given up (Rule RFC-03)
- **ASYNC REVIEW WINDOW**: Distributed/async teams must observe the review window in Rule RFC-07
- **PRESERVE DISSENT**: Capture dissenting reviewer views honestly — do not summarize them away (Rule RFC-06)
- **UPDATE CHECKBOXES**: Mark [x] immediately after completing each step
- **VERIFY COMPLETION**: Ensure all RFC artifacts are consistent before requesting approval (Rule RFC-08)

## Completion Criteria
- All planning questions answered and ambiguities resolved
- RFC plan explicitly approved by user
- All steps in RFC plan marked [x]
- All RFC artifacts generated (`rfc.md`, `alternatives.md`, `decision-record.md`)
- "Do Nothing" alternative documented
- Reversibility, risks, and revisit triggers captured
- Async review window observed or explicitly marked N/A with rationale
- RFC explicitly accepted, rejected, or changes-requested by user
- Status recorded in `decision-record.md` and `aidlc-state.md`

---

## Enforcement Integration

These rules apply to the Inception phase RFC stage. At this stage:
- Evaluate all RFC rule verification criteria against the artifacts produced
- Include an "RFC Compliance" section in the stage completion summary listing each rule as compliant, non-compliant, or N/A
- If any rule is non-compliant, this is a blocking RFC finding — follow the Blocking RFC Finding Behavior defined in the Overview
- Include RFC references in downstream design documentation when decisions made in the RFC constrain design choices
