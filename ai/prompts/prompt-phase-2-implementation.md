# Phase 2: Implementation Phase Prompt

## Purpose

This prompt guides you through the implementation phase, translating design artifacts into working code with tests and documentation.

---

## Implementation Phase Prompt

```
I'm starting the Implementation Phase for [MODULE_NAME]. Help me build this module step-by-step, ensuring I understand each piece before moving forward.

## Context
- Project Overview: @project.md
- Module Plan: @[module_file_name].md
- Design Artifacts: @design_decisions.md and files in /artifacts/
- Implementation Plan: @phase_2_implementation.md

## Current Status
- ✅ Design phase completed
- ✅ Architecture diagrams created
- ✅ API contracts defined
- ✅ Data model designed
- ✅ Technology decisions documented

## Implementation Phase Goals
[PASTE PHASE 2 OBJECTIVES FROM YOUR PHASE FILE]

## Your Task

Guide me through implementation in a structured, incremental way. Break down each component into digestible steps. After each step, help me verify it works before proceeding.

### Implementation Approach

**Principles:**
1. **Incremental Development:** Build one component at a time
2. **Test as You Go:** Write tests immediately after implementation
3. **Commit Frequently:** Working code at each checkpoint
4. **Document Inline:** Explain "why" in code comments
5. **Validate Early:** Run and test each piece before moving on

### Step-by-Step Guidance Needed

For each implementation step, provide:

1. **What to Build:**
   - Clear description of component/feature
   - Where it fits in architecture
   - Dependencies on other components

2. **How to Build It:**
   - Code structure and organization
   - Key design patterns to apply
   - Best practices to follow
   - Common mistakes to avoid

3. **Code Examples:**
   - Starter templates
   - Integration examples
   - Configuration samples

4. **Testing Strategy:**
   - What to test
   - How to test it
   - Sample test cases

5. **Validation Checkpoint:**
   - How to verify it works
   - What output/behavior to expect
   - When to move to next step

## Implementation Sequence

### Step 0: Project Setup (ALWAYS START HERE)

**Tasks:**
1. Initialize project structure
2. Install dependencies
3. Configure development environment
4. Setup version control
5. Create Docker configuration (if applicable)
6. Verify basic "hello world" works

**Help me with:**
- Recommended folder structure
- package.json/requirements.txt/etc setup
- Environment configuration (.env files)
- Docker Compose configuration
- Initial README structure

**Checkpoint:** Can run empty application successfully

---

### Step 1: Database/Storage Layer

**Tasks:**
1. Setup database connection
2. Create entity definitions
3. Write and run migrations
4. Verify database schema
5. Test basic CRUD operations

**Help me with:**
- Database connection configuration
- Entity/model definitions based on my ERD
- Migration scripts
- Connection pooling setup
- Initial seed data (if needed)

**Checkpoint:** Can connect to database and perform basic queries

---

### Step 2: Repository/Data Access Layer

**Tasks:**
1. Create repository interfaces/classes
2. Implement basic CRUD operations
3. Add query methods per design
4. Handle database errors
5. Write repository unit tests

**Help me with:**
- Repository pattern implementation
- Query optimization techniques
- Error handling strategies
- Mocking database for tests
- Test data builders

**Checkpoint:** Repository tests pass, can perform all planned queries

---

### Step 3: Service/Business Logic Layer

**Tasks:**
1. Create service classes
2. Implement business logic
3. Add validation rules
4. Handle edge cases
5. Write service unit tests (with mocked repositories)

**Help me with:**
- Service architecture patterns
- Business logic organization
- Validation strategies
- Error handling and exceptions
- Dependency injection setup

**Checkpoint:** Service tests pass with mocked dependencies

---

### Step 4: API/Controller Layer

**Tasks:**
1. Create controllers/route handlers
2. Define request/response DTOs
3. Add input validation
4. Implement error handling middleware
5. Setup API documentation (Swagger/etc)

**Help me with:**
- Controller structure
- DTO design and validation
- HTTP status codes
- Request/response transformation
- API documentation annotations

**Checkpoint:** Can call API endpoints and get expected responses

---

### Step 5: Integration & E2E Testing

**Tasks:**
1. Setup test environment
2. Write integration tests (all layers)
3. Write E2E tests (critical flows)
4. Test error scenarios
5. Achieve target code coverage

**Help me with:**
- Test database setup
- Integration test structure
- E2E test scenarios
- Coverage measurement
- CI/CD considerations

**Checkpoint:** All tests pass, coverage meets target (80%+)

---

### Step 6: Error Handling & Logging

**Tasks:**
1. Implement global error handler
2. Add structured logging
3. Add request tracing/correlation IDs
4. Create health check endpoints
5. Test failure scenarios

**Help me with:**
- Error handling middleware
- Logging best practices
- Correlation ID implementation
- Health check design
- Observability basics

**Checkpoint:** Errors handled gracefully, logs are useful

---

### Step 7: Documentation & Polish

**Tasks:**
1. Complete README with setup instructions
2. Document API endpoints
3. Add code comments for complex logic
4. Create example requests (Postman/curl)
5. Document known limitations

**Help me with:**
- README structure
- API documentation generation
- Code documentation standards
- Creating useful examples

**Checkpoint:** Another developer could setup and use your project

---

## Interactive Implementation Style

**For each step above:**

1. **Before Starting:**
   - Review what we're building
   - Show me file structure for this component
   - Explain how it fits with what we've built

2. **During Implementation:**
   - Provide code template or example
   - Explain key concepts and patterns
   - Point out best practices
   - Warn about common pitfalls

3. **After Completing:**
   - Help me write tests
   - Guide verification process
   - Identify what could be improved
   - Confirm ready for next step

4. **If I Get Stuck:**
   - Help debug issues
   - Explain error messages
   - Suggest troubleshooting steps
   - Provide alternative approaches

## Code Quality Standards

Ensure implementation follows:

- [ ] **Clean Code:**
  - Meaningful variable/function names
  - Single responsibility principle
  - DRY (Don't Repeat Yourself)
  - Consistent formatting

- [ ] **Security:**
  - Input validation everywhere
  - No secrets in code
  - SQL injection prevention
  - CORS configured properly

- [ ] **Testing:**
  - Unit tests for business logic
  - Integration tests for database
  - E2E tests for critical flows
  - Edge cases covered

- [ ] **Documentation:**
  - README with setup steps
  - API documentation generated
  - Complex logic explained
  - Architecture documented

- [ ] **Error Handling:**
  - Graceful failure handling
  - Meaningful error messages
  - Proper logging
  - No silent failures

## Validation at Each Checkpoint

At every checkpoint, verify:

1. **Functionality:** Does it work as designed?
2. **Tests:** Do tests pass? Is coverage adequate?
3. **Code Quality:** Is code clean and maintainable?
4. **Documentation:** Is it documented?
5. **Learning:** Do I understand what I built and why?

Don't proceed to next step until current step is solid.

## Time Management

- Track actual time spent on each step
- Compare to estimates from design phase
- Adjust remaining estimates based on learnings
- Don't rush - understanding matters more than speed

## Getting Unstuck

If blocked for more than 30 minutes:

1. **Take a break:** Step away, come back fresh
2. **Review design:** Did we miss something in design phase?
3. **Simplify:** Can we build MVP first, enhance later?
4. **Ask for help:** Use this prompt to get specific guidance
5. **Research:** Look for similar implementations

## Common Implementation Pitfalls

- **Premature optimization:** Make it work, then make it fast
- **Scope creep:** Stick to module requirements
- **Skipping tests:** Tests save time debugging later
- **Poor error handling:** Handle errors from the start
- **Undocumented decisions:** Comment why, not just what

## Deliverables Checklist

At end of implementation phase, verify:

- [ ] All planned features implemented
- [ ] Database schema created and migrated
- [ ] API endpoints working as specified
- [ ] Unit tests written and passing
- [ ] Integration tests written and passing
- [ ] E2E tests written and passing
- [ ] Code coverage meets target (80%+)
- [ ] Error handling implemented
- [ ] Logging implemented
- [ ] Health checks working
- [ ] README completed with setup steps
- [ ] API documentation generated
- [ ] Docker configuration working (if applicable)
- [ ] Can demonstrate all features working

## When to Move to Next Phase

Proceed to Phase 3 (Enhancement) only when:

- All deliverables above are complete
- Tests are passing consistently
- Documentation is sufficient for others to use
- You understand the code you've written
- Core functionality is stable
- Ready to add optimizations and enhancements

## Getting Started

Let's begin with Step 0: Project Setup.

Walk me through:
1. Creating the initial project structure
2. Installing necessary dependencies
3. Setting up configuration files
4. Getting a basic "hello world" running

Ready to start? Let's build [MODULE_NAME]!
```

