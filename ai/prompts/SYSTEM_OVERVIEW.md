# Prompt System Overview - Visual Guide

## The Complete Learning System

This document provides a visual overview of how all the prompts work together to guide you through each module.

---

## The Big Picture

```
┌─────────────────────────────────────────────────────────────────┐
│                       YOUR LEARNING JOURNEY                      │
│                                                                  │
│  Project Context (project.md)                                   │
│         +                                                        │
│  Module Plan (module_X_[name].md)                              │
│         ↓                                                        │
│  ┌──────────────────────────────────────────────────┐          │
│  │     ORCHESTRATOR PROMPT                           │          │
│  │     Generates detailed implementation plan        │          │
│  └──────────────────────────────────────────────────┘          │
│         ↓                                                        │
│  ┌──────────────────────────────────────────────────┐          │
│  │     Generated Phase Files                         │          │
│  │     • phase_1_design.md                          │          │
│  │     • phase_2_implementation.md                  │          │
│  │     • phase_3_enhancement.md                     │          │
│  │     • break_and_observe.md                       │          │
│  │     • templates & checklists                     │          │
│  └──────────────────────────────────────────────────┘          │
│         ↓                                                        │
│  ┌──────────────────────────────────────────────────┐          │
│  │     PHASE-SPECIFIC PROMPTS                       │          │
│  │     Guide execution of each phase                │          │
│  └──────────────────────────────────────────────────┘          │
│         ↓                                                        │
│  ┌──────────────────────────────────────────────────┐          │
│  │     YOUR IMPLEMENTATION                          │          │
│  │     • Design artifacts                            │          │
│  │     • Working code                                │          │
│  │     • Tests and documentation                     │          │
│  │     • Exercise results                            │          │
│  └──────────────────────────────────────────────────┘          │
│         ↓                                                        │
│  ┌──────────────────────────────────────────────────┐          │
│  │     POSTMORTEM PROMPT                            │          │
│  │     Extract and solidify learning                │          │
│  └──────────────────────────────────────────────────┘          │
│         ↓                                                        │
│  ┌──────────────────────────────────────────────────┐          │
│  │     DEEP UNDERSTANDING & NEXT MODULE             │          │
│  └──────────────────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────────┘
```

---

## Prompt Files Relationship Map

```
prompts/
│
├── README.md ──────────────────────────► Overall guide & reference
│                                         Read this first!
│
├── SYSTEM_OVERVIEW.md ─────────────────► Visual/conceptual map
│                                         (This file)
│
├── quick-start-example.md ─────────────► Concrete Module 1 example
│                                         Follow this to get started
│
├── prompt-module-orchestrator.md ──────► ENTRY POINT for each module
│       │                                 Run this first!
│       │
│       ├─► Generates: phase_1_design.md
│       ├─► Generates: phase_2_implementation.md
│       ├─► Generates: phase_3_enhancement.md
│       ├─► Generates: break_and_observe.md
│       └─► Generates: templates & checklists
│
├── prompt-phase-1-design.md ───────────► Use with generated phase_1_design.md
│       │                                 Guide design phase execution
│       └─► Output: Design artifacts, decisions
│
├── prompt-phase-2-implementation.md ───► Use with generated phase_2_implementation.md
│       │                                 Guide implementation step-by-step
│       └─► Output: Working code, tests
│
├── prompt-phase-3-enhancement.md ──────► Use with generated phase_3_enhancement.md
│       │                                 Guide optimization & polish
│       └─► Output: Enhanced features, performance improvements
│
├── prompt-break-and-observe.md ────────► Use with generated break_and_observe.md
│       │                                 Guide failure exercises
│       └─► Output: Exercise results, resilience insights
│
└── prompt-postmortem-reflection.md ────► Use after module completion
        │                                 Guide deep reflection
        └─► Output: Module reflection, lessons learned
```

---

## Module Execution Flow

### Stage 0: Module Preparation

