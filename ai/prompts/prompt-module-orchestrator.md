# Module Orchestrator Prompt

## Purpose

This prompt takes the project context and a module description to generate an actionable, detailed implementation plan broken into separate phases.

---

## Main Orchestrator Prompt

```
I'm working on a technical learning project. I need you to create a comprehensive, actionable implementation plan based on the provided context.

## Context Files
- Project Overview: @project.md
- Module Plan: @[module_file_name].mdvis

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

### 3. Create Supporting Artifacts
Generate templates for:
- `design_decisions.md` - Template for documenting architectural decisions
- `break_and_observe.md` - Detailed exercises with procedures
- `implementation_checklist.md` - Checklist of all deliverables

### 4. Learning Strategy
- Recommend reading order for theory materials
- Suggest which concepts to learn deeply vs understand broadly
- Identify practical exercises to solidify understanding
- Propose metrics to measure progress

### 5. Next Steps
Provide a clear, ordered list of immediate next actions to start this module.

## Output Format

1. **Executive Summary** (200-300 words)
   - Module overview
   - Key deliverables
   - Time commitment
   - Prerequisites check

2. **Phase Breakdown** (separate files)
   - Create individual markdown files for each phase
   - Include specific implementation steps
   - Add code structure examples where relevant

3. **Quick Start Guide** (in main file)
   - First 3 actions to take today
   - First milestone to achieve this week
   - How to know when each phase is complete

4. **Resource Package**
   - Prioritized reading list
   - Video tutorials (if applicable)
   - Documentation links
   - Tool setup guides

## Additional Considerations

- Keep my profile in mind: {{leadership_background}}, {{technical_background}}, {{learning_goals}}
- Focus on practical construction and hands-on learning
- Emphasize "why" and trade-offs, not just "how"
- Include reflection prompts at key milestones
- Connect concepts to real-world production systems
- Ensure complexity matches "intermediate" level

## Expected Deliverables

After running this prompt, I should have:
1. A clear understanding of what I'm building
2. Step-by-step tasks I can start immediately
3. Decision frameworks for architectural choices
4. Checkpoints to validate progress
5. Resources organized by learning phase
6. Templates ready to fill in as I work

Generate the plan now.
```

---

## How to Use This Prompt

### Step 1: Prepare Context

1. Ensure `project.md` is up to date with your profile and goals
2. Have the specific module file ready (e.g., `module_1_system_design.md`)
3. Review the module overview before running the prompt

### Step 2: Run the Orchestrator

1. Copy the prompt above
2. Replace `@[module_file_name].md` with actual module file
3. Replace `{{leadership_background}}`, etc. with your specifics from project.md
4. Run in AI assistant

### Step 3: Review Output

1. Check that all phase files are generated
2. Verify time estimates match your availability
3. Ensure prerequisites are clear
4. Review first milestone for achievability

### Step 4: Proceed to Phase Prompts

Once you have the plan, use phase-specific prompts (see next files) to execute each phase.

---

## Expected File Structure After Running

```
/ai/
  /module_X_[name]/
    module_plan.md                    # The original module description
    implementation_plan.md            # Executive summary from orchestrator
    phase_1_design.md                 # Design phase details
    phase_2_implementation.md         # Implementation phase details
    phase_3_enhancement.md            # Enhancement phase details
    design_decisions.md               # Template for decisions
    break_and_observe.md              # Exercises with procedures
    implementation_checklist.md       # Deliverables checklist
    /artifacts/                       # Diagrams, specs created during work
    /reflections/                     # Learning notes and insights
```

---

## Customization Options

### For Different Module Types

- **Design-Heavy Modules:** Request more architecture diagrams and decision templates
- **Implementation-Heavy Modules:** Request more code examples and structure guidance
- **Theory-Heavy Modules:** Request reading guides and concept maps

### For Different Time Constraints

- **Intensive Mode:** Compress phases, focus on essentials
- **Deep Dive Mode:** Add optional explorations, more research time
- **Standard Mode:** Follow module estimates as-is

### For Different Learning Styles

- **Visual Learners:** Request diagrams, architecture drawings, flowcharts
- **Hands-On Learners:** Request more coding exercises, fewer reading materials
- **Theoretical Learners:** Request deeper conceptual explanations, more research papers

---

## Validation Checklist

After receiving orchestrator output, verify:

- [ ] Phase 1 has clear, actionable first steps
- [ ] Each task has estimated time
- [ ] Decision points are identified with options listed
- [ ] Success criteria for each phase are clear
- [ ] Resources are linked and accessible
- [ ] File structure matches expected output
- [ ] Next steps are immediately actionable
- [ ] Checkpoints help track progress

If any items are missing, ask AI to generate them specifically.

---

## Tips for Best Results

1. **Be Specific About Context:** The more accurate your profile in project.md, the better the plan
2. **Review Before Starting:** Don't blindly follow - adjust to your situation
3. **Iterate on Output:** If something isn't clear, ask AI to elaborate
4. **Update as You Go:** Actual time spent may differ from estimates
5. **Use Templates:** Fill in design_decisions.md as you make choices
6. **Track Progress:** Use implementation_checklist.md to stay organized

---

## Next File

See `prompt-phase-1-design.md` for detailed design phase execution prompt.