---

## How to Use This Prompt

### Preparation

1. Complete Design Phase first
2. Have all design artifacts ready
3. Set up development environment
4. Clear your workspace for focused work

### Execution Strategy

**Option A: One Big Session**

- Block full day(s)
- Work through all steps sequentially
- Build momentum

**Option B: Step-by-Step Sessions**

- Complete one step per session
- More time to absorb concepts
- Easier to fit in schedule

**Option C: Hybrid**

- Do related steps together (e.g., Steps 1-2, Steps 3-4)
- Balance momentum and learning

### Working with the AI

1. **Start Each Step:**

   - Copy the step description
   - Ask AI to guide you through it
   - Request code examples

2. **During Implementation:**

   - Ask questions when unclear
   - Request explanations of patterns
   - Get help debugging

3. **At Checkpoints:**
   - Verify with AI you're on track
   - Review code quality
   - Confirm ready to proceed

### Tracking Progress

Create a simple progress tracker:

```markdown
## Implementation Progress

- [x] Step 0: Project Setup - 2 hours (estimated: 2 hours)
- [ ] Step 1: Database Layer - \_\_\_ hours (estimated: 3 hours)
- [ ] Step 2: Repository Layer - \_\_\_ hours (estimated: 3 hours)
- [ ] Step 3: Service Layer - \_\_\_ hours (estimated: 4 hours)
- [ ] Step 4: API Layer - \_\_\_ hours (estimated: 4 hours)
- [ ] Step 5: Testing - \_\_\_ hours (estimated: 4 hours)
- [ ] Step 6: Error Handling - \_\_\_ hours (estimated: 2 hours)
- [ ] Step 7: Documentation - \_\_\_ hours (estimated: 2 hours)

**Total Time Spent:** **\_ hours
**Estimated Remaining:** \_** hours
```