```
┌─────────────────────────────────────┐
│ Read module plan                     │
│ • Understand objectives              │
│ • Check prerequisites                │
│ • Review time commitment             │
└──────────────┬──────────────────────┘
               ↓
┌─────────────────────────────────────┐
│ Run ORCHESTRATOR prompt              │
│ • Provide @project.md                │
│ • Provide @module_X.md               │
│ • Review generated files             │
└──────────────┬──────────────────────┘
               ↓
┌─────────────────────────────────────┐
│ Outputs Generated:                   │
│ ✓ phase_1_design.md                 │
│ ✓ phase_2_implementation.md         │
│ ✓ phase_3_enhancement.md            │
│ ✓ break_and_observe.md              │
│ ✓ design_decisions.md (template)    │
│ ✓ implementation_checklist.md       │
└──────────────┬──────────────────────┘
               ↓
       Ready to start Phase 1!
```

---

### Stage 1: Design Phase (8-12 hours)

```
Use: prompt-phase-1-design.md
With: generated phase_1_design.md

┌────────────────────────────────────┐
│ Architecture Design                 │
│ • System diagram                    │
│ • Component interactions            │
│ • Service boundaries                │
└──────────────┬─────────────────────┘
               ↓
┌────────────────────────────────────┐
│ API Contract Specification          │
│ • Define endpoints                  │
│ • Request/response schemas          │
│ • OpenAPI spec                      │
└──────────────┬─────────────────────┘
               ↓
┌────────────────────────────────────┐
│ Data Model Design                   │
│ • Entities & relationships          │
│ • ERD diagram                       │
│ • Field types & constraints         │
└──────────────┬─────────────────────┘
               ↓
┌────────────────────────────────────┐
│ Decision Documentation              │
│ • Technology choices                │
│ • Architecture decisions            │
│ • Trade-offs analysis               │
└──────────────┬─────────────────────┘
               ↓
         Design Complete!
    Ready for implementation
```

**AI Interaction Pattern:**

```
You: "Help me create architecture diagram"
AI: Asks questions, guides design
You: Make decisions with AI help
AI: Validates, suggests improvements
You: Document in design_decisions.md
```

---

### Stage 2: Implementation Phase (16-20 hours)

```
Use: prompt-phase-2-implementation.md
With: generated phase_2_implementation.md

Step 0: Setup          → Checkpoint: Hello world works
   ↓
Step 1: Database       → Checkpoint: Can query DB
   ↓
Step 2: Repository     → Checkpoint: Tests pass
   ↓
Step 3: Service        → Checkpoint: Business logic works
   ↓
Step 4: API/Controller → Checkpoint: Endpoints respond
   ↓
Step 5: Integration    → Checkpoint: Full stack works
   ↓
Step 6: Error Handling → Checkpoint: Failures handled
   ↓
Step 7: Documentation  → Checkpoint: Others can use it
   ↓
   Implementation Complete!
   All tests passing
```

**AI Interaction Pattern:**

```
You: "Help me implement Step X"
AI: Provides template/example
You: Implement with guidance
AI: Helps debug issues
You: Test and verify
You: Commit working code
AI: Confirms checkpoint met
You: → Move to next step
```

---

### Stage 3: Enhancement Phase (4-6 hours)

```
Use: prompt-phase-3-enhancement.md
With: generated phase_3_enhancement.md

┌────────────────────────────────────┐
│ Performance Optimization            │
│ • Measure baseline                  │
│ • Add indexes                       │
│ • Optimize queries                  │
│ • Measure improvements              │
└──────────────┬─────────────────────┘
               ↓
┌────────────────────────────────────┐
│ Advanced Features                   │
│ • Pagination                        │
│ • Filtering & search                │
│ • Bulk operations                   │
└──────────────┬─────────────────────┘
               ↓
┌────────────────────────────────────┐
│ Production Readiness                │
│ • Structured logging                │
│ • Health checks                     │
│ • Rate limiting                     │
└──────────────┬─────────────────────┘
               ↓
┌────────────────────────────────────┐
│ Code Quality                        │
│ • Refactoring                       │
│ • Test coverage                     │
│ • Documentation polish              │
└──────────────┬─────────────────────┘
               ↓
     Enhancement Complete!
     Production-ready code
```

---

### Stage 4: Break & Observe Phase (4-6 hours)

