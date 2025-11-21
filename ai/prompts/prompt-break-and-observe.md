# Break & Observe Exercises Prompt

## Purpose

This prompt guides you through controlled failure exercises to understand system behavior, resilience, and debugging under stress.

---

## Break & Observe Prompt

````
I'm ready to run Break & Observe exercises for [MODULE_NAME]. Help me systematically break the system and learn from observing its behavior.

## Context
- Project Overview: @project.md
- Module Plan: @[module_file_name].md
- Working Implementation: [reference to codebase]
- Break & Observe Plan: @break_and_observe.md

## Current Status
- ✅ Core implementation complete
- ✅ Enhancements complete
- ✅ System stable and tested
- ✅ Ready to break things intentionally!

## Break & Observe Philosophy

**Goal:** Learn by breaking things in controlled ways

**Key Principles:**
1. **Controlled Chaos:** Break one thing at a time
2. **Observe Carefully:** Watch what happens, don't just guess
3. **Document Everything:** Capture actual behavior
4. **Learn from Failure:** Understand why it broke
5. **Restore Fully:** Get back to working state before next exercise

**Not Goals:**
- ❌ Breaking things for fun
- ❌ Testing already known failures
- ❌ Creating permanent damage

---

## Exercise Template

For each exercise, follow this structure:

### 1. Pre-Exercise Setup
- **Verify system is healthy:** Run all tests, check all components work
- **Backup current state:** Commit all code, snapshot database if needed
- **Prepare monitoring:** Have logs visible, metrics ready to observe

### 2. Exercise Execution

#### What to Break
[Specific action to take to cause failure]

#### How to Break It
[Step-by-step instructions]

#### Expected Behavior
[What you predict will happen]

#### Actual Behavior
[What actually happened - fill during exercise]

#### Observations
[Detailed notes about what you observed]

**Questions to answer:**
- What error messages appeared?
- How did the system respond?
- What was user experience?
- What did logs show?
- How quickly did failure occur?
- Was failure graceful or catastrophic?

### 3. Measurement & Analysis

**Metrics to capture:**
- Response times before/during failure
- Error rates
- Resource usage (CPU, memory, connections)
- Recovery time
- Data consistency

**Analysis questions:**
- Why did it fail this way?
- What made it better or worse?
- What assumptions were wrong?
- What surprised you?

### 4. Recovery Process
- **Steps to restore:** [Document how you recovered]
- **Recovery time:** [How long it took]
- **Data loss:** [Any permanent impacts?]
- **Lessons:** [What did recovery teach?]

### 5. Improvements Identified
- What could make system more resilient?
- What monitoring/alerting would help?
- What's acceptable behavior vs needs fixing?

---

## Core Exercise Categories

### Category 1: Infrastructure Failures

**Purpose:** Understand dependency failures

**Exercises:**

#### Exercise 1.1: Database Unavailability
**Break:** Stop database container/service
**Observe:**
- API response behavior
- Error messages to users vs logs
- Connection pool behavior
- Application crash or graceful degradation
**Learn:**
- Connection retry behavior
- Timeout configuration
- Error handling effectiveness
- Need for circuit breaker

#### Exercise 1.2: Database Connection Exhaustion
**Break:** Set connection pool to 1, make multiple concurrent requests
**Observe:**
- Request queueing or rejection
- Response times
- Error messages
- Recovery after requests complete
**Learn:**
- Connection pool sizing
- Queue behavior
- Timeout impacts
- Monitoring importance

#### Exercise 1.3: Slow Database
**Break:** Add artificial delay to queries (via proxy or sleep)
**Observe:**
- Request timeout behavior
- Client experience
- Resource consumption
- Cascade effects
**Learn:**
- Timeout configuration
- User experience degradation
- Need for monitoring slow queries

---

### Category 2: Invalid Input & Attack Scenarios

**Purpose:** Test input validation and security

**Exercises:**

#### Exercise 2.1: Malformed Input
**Break:** Send invalid JSON, missing required fields, wrong types
**Observe:**
- Error responses
- Status codes
- Error message clarity
- Server-side logging
**Learn:**
- Validation effectiveness
- Error message quality
- Client debugging experience

#### Exercise 2.2: Boundary Violations
**Break:** Send extremely large payloads, negative numbers, out-of-range values
**Observe:**
- Request rejection
- Resource usage
- Performance impact
- Error handling
**Learn:**
- Validation completeness
- Resource protection
- Security boundaries

