# Case Study Reference Implementations

**AOSD Level 2 Examples - Narrative Case Studies**

---

## Purpose

This directory contains narrative case studies demonstrating how AOSD principles are applied in realistic scenarios. Unlike other reference implementations that show specific patterns, these case studies tell the story of a complete feature or problem from start to finish.

Case studies help:
- Illustrate how AOSD principles work together
- Show realistic decision-making processes
- Demonstrate handling of complications and tradeoffs
- Provide complete context for learning

---

## What's Included

### The Orion Initiative: An AOSD Adoption Story (Story-Driven)
**File**: `../../../../SYLLABUS/THE_ORION_INITIATIVE.md`

A *Phoenix Project*-style narrative following Alex Chen and their team through eight months of adopting AOSD. Located in the SYLLABUS directory as a curriculum resource. See [THE_ORION_INITIATIVE.md](../../../../SYLLABUS/THE_ORION_INITIATIVE.md).

---

### Health Check API Case Study (Technical)
**File**: `health-check-api-case-study.md`

A procedural case study following a team implementing a comprehensive health check API using AOSD principles. Demonstrates:
- Slice-based planning
- Multi-agent orchestration
- TodoWrite workflow in action
- Testing strategy (Fast/Medium/Slow)
- Handling unexpected complications
- Human-in-the-loop decision points

**Best for**: Understanding specific technical implementation patterns and seeing code examples in context.

---

### Lambda Deploy Optimization Case Study (Process)
**File**: `lambda-deploy-optimization-case-study.md`

A case study demonstrating the "measure before refactor" principle. A team proposed splitting a monolithic CDK stack to improve deploy speed, but measurement revealed the actual bottleneck was duplicate Lambda asset bundling. Demonstrates:
- Challenging assumptions before acting
- Multi-agent disagreement resolution via measurement
- Decision record vs execution issue separation
- Finding simpler solutions than originally proposed

**Best for**: Understanding when NOT to refactor and how measurement changes decisions.

---

## How to Use These Case Studies

### For Learning
1. Read the case study end-to-end like a story
2. Note how AOSD principles are applied at each stage
3. Observe how the team handles complications
4. Identify decision points and the reasoning behind choices

### For Training
1. Use as discussion material for team onboarding
2. Walk through scenarios during pair programming
3. Reference when explaining "why" behind AOSD practices
4. Compare to your own project experiences

### For Reference
1. Extract specific patterns when facing similar challenges
2. Use as templates for documenting your own case studies
3. Share with stakeholders to explain AOSD value

---

## Case Study Structure

Each case study follows a consistent narrative structure:

1. **Context**: Background and initial situation
2. **Planning Phase**: How work was broken down
3. **Implementation Journey**: Step-by-step progress
4. **Complications**: What went wrong and how it was handled
5. **Resolution**: Final outcome and lessons learned
6. **Reflection**: AOSD principles demonstrated

---

## Related Documents

- [AOSD Framework](../../../AOSD_FRAMEWORK.md) - Core methodology
- [Slice Spec Template](../../../../TEMPLATES/SLICE_SPEC_TEMPLATE.md) - Feature specification template
- [TodoWrite Template](../../../../TEMPLATES/TODO_WRITE_TEMPLATE.md) - Task planning template

---

**Note**: These case studies are fictionalized composites based on real patterns. Names, specific technologies, and details have been generalized to maintain platform-agnosticism.