```
Use: prompt-break-and-observe.md
With: generated break_and_observe.md

For each exercise (repeat 5-8 times):

1. Prepare
   ↓
2. Break Something
   ↓
3. Observe Behavior
   ↓
4. Document Findings
   ↓
5. Analyze Why
   ↓
6. Identify Improvements
   ↓
7. Restore System
   ↓
   Exercise Complete!

Exercise Categories:
• Infrastructure failures
• Invalid input / attacks
• Concurrency issues
• Performance under load
• Data integrity

↓
All Exercises Complete!
Resilience insights captured
```

**Example Exercise Flow:**

```
Exercise: Database Unavailability

┌──────────────────────────────────────┐
│ Before: System healthy, tests pass   │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ Break: Stop database container        │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ Observe: API returns 500, logs show  │
│ connection error, app doesn't crash  │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ Document: Error messages, behavior   │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ Learn: Need connection retry,        │
│ circuit breaker, health monitoring   │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ Restore: Start database, verify OK   │
└──────────────────────────────────────┘
```

---

### Stage 5: Postmortem Phase (2-4 hours)

```
Use: prompt-postmortem-reflection.md

Work through sections with AI:

1. Module Summary
   • Time spent vs estimated
   • Deliverables completed
   ↓
2. What Worked Well
   • Technical successes
   • Effective learning methods
   ↓
3. What Was Challenging
   • Conceptual difficulties
   • Technical obstacles
   ↓
4. Key Technical Insights
   • Deep learnings
   • "Aha!" moments
   ↓
5. Design Decisions Review
   • Were choices correct?
   • What would I change?
   ↓
6. Break & Observe Learnings
   • Resilience insights
   • Failure mode understanding
   ↓
7. Real-World Connection
   • Production relevance
   • Leadership application
   ↓
8. What I'd Do Differently
   • Concrete improvements
   • Better approaches
   ↓
9. Questions for Further Exploration
   • What to learn next
   • Deeper dives needed
   ↓
10. Preparation for Next Module
    • Readiness assessment
    • Skill gaps to address
    ↓
    Reflection Complete!
    Learning solidified
    Ready for next module
```

**AI Interaction Style:**

```
AI: "What was most challenging conceptually?"
You: "Understanding when to use service vs repository layer"
AI: "Why was that difficult? What helped it click?"
You: "Initially seemed arbitrary, but when I tried to test..."
AI: "So the testing perspective made it clear. How does this
     connect to separation of concerns in systems you've led?"
You: [deeper reflection]
AI: "What would you tell a team member about this?"
You: [articulates learning clearly]
```

---

## Decision Tree: Which Prompt to Use?

```
┌─────────────────────────────────────┐
│ Where are you in the module?        │
└─────────────┬───────────────────────┘
              ↓
         ┌────┴────┐
         │ START?  │
         └────┬────┘
              ↓ YES
    ┌─────────────────────┐
    │ Use ORCHESTRATOR    │
    │ prompt to generate  │
    │ phase files         │
    └─────────────────────┘

         ┌────┴────┐
         │DESIGN?  │
         └────┬────┘
              ↓ YES
    ┌─────────────────────┐
    │ Use Phase 1         │
    │ Design Prompt       │
    └─────────────────────┘

         ┌────┴────┐
         │ CODE?   │
         └────┬────┘
              ↓ YES
    ┌─────────────────────┐
    │ Use Phase 2         │
    │ Implementation      │
    └─────────────────────┘

         ┌────┴────┐
         │ENHANCE? │
         └────┬────┘
              ↓ YES
    ┌─────────────────────┐
    │ Use Phase 3         │
    │ Enhancement         │
    └─────────────────────┘

         ┌────┴────┐
         │ BREAK?  │
         └────┬────┘
              ↓ YES
    ┌─────────────────────┐
    │ Use Break &         │
    │ Observe Prompt      │
    └─────────────────────┘

         ┌────┴────┐
         │ DONE?   │
         └────┬────┘
              ↓ YES
    ┌─────────────────────┐
    │ Use Postmortem      │
    │ Reflection Prompt   │
    └─────────────────────┘
              ↓
    ┌─────────────────────┐
    │ Start Next Module!  │
    │ → Back to START     │
    └─────────────────────┘
```

---

## Files You Create vs Files AI Generates

### AI Generates (via Orchestrator)