#### Exercise 2.3: SQL Injection Attempts
**Break:** Try SQL injection patterns in inputs
**Observe:**
- ORM protection
- Error messages (should not reveal DB structure)
- Logging (should log security events)
**Learn:**
- ORM security effectiveness
- Information leakage risks
- Security logging

#### Exercise 2.4: Script Injection
**Break:** Submit XSS payloads in text fields
**Observe:**
- Storage of malicious content
- Retrieval behavior
- Sanitization effectiveness
**Learn:**
- Content handling
- Output encoding
- Security considerations

---

### Category 3: Concurrency & Race Conditions

**Purpose:** Understand concurrent operation challenges

**Exercises:**

#### Exercise 3.1: Concurrent Updates
**Break:** Update same resource from multiple clients simultaneously
**Observe:**
- Which update wins (last write wins?)
- Data consistency
- Conflict detection
- Version tracking
**Learn:**
- Optimistic vs pessimistic locking
- Need for versioning
- Conflict resolution strategies

#### Exercise 3.2: Concurrent Deletes
**Break:** Try to delete same resource multiple times concurrently
**Observe:**
- Error responses
- Idempotency
- Database state
**Learn:**
- Idempotency patterns
- Error handling for not found
- Transaction isolation

---

### Category 4: Performance & Scale

**Purpose:** Understand performance boundaries

**Exercises:**

#### Exercise 4.1: Load Testing
**Break:** Send many requests concurrently (use tool like `ab`, `wrk`, or `k6`)
**Observe:**
- Response time degradation
- Error rate increases
- Resource saturation
- Database performance
**Learn:**
- System capacity
- Bottlenecks
- Resource limits
- Need for scaling

#### Exercise 4.2: Large Dataset Operations
**Break:** Create thousands of records, then query without pagination
**Observe:**
- Query performance
- Memory usage
- Timeout issues
- Client experience
**Learn:**
- Importance of pagination
- Query optimization
- Index effectiveness
- Resource protection

#### Exercise 4.3: Deep Pagination
**Break:** Request very high page numbers (e.g., page 10,000)
**Observe:**
- Query execution time
- OFFSET performance degradation
- Database load
**Learn:**
- OFFSET limitations
- Need for cursor-based pagination
- Query optimization

---

### Category 5: Data Integrity

**Purpose:** Test data consistency and integrity

**Exercises:**

#### Exercise 5.1: Soft Delete Verification
**Break:** Delete record, try to access/update it, check database directly
**Observe:**
- API behavior (404s)
- Database state (record still there)
- Query performance impact (filtering deleted)
**Learn:**
- Soft delete trade-offs
- Query complexity
- Recovery possibilities
- Storage implications

#### Exercise 5.2: Invalid Foreign Keys
**Break:** Try to create records with non-existent foreign keys
**Observe:**
- Database constraint enforcement
- Error messages
- Transaction rollback
**Learn:**
- Database constraint effectiveness
- Error handling for referential integrity

#### Exercise 5.3: Transaction Rollback
**Break:** Cause error mid-transaction in bulk operation
**Observe:**
- Partial vs full rollback
- Database consistency
- Error reporting
**Learn:**
- Transaction behavior
- ACID properties in practice
- Error recovery

---

## Exercise Execution Guide

### For Each Exercise:

**1. Prepare (5 minutes)**
- [ ] System is healthy
- [ ] Code is committed
- [ ] Logs are visible
- [ ] Monitoring ready
- [ ] Exercise template ready to fill

**2. Execute (10-15 minutes)**
- [ ] Follow "How to Break It" steps
- [ ] Observe and document behavior
- [ ] Capture error messages
- [ ] Screenshot/save logs
- [ ] Note metrics

**3. Analyze (10 minutes)**
- [ ] Compare expected vs actual
- [ ] Understand why it behaved this way
- [ ] Identify what could be better
- [ ] Note what was good

**4. Recover (5 minutes)**
- [ ] Restore system to working state
- [ ] Verify full functionality
- [ ] Clean up any test data

**5. Document (10 minutes)**
- [ ] Fill in exercise template completely
- [ ] Capture key learnings
- [ ] Note improvement ideas
- [ ] Add to module reflection

**Total per exercise: ~40-45 minutes**

---

## Safety Guidelines

### Before Any Exercise:
1. ✅ Commit all working code
2. ✅ Use test/dev environment only
3. ✅ Have backup of any important data
4. ✅ Know how to restore to working state

### During Exercise:
1. 🔍 Break only what exercise specifies
2. 🔍 Observe, don't immediately fix
3. 🔍 Take notes in real-time
4. 🔍 Capture actual error messages

