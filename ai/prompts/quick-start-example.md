# Quick Start Example - Using the Prompt System

## Purpose

This file shows you exactly how to use the prompt system with Module 1 as a concrete example. Use this as a template for starting any module.

---

## Example: Starting Module 1 (System Design - REST API & DB Persistence)

### Step 1: Prepare Your Context

**Files you need:**

- ✅ `/ai/project.md` (your profile and learning goals)
- ✅ `/ai/module_1_api/module_1_system_design.md` (the module plan)
- ✅ `/ai/prompts/prompt-module-orchestrator.md` (the orchestrator prompt)

**What to verify:**

- [ ] `project.md` reflects your current profile
- [ ] You've read the module overview
- [ ] You understand the learning objectives
- [ ] You have time commitment available (30-40 hours)

---

### Step 2: Prepare the Orchestrator Prompt

**Open:** `prompt-module-orchestrator.md`

**Customize the prompt by replacing:**

Replace this:

```
## Context Files
- Project Overview: @project.md
- Module Plan: @[module_file_name].md
```

With this:

```
## Context Files
- Project Overview: @project.md
- Module Plan: @module_1_api/module_1_system_design.md
```

Replace this:

```
- Keep my profile in mind: {{leadership_background}}, {{technical_background}}, {{learning_goals}}
```

With this:

```
- Keep my profile in mind: 13+ years engineering leadership, Java backend background, goal to regain hands-on technical depth
```

---

### Step 3: Run the Orchestrator Prompt in Your AI Assistant

**Copy the full customized prompt and paste it into your AI assistant (like Cursor, ChatGPT, Claude, etc.)**

**Example prompt you'll run:**

```
I'm working on a technical learning project. I need you to create a comprehensive, actionable implementation plan based on the provided context.

## Context Files
- Project Overview: @project.md
- Module Plan: @module_1_api/module_1_system_design.md

## Your Task

Analyze the module plan and create a detailed, step-by-step implementation guide that will help me execute this module successfully.

## Requirements

### 1. Pre-Implementation Analysis
- Summarize the module's learning objectives in 2-3 sentences
- List specific prerequisites I need to complete before starting
- Identify any gaps between my current knowledge (from profile) and module requirements
- Estimate realistic time commitment based on my profile

### 2. Create Detailed Phase Plans
For each phase identified in the module (typically: Design, Implementation, Enhancement):

**Generate separate markdown files:**
- `phase_1_design.md` - Design phase details
- `phase_2_implementation.md` - Core implementation details
- `phase_3_enhancement.md` - Enhancement and optimization details

**Each phase file should include:**
- Clear objectives (what I'll accomplish)
- Detailed task breakdown (specific, actionable steps)
- Decision points (where I need to make architectural choices)
- Checkpoints (how to verify I'm on track)
- Estimated time for each task
- Links to relevant documentation/resources
- Common pitfalls to avoid

[... rest of orchestrator prompt ...]

## Additional Considerations

- Keep my profile in mind: 13+ years engineering leadership, Java backend background, goal to regain hands-on technical depth
- Focus on practical construction and hands-on learning
- Emphasize "why" and trade-offs, not just "how"
- Include reflection prompts at key milestones
- Connect concepts to real-world production systems
- Ensure complexity matches "intermediate" level

Generate the plan now.
```

---

### Step 4: Review the AI Output

**The AI should generate several markdown files:**

```
/ai/module_1_api/
  implementation_plan.md           ← Executive summary
  phase_1_design.md               ← Detailed design tasks
  phase_2_implementation.md       ← Detailed implementation tasks
  phase_3_enhancement.md          ← Detailed enhancement tasks
  break_and_observe.md            ← Exercise procedures
  design_decisions.md             ← Template for your decisions
  implementation_checklist.md     ← Deliverables checklist
```

**Review each file:**

- [ ] Do objectives align with module plan?
- [ ] Are time estimates realistic for you?
- [ ] Are tasks specific and actionable?
- [ ] Do you understand what's expected?
- [ ] Are prerequisites clear?

**If not satisfied:**

- Ask AI to regenerate specific sections
- Request more/less detail
- Adjust time estimates
- Add/remove tasks

---

### Step 5: Start Phase 1 (Design)

**Open:** `prompt-phase-1-design.md`

**Customize by replacing:**

```
I'm starting the Design Phase for [MODULE_NAME].
```

With:

```
I'm starting the Design Phase for Module 1: System Design - REST API & DB Persistence.
```

And:

```
## Context
- Project Overview: @project.md
- Module Plan: @[module_file_name].md
- Implementation Plan: @phase_1_design.md
```

With:

```
## Context
- Project Overview: @project.md
- Module Plan: @module_1_api/module_1_system_design.md
- Implementation Plan: @module_1_api/phase_1_design.md
```

And paste your objectives:

```
## Current Phase Goals
According to my phase plan, I need to accomplish:

[COPY OBJECTIVES FROM THE GENERATED phase_1_design.md FILE]
```

For example:

```
## Current Phase Goals
According to my phase plan, I need to accomplish:
- Create system architecture diagram showing NestJS layered architecture
- Define REST API contract with OpenAPI specification
- Design PostgreSQL data model for Notes entity
- Document all major technology and architectural decisions
- Create implementation roadmap with time estimates
```

**Then run the customized prompt in your AI assistant.**

---

### Step 6: Work Through Design Phase Interactively

**The AI will guide you through creating:**

1. **System Architecture Diagram**

   - AI asks questions about your components
   - You describe what you want to build
   - AI helps you create visual representation
   - Save in `/artifacts/architecture_diagram.png` (or `.drawio`, or ASCII art in `.md`)

2. **API Contract Specification**

   - AI helps you define endpoints
   - You specify request/response structures
   - AI generates OpenAPI spec
   - Save as `/artifacts/api_contract.yaml`

3. **Data Model**

   - AI guides entity design
   - You define fields and relationships
   - AI helps with ERD
   - Save in `/artifacts/data_model_erd.png`

4. **Design Decisions**
   - For each major choice (framework, database, patterns)
   - Fill template in `design_decisions.md`
   - Document trade-offs
   - AI helps analyze alternatives

**Example interaction:**

```
You: "Let's start with the system architecture diagram."

AI: "Great! Let's think about the main components. Based on Module 1,
you're building a REST API with NestJS and PostgreSQL.

What layers do you want in your architecture?
Typically we have:
- Controller Layer (handles HTTP requests)
- Service Layer (business logic)
- Repository Layer (data access)
- Database Layer (PostgreSQL)

Is this structure appropriate for your Notes API?"

You: "Yes, that makes sense. How should I handle validation?"

AI: "Validation typically happens at the controller boundary using
DTOs and validation pipes. Let me show you how that fits in...

[AI provides diagram and explanation]

Now, let's document this decision in your design_decisions.md..."
```

**Continue until all design artifacts are complete.**

---

### Step 7: Verify Design Phase Completion

**Checklist from phase_1_design.md:**

- [ ] System architecture diagram created
- [ ] API contract specification (OpenAPI) created
- [ ] Entity-relationship diagram created
- [ ] All major decisions documented in `design_decisions.md`
- [ ] Technology stack justified
- [ ] Implementation roadmap created
- [ ] Time estimates feel reasonable
- [ ] You understand the "why" behind each choice

**If complete, commit your work:**

```bash
cd /Users/dawid/Dev/Projects/Back2Code
git add ai/module_1_api/
git commit -m "Complete Module 1 design phase

- Created architecture diagram
- Defined API contracts
- Designed data model
- Documented key decisions"
```

---

### Step 8: Start Phase 2 (Implementation)

**Open:** `prompt-phase-2-implementation.md`

**Customize the same way:**

- Replace `[MODULE_NAME]` with actual module
- Add correct file paths
- Paste your Phase 2 objectives

**Example start of implementation:**

````
You: "I'm starting Phase 2: Implementation for Module 1.
Let's begin with Step 0: Project Setup.

Walk me through:
1. Creating the initial NestJS project structure
2. Installing necessary dependencies
3. Setting up configuration files
4. Getting a basic 'hello world' running"

AI: "Perfect! Let's set up your NestJS project.

Step 1: Create the project
Run this command:
```bash
npx @nestjs/cli new core-api
````

Choose npm or yarn when prompted.

This creates a basic NestJS structure:
core-api/
├── src/
│ ├── app.controller.ts
│ ├── app.service.ts
│ ├── app.module.ts
│ └── main.ts
├── test/
├── package.json
└── tsconfig.json

Step 2: Install dependencies for this module...
[AI continues with detailed guidance]

After each step, verify it works before proceeding to the next step."

```

**Continue working through:**
- Step 0: Project Setup
- Step 1: Database Layer
- Step 2: Repository Layer
- Step 3: Service Layer
- Step 4: API Layer
- Step 5: Testing
- Step 6: Error Handling
- Step 7: Documentation

**At each checkpoint:**
1. Verify it works
2. Run tests
3. Commit code
4. Proceed to next step

---

### Step 9: Continue Through Remaining Phases

**Phase 3: Enhancement**
- Use `prompt-phase-3-enhancement.md`
- Focus on 3-5 high-priority enhancements
- Measure performance improvements
- Polish code quality

**Phase 4: Break & Observe**
- Use `prompt-break-and-observe.md`
- Complete 5-8 failure exercises
- Document observations
- Extract resilience insights

**Phase 5: Postmortem**
- Use `prompt-postmortem-reflection.md`
- Deep reflection on learning
- Document insights
- Prepare for next module

---

## Example Timeline (Module 1)

### Week 1: Design & Setup
- **Monday:** Run orchestrator, review outputs (2 hours)
- **Tuesday-Wednesday:** Phase 1 - Design artifacts (8 hours)
- **Thursday-Friday:** Phase 2 Step 0-1 - Setup & Database (5 hours)

