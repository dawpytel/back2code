# Phase 1: Design Phase Prompt

## Purpose

This prompt guides you through the design phase of any module, helping you create architectural artifacts before writing code.

---

## Design Phase Prompt

````
I'm starting the Design Phase for [MODULE_NAME]. Help me create comprehensive design artifacts that will guide my implementation.

## Context
- Project Overview: @project.md
- Module Plan: @[module_file_name].md
- Implementation Plan: @phase_1_design.md

## Current Phase Goals
According to my phase plan, I need to accomplish:
[PASTE PHASE 1 OBJECTIVES FROM YOUR PHASE FILE]

## Your Task

Guide me through creating detailed design artifacts. Work step-by-step, one artifact at a time, ensuring I understand each decision before moving to the next.

### 1. Architecture Design

**Create System Architecture Diagram:**
- Visual representation (ASCII or describe structure for tool like draw.io)
- Show all major components and their interactions
- Identify service boundaries
- Show data flow between components
- Include external dependencies (databases, message queues, etc.)

**Questions to address:**
- What are the main components/services?
- How do they communicate?
- What are the trust boundaries?
- Where is state managed?
- What can fail and how?

### 2. API Contract Specification

**For REST APIs:**
- List all endpoints with HTTP methods
- Define request/response schemas
- Document query parameters, headers
- Specify status codes for success/error cases
- Include example requests and responses

**For other interfaces:**
- Define interface contracts
- Document message formats
- Specify protocols used

**Deliverable:** OpenAPI/Swagger spec or equivalent formal contract

### 3. Data Model Design

**Create Entity-Relationship Diagram:**
- Define all entities/collections
- Show relationships and cardinality
- Document field names, types, constraints
- Identify primary keys, foreign keys
- Note indexes needed for performance

**Questions to address:**
- What data needs to be persisted?
- How is data related?
- What queries will be common?
- Where is denormalization beneficial?
- How will data migrate/evolve?

### 4. Decision Documentation

**For each significant architectural decision:**

Create entries in `design_decisions.md` using this format:

```markdown
# Decision: [Concise Title]
**Date:** [YYYY-MM-DD]
**Status:** Proposed

## Context
What problem or requirement drives this decision?
What constraints exist?

## Options Considered
1. **Option A:** [Description]
   - Pros: ...
   - Cons: ...

2. **Option B:** [Description]
   - Pros: ...
   - Cons: ...

3. **Option C:** [Description]
   - Pros: ...
   - Cons: ...

## Decision
We will use [chosen option] because [rationale].

## Consequences
### Positive
- What becomes easier?
- What problems are solved?

### Negative
- What becomes harder?
- What new challenges emerge?
- What technical debt is accepted?

## Validation
How will we know if this was the right choice?
What metrics or outcomes will we measure?
````

**Key decisions to document:**

- Framework/library choices
- Database selection and schema approach
- Communication patterns
- Error handling strategy
- Testing approach
- Deployment model

### 5. Technology Stack Justification

**Create a comparison table:**

| Component         | Options Evaluated | Choice Made | Justification |
| ----------------- | ----------------- | ----------- | ------------- |
| [e.g., Framework] | [A, B, C]         | [Choice]    | [Why]         |
| [e.g., Database]  | [A, B, C]         | [Choice]    | [Why]         |
| ...               | ...               | ...         | ...           |

For each choice, explain:

- Why it fits the learning objectives
- How it supports the module goals
- What trade-offs are accepted
- What you'll learn by using it

### 6. Risk Assessment

**Identify potential risks:**

| Risk                    | Likelihood | Impact | Mitigation Strategy |
| ----------------------- | ---------- | ------ | ------------------- |
| [e.g., Complex setup]   | Medium     | High   | [Plan]              |
| [e.g., Unfamiliar tech] | High       | Medium | [Plan]              |

### 7. Success Criteria

**Define what "done" looks like for implementation phase:**

- [ ] Functional requirements met
- [ ] Non-functional requirements met
- [ ] Tests pass with X% coverage
- [ ] Documentation complete
- [ ] "Break & Observe" exercises possible

### 8. Implementation Roadmap

**Create high-level task sequence:**

1. Setup (environment, dependencies) - X hours
2. [Component A] - X hours
3. [Component B] - X hours
4. Integration - X hours
5. Testing - X hours
6. Documentation - X hours

**Total estimated time:** X hours

## Guided Process

**Step 1:** Start with architecture diagram

- Work with me to identify components
- Ask clarifying questions about scope
- Validate against module objectives

**Step 2:** Design data model

- Help me choose appropriate data structures
- Identify relationships and constraints
- Consider query patterns

**Step 3:** Define API contracts

- Ensure RESTful design (or appropriate pattern)
- Validate against best practices
- Consider versioning and evolution

**Step 4:** Document decisions

- For each major choice, create decision record
- Challenge assumptions
- Identify trade-offs

**Step 5:** Validate design

- Check against module learning objectives
- Ensure appropriate complexity
- Verify all prerequisites are clear

## Interactive Approach

Please work with me interactively:

1. Present one artifact template at a time
2. Ask me questions to fill in details
3. Offer guidance on best practices
4. Validate my choices against module goals
5. Point out potential issues early
6. Help me document rationale

## Deliverables Checklist

At the end of this phase, I should have:

- [ ] System architecture diagram (saved in `/artifacts/`)
- [ ] API contract specification (OpenAPI/Swagger file)
- [ ] Entity-relationship diagram (saved in `/artifacts/`)
- [ ] Design decisions documented (in `design_decisions.md`)
- [ ] Technology stack table with justifications
- [ ] Risk assessment matrix
- [ ] Success criteria defined
- [ ] Implementation roadmap with time estimates

## When to Move to Next Phase

Only proceed to Phase 2 (Implementation) when:

- All design artifacts are complete
- Major architectural decisions are documented
- You understand the "why" behind each choice
- Implementation roadmap is clear
- You've reviewed design with critical eye
- Time estimates feel reasonable

## Common Pitfalls to Avoid

- **Over-designing:** Don't add complexity not required by module
- **Under-designing:** Don't skip hard decisions - address them now
- **Analysis paralysis:** Set time box, make best decision with current info
- **Ignoring learning goals:** Design should serve module objectives
- **Skipping documentation:** Future you needs to understand why

---

## Example Decision to Document

Based on this module, you'll make decisions like:

- REST vs GraphQL vs gRPC
- SQL vs NoSQL vs both
- Synchronous vs asynchronous processing
- Optimistic vs pessimistic locking
- Stateful vs stateless design

Each deserves a decision record.

---

## Next Steps After Design Phase

Once design phase is complete:

1. Review all artifacts for consistency
2. Get feedback (self-review or peer review)
3. Update estimates based on design insights
4. Proceed to Phase 2 using `prompt-phase-2-implementation.md`

---

Let's begin! Start by helping me create the system architecture diagram for [MODULE_NAME].

```

---

## How to Use This Prompt

### Preparation
1. Complete the Module Orchestrator first
2. Have `phase_1_design.md` file generated and reviewed
3. Set up `/artifacts/` folder for diagrams
4. Have `design_decisions.md` template ready

### Execution
1. Replace `[MODULE_NAME]` with actual module name
2. Paste Phase 1 objectives from your generated phase file
3. Work through each artifact interactively with AI
4. Save artifacts as you create them
5. Use the decision template for each major choice

### Time Management
- Set a time box for design phase (typically 8-12 hours)
- Don't get stuck in analysis paralysis
- Document decisions quickly, refine later if needed
- Use timer for each section

### Quality Checks
- Review each artifact against module objectives
- Ensure design supports learning goals
- Verify complexity is appropriate
- Check that all decisions are documented

---

## Tips for Success

1. **Start High-Level:** Begin with architecture, then go detailed
2. **Ask "Why" Three Times:** For each choice, understand rationale deeply
3. **Draw First, Describe Later:** Visual models help clarify thinking
4. **Compare Options:** Don't just pick first option - evaluate alternatives
5. **Think About Failure:** Design for what can go wrong
6. **Consider Evolution:** How will this design change over time?
7. **Link to Learning:** Explicitly connect design choices to module objectives

---

## Output Organization

Save artifacts in structured way:

```

/module*X*[name]/
/artifacts/
architecture_diagram.png (or .drawio)
data_model_erd.png
api_contract.yaml (OpenAPI spec)
sequence_diagrams/ (if needed)
design_decisions.md
implementation_roadmap.md

```

---

## Next Prompt
After completing design phase, proceed to `prompt-phase-2-implementation.md`

```