### After Exercise:
1. ✅ Fully restore system before next exercise
2. ✅ Run tests to verify health
3. ✅ Document all findings
4. ✅ Don't stack failures

---

## Documentation Template for Each Exercise

```markdown
## Exercise: [Name]

**Date:** [YYYY-MM-DD]
**Duration:** [X minutes]

### Setup
- System state: [Healthy / Already stressed]
- Test data: [What test data existed]
- Monitoring: [What was monitored]

### Hypothesis
I expected: [Your prediction]

### Execution
Steps taken:
1. [Step 1]
2. [Step 2]
...

### Observations

**Error Messages:**
````

[Exact error messages]

```

**Log Output:**
```

[Relevant log lines]

```

**User Experience:**
[What user would see/experience]

**System Behavior:**
- Response time: [before/during/after]
- Resource usage: [CPU/Memory/Connections]
- Error rate: [X%]
- Recovery time: [X seconds/minutes]

### Analysis

**Why did this happen?**
[Technical explanation]

**What surprised me?**
[Unexpected behavior]

**What worked well?**
[Positive observations]

**What could be better?**
[Improvement opportunities]

### Improvements Identified

**High Priority:**
- [ ] [Improvement 1]
- [ ] [Improvement 2]

**Low Priority:**
- [ ] [Improvement 3]

**Future Module:**
- [ ] [Improvement for later module]

### Key Learning

**Most important insight:**
[1-2 sentences]

**How this applies to production systems:**
[Real-world connection]

---
```

---

## Recommended Exercise Sequence

### Minimum Viable Exercises (Pick 3-5)

1. Database Unavailability
2. Invalid Input
3. Concurrent Updates
4. Load Testing
5. Soft Delete Verification

### Comprehensive Learning (Do 8-10)

Include above plus: 6. Connection Exhaustion 7. SQL Injection Attempts 8. Large Dataset Operations 9. Deep Pagination 10. Transaction Rollback

### Complete Coverage (Do All)

All exercises in all categories

---

## Getting Started

Let's begin with Exercise 1.1: Database Unavailability

**Help me with:**

1. Setting up monitoring before breaking
2. Executing the failure correctly
3. Interpreting the results
4. Understanding what it means
5. Identifying improvements

Ready to break [MODULE_NAME]?

```

---

## How to Use This Prompt

### Scheduling Break & Observe

**Option A: Dedicated Session**
- Block 3-4 hours
- Run 5-6 exercises back-to-back
- Good for momentum and comparison

**Option B: One Per Day**
- Run one exercise daily
- More time to absorb lessons
- Better retention

**Option C: Grouped by Category**
- All infrastructure failures one day
- All input validation another day
- Helps see patterns

### Working with AI During Exercises

**Before Exercise:**
```

Help me prepare for [Exercise Name]:

- What should I monitor?
- What behavior should I expect?
- How do I safely break this?

```

**During Exercise:**
```

I'm observing [behavior].
What does this mean?
Why is it happening?
What should I look for next?

```

**After Exercise:**
```

Here's what I observed: [paste notes]
Help me understand:

- Why did it behave this way?
- What would make it more resilient?
- How does this apply to production?

```

### Documentation Discipline

- Fill template during exercise, not after
- Capture exact error messages
- Take screenshots if helpful
- Note timestamps
- Record your immediate reactions

### Learning Extraction

After each exercise, ask yourself:
1. What did I learn about the system?
2. What did I learn about failure modes?
3. What did I learn about debugging?
4. How would I prevent/handle this in production?
5. What monitoring would help detect this?

---

## Tips for Success

1. **Don't Skip Exercises:** They teach what testing can't
2. **Observe Before Fixing:** Resist urge to immediately fix
3. **Compare to Predictions:** Learn from mismatches
4. **Document Thoroughly:** Future you will reference this
5. **Connect to Production:** Always ask "what if this was real?"
6. **Celebrate Failure:** Finding issues is success
7. **Share Learnings:** Discuss findings with others

---

## Common Pitfalls

- **Skipping documentation:** Can't learn without capturing observations
- **Fixing too quickly:** Miss opportunity to understand
- **Not restoring fully:** Next exercise starts with compromised system
- **Not measuring:** Observations without data are just opinions
- **Assuming behavior:** Actually test, don't guess

---

## Next Prompt

After completing Break & Observe exercises, proceed to:
- `prompt-postmortem-reflection.md` for module wrap-up and reflection

```