### Week 2: Core Implementation
- **Monday-Tuesday:** Phase 2 Step 2-3 - Repository & Service (8 hours)
- **Wednesday-Thursday:** Phase 2 Step 4-5 - API & Testing (8 hours)
- **Friday:** Phase 2 Step 6-7 - Error Handling & Docs (4 hours)

### Week 3: Enhancement & Testing
- **Monday-Tuesday:** Phase 3 - Enhancements (6 hours)
- **Wednesday-Thursday:** Phase 4 - Break & Observe (6 hours)
- **Friday:** Phase 5 - Postmortem (3 hours)

**Total:** ~50 hours over 3 weeks (moderate pace)

---

## Example File Tree After Completion

```

/Back2Code/
/ai/
/module_1_api/
module_1_system_design.md ← Original module plan
implementation_plan.md ← Generated by orchestrator
phase_1_design.md ← Generated by orchestrator
phase_2_implementation.md ← Generated by orchestrator
phase_3_enhancement.md ← Generated by orchestrator
break_and_observe.md ← Generated by orchestrator
design_decisions.md ← Filled by you during Phase 1
implementation_roadmap.md ← Filled by you during Phase 1
module_reflection.md ← Filled by you during Phase 5
lessons_learned.md ← Summary of key insights
/artifacts/
architecture_diagram.png
data_model_erd.png
api_contract.yaml
/break_observe_results/
exercise_1_database_failure.md
exercise_2_invalid_input.md
[... other exercises ...]
/core-api/ ← Your actual code
/src/
/common/
/config/
/notes/
notes.module.ts
notes.controller.ts
notes.service.ts
/entities/
note.entity.ts
/dto/
create-note.dto.ts
update-note.dto.ts
/test/
README.md
docker-compose.yml

```

---

## Tips for Your First Module

### Do's
✅ **Read the full module plan first** - Understand the big picture
✅ **Trust the process** - The phases are designed to build on each other
✅ **Ask AI questions** - Use it as your pair programming partner
✅ **Take breaks** - Especially between phases
✅ **Commit frequently** - Small, working increments
✅ **Document decisions** - Your future self will thank you
✅ **Complete reflection** - This is where learning solidifies

### Don'ts
❌ **Don't skip design** - It saves time later
❌ **Don't rush implementation** - Understanding matters more than speed
❌ **Don't skip tests** - They're your safety net
❌ **Don't skip Break & Observe** - This teaches resilience thinking
❌ **Don't skip reflection** - This is where learning becomes wisdom

### When You're Stuck

**Problem:** "The orchestrator output is too detailed/not detailed enough"
**Solution:** Ask AI to adjust - "Make this more concise" or "Add more detail to Step 3"

**Problem:** "I don't understand what Phase 1 is asking me to do"
**Solution:** Ask AI - "Explain what you mean by 'service boundaries' in my context"

**Problem:** "Implementation is taking much longer than estimated"
**Solution:** That's normal for first module. Track actual time, adjust future estimates

**Problem:** "I'm stuck on implementing [feature]"
**Solution:** Ask AI for specific help - "I'm trying to implement pagination. Here's what I have [code]. How do I add page/limit parameters?"

---

## Moving to Module 2

**After completing Module 1:**

1. ✅ Complete postmortem reflection
2. ✅ Commit and organize all files
3. ✅ Take 2-3 day break
4. ✅ Review lessons learned
5. ✅ Check next module prerequisites
6. 🔄 **Repeat this process for Module 2**

**For Module 2, you'll:**
- Run orchestrator with `@module_2_data/module_2_data_storage.md`
- Use same phase prompts (they're generic!)
- Build on what you learned in Module 1
- Go faster because you understand the process

---

## Quick Reference Card

### Starting Any Module

1. **Read** module plan file
2. **Run** orchestrator prompt (customized)
3. **Review** generated phase files
4. **Execute** Phase 1 (Design)
5. **Build** Phase 2 (Implementation)
6. **Polish** Phase 3 (Enhancement)
7. **Break** Phase 4 (Observe failures)
8. **Reflect** Phase 5 (Postmortem)
9. **Rest** before next module
10. **Repeat** for next module

### Key Customizations for Each Module

Replace in prompts:
- `[MODULE_NAME]` → actual module name
- `@[module_file_name].md` → actual module file path
- `[PASTE OBJECTIVES]` → copy from generated phase files
- Context file paths → actual paths

### Essential Files

**To read:**
- `prompts/README.md` (overall guide)
- `prompt-module-orchestrator.md` (start each module)
- Phase prompts (during execution)

**To generate:**
- Phase plan files (via orchestrator)
- Design artifacts (during Phase 1)
- Code (during Phase 2-3)
- Exercise results (during Phase 4)
- Reflection (during Phase 5)

---

## You're Ready!

This example should give you a clear picture of how to use the prompt system. The system is designed to:

- **Guide** without constraining
- **Structure** without prescribing
- **Support** without hand-holding
- **Teach** without lecturing

**Next step:** Run the orchestrator for Module 1 and start your learning journey!

**Remember:** This is YOUR learning journey. Adapt these prompts to serve YOU. The goal is deep understanding, not completing tasks quickly.

**Good luck!** 🚀

```