```
✨ implementation_plan.md
✨ phase_1_design.md
✨ phase_2_implementation.md
✨ phase_3_enhancement.md
✨ break_and_observe.md
✨ design_decisions.md (template)
✨ implementation_checklist.md
```

### You Fill In (during phases)

```
✍️ design_decisions.md (with actual decisions)
✍️ /artifacts/architecture_diagram.png
✍️ /artifacts/api_contract.yaml
✍️ /artifacts/data_model_erd.png
✍️ implementation_roadmap.md
✍️ /break_observe_results/*.md
✍️ module_reflection.md
✍️ lessons_learned.md
```

### You Build (the actual project)

```
💻 /[service-name]/src/**
💻 /[service-name]/test/**
💻 docker-compose.yml
💻 README.md
💻 [all your code]
```

---

## Information Flow Through System

```
┌──────────────────────────────────────────────────────────────┐
│                     INPUT: Your Context                       │
│                                                               │
│  • Your profile (project.md)                                 │
│  • Module objectives (module_X.md)                           │
│  • Your learning goals & constraints                         │
└───────────────────────┬──────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────────────┐
│                 PROCESS: Orchestrator                         │
│                                                               │
│  Analyzes context and generates:                             │
│  • Detailed task breakdown                                   │
│  • Time estimates                                            │
│  • Decision points                                           │
│  • Checkpoints                                               │
│  • Resources                                                 │
└───────────────────────┬──────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────────────┐
│              OUTPUT: Actionable Phase Plans                   │
│                                                               │
│  Phase plans guide you through:                              │
│  • What to build                                             │
│  • How to build it                                           │
│  • How to verify it works                                    │
│  • When to move forward                                      │
└───────────────────────┬──────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────────────┐
│           EXECUTION: Phase-Specific Prompts                   │
│                                                               │
│  Interactive guidance:                                        │
│  • Step-by-step instructions                                 │
│  • Code examples                                             │
│  • Debugging help                                            │
│  • Best practices                                            │
└───────────────────────┬──────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────────────┐
│            CREATION: Your Implementation                      │
│                                                               │
│  You build:                                                   │
│  • Design artifacts                                          │
│  • Working code                                              │
│  • Tests                                                     │
│  • Documentation                                             │
└───────────────────────┬──────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────────────┐
│           VALIDATION: Break & Observe                         │
│                                                               │
│  You test:                                                    │
│  • Failure modes                                             │
│  • Edge cases                                                │
│  • Performance boundaries                                    │
│  • Resilience patterns                                       │
└───────────────────────┬──────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────────────┐
│            REFLECTION: Postmortem                             │
│                                                               │
│  You extract:                                                 │
│  • Technical insights                                        │
│  • Process learnings                                         │
│  • Career connections                                        │
│  • Improvements for next time                                │
└───────────────────────┬──────────────────────────────────────┘
                        ↓
┌──────────────────────────────────────────────────────────────┐
│         OUTCOME: Deep Understanding + Next Steps              │
│                                                               │
│  You have:                                                    │
│  • Solid technical skills                                    │
│  • Production perspective                                    │
│  • Leadership insights                                       │
│  • Readiness for next module                                 │
└──────────────────────────────────────────────────────────────┘
```

---

## Time Investment Per Module

```
Typical Module Timeline (40 hours total)

Phase 0: Preparation        ║████░░░░░░░░░░░░░░░░  2 hours (5%)
Phase 1: Design             ║███████████░░░░░░░░░░ 10 hours (25%)
Phase 2: Implementation     ║████████████████████  18 hours (45%)
Phase 3: Enhancement        ║██████░░░░░░░░░░░░░░   6 hours (15%)
Phase 4: Break & Observe    ║████░░░░░░░░░░░░░░░░   4 hours (10%)
Phase 5: Postmortem         ║░░░░░░░░░░░░░░░░░░░░   0 hours (0%)
                            └────────────────────────────────────
                                    0        20        40 hours

Pacing Options:

Intensive:  ████████████████████  1-2 weeks (8-10 hours/day)
Balanced:   ████████░░░░░░░░░░░░  2-4 weeks (2-3 hours/day)
Casual:     ████░░░░░░░░░░░░░░░░  4-8 weeks (1 hour/day)
```

---

## Success Indicators

### You're on track when:

**Design Phase:**