### Git Workflow

Commit at each checkpoint:

```bash
# After Step 0
git commit -m "feat: initial project setup"

# After Step 1
git commit -m "feat: implement database layer and migrations"

# After Step 2
git commit -m "feat: implement repository layer with tests"

# And so on...
```

### Testing as You Go

Don't wait until Step 5 to test:

- Write unit tests immediately after each class/function
- Run tests after every change
- Fix failing tests before proceeding
- Maintain green test suite

---

## Customization by Technology

### For Node.js/NestJS Projects

- Focus on decorators and dependency injection
- Use NestJS CLI generators
- Follow NestJS testing patterns

### For Python/Django Projects

- Focus on Django ORM and views
- Use Django management commands
- Follow Django testing patterns

### For Other Stacks

- Adapt steps to your framework
- Maintain same checkpoint discipline
- Follow framework conventions

---

## Tips for Success

1. **Read Before Coding:** Understand full step before writing code
2. **Small Commits:** Commit working code frequently
3. **Test Immediately:** Don't accumulate untested code
4. **Refactor Boldly:** Improve as you learn
5. **Document Why:** Future you will thank present you
6. **Take Breaks:** Step away when frustrated
7. **Celebrate Progress:** Acknowledge each completed checkpoint

---

## Troubleshooting Guide

### "This is taking longer than estimated"

- Normal! Estimates are guidelines
- Track actual time for learning
- Adjust future estimates

### "Tests are failing"

- Don't move forward
- Fix tests before proceeding
- Tests are your safety net

### "Code is messy"

- Refactor at checkpoints
- Clean code is faster long-term
- Apply boy scout rule

### "Stuck on implementation detail"

- Consult documentation
- Ask AI for specific help
- Simplify first, enhance later

### "Lost sight of big picture"

- Review architecture diagram
- Revisit module objectives
- Check against design decisions

---

## Next Prompt

After completing implementation phase, proceed to:

- `prompt-phase-3-enhancement.md` for optimizations
- `prompt-break-and-observe.md` for resilience exercises