- ✅ Can explain architecture to someone else
- ✅ Understand why you chose each technology
- ✅ Have clear picture of what to build

**Implementation Phase:**

- ✅ Tests passing at each checkpoint
- ✅ Understand code you're writing
- ✅ Can explain design patterns used

**Enhancement Phase:**

- ✅ Can measure performance improvements
- ✅ Code quality improving
- ✅ System feels more robust

**Break & Observe Phase:**

- ✅ Surprised by some behaviors
- ✅ Understanding failure modes
- ✅ Generating improvement ideas

**Postmortem Phase:**

- ✅ Articulating concrete learnings
- ✅ Connecting to real-world systems
- ✅ Identifying growth areas

---

## Getting Help Within the System

```
Issue: "I don't understand what the prompt is asking"
Solution: Ask AI to explain in simpler terms
Example: "Can you explain what 'service boundaries' means
         in the context of my notes API?"

Issue: "The generated phase plan is too detailed/vague"
Solution: Ask AI to adjust
Example: "Make the implementation steps more detailed" or
         "Simplify this to high-level tasks only"

Issue: "I'm stuck on implementation"
Solution: Use the implementation prompt for specific help
Example: "I'm implementing pagination. Here's my code [paste].
         It's not working because [error]. Help me fix it."

Issue: "I don't know if my design is good"
Solution: Ask AI to validate
Example: "Review my architecture design. Does it follow
         best practices? What could be improved?"

Issue: "Break & Observe exercise didn't work as expected"
Solution: Ask AI to explain the behavior
Example: "I stopped the database but the API didn't return
         500, it returned 200 with cached data. Why?"
```

---

## The Philosophy Behind This System

### Why This Structure?

1. **Theory → Design → Build → Break → Reflect**

   - Matches how senior engineers approach problems
   - Each phase builds on previous
   - Learning compounds

2. **Generic Prompts, Specific Outputs**

   - Write prompts once, use for all modules
   - Adapt to your context automatically
   - Consistent structure, varied content

3. **AI as Guide, You as Builder**

   - AI explains, you implement
   - AI validates, you decide
   - AI supports, you learn

4. **Documentation-Driven Learning**

   - Writing solidifies understanding
   - Decisions captured for reflection
   - Creates reference for future

5. **Practical Construction**
   - Learn by building real things
   - No toy examples
   - Production-quality practices

---

## Quick Reference: One-Page Cheat Sheet

```
┌──────────────────────────────────────────────────────────────┐
│                MODULE EXECUTION CHECKLIST                     │
├──────────────────────────────────────────────────────────────┤
│                                                               │
│ □ Read module plan (module_X.md)                            │
│ □ Run orchestrator → get phase files                        │
│ □ Phase 1: Design                                           │
│   □ Architecture diagram                                    │
│   □ API contracts                                           │
│   □ Data model                                              │
│   □ Document decisions                                      │
│ □ Phase 2: Implementation                                   │
│   □ Setup project                                           │
│   □ Build incrementally                                     │
│   □ Test at each step                                       │
│   □ Handle errors                                           │
│ □ Phase 3: Enhancement                                      │
│   □ Add 3-5 optimizations                                   │
│   □ Measure improvements                                    │
│   □ Polish quality                                          │
│ □ Phase 4: Break & Observe                                  │
│   □ Run 5-8 failure exercises                               │
│   □ Document behavior                                       │
│   □ Extract insights                                        │
│ □ Phase 5: Postmortem                                       │
│   □ Deep reflection                                         │
│   □ Document learnings                                      │
│   □ Prepare for next                                        │
│                                                               │
│ ✓ Module Complete!                                           │
│   → Take break → Start next module                           │
└──────────────────────────────────────────────────────────────┘
```

---

## Where to Go Next

1. **Start Learning:**

   - Read `README.md` for complete guide
   - Follow `quick-start-example.md` for Module 1
   - Run orchestrator to begin

2. **Reference During Work:**

   - This file for big picture
   - Specific prompt files for guidance
   - Generated phase files for tasks

3. **Need Help:**
   - AI can explain any part
   - Prompts are starting points, adapt freely
   - Focus on learning, not following perfectly

---

**You now have a complete map of the learning system. Ready to start your journey!** 🚀
