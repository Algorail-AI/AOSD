# Reference Implementations

**AOSD Level 2 Examples**

---

## Purpose

This directory contains reference implementations showing how AOSD Framework principles can be implemented with specific platforms, tools, and workflows. These examples demonstrate patterns and approaches but are not meant to be copied directly.

Use these as learning resources when creating your own Level 2 Orchestration Profile.

---

## Directory Structure

```
reference-implementations/
├── README.md                 # This file
├── level-2/                  # Orchestration Profile examples
│   ├── folder-structure-example.md
│   ├── agent-configuration-example.md
│   ├── workflow-example.md
│   └── invariants-example.md
├── aws/                      # AWS platform examples
│   ├── aws-factory-pattern-example.md
│   ├── multi-tenant-dynamodb-example.md
│   └── environment-strategy-aws.md
├── workflows/                # Development workflow examples
│   ├── aws-claude-workflow.md
│   ├── claude-chatgpt-prompting-patterns.md
│   └── work-item-lifecycle-example.md
├── model-assignments/        # AI model role assignment examples
├── case-studies/             # Narrative case studies
│   └── health-check-api-case-study.md
├── restrictive-environments/ # Constrained environment patterns
│   └── limited-ai-capability-patterns.md
└── integrated-features/      # AI and training integrated into apps
    ├── integrated-training-example.md
    └── security-monitoring-example.md
```

---

## What's Included

### Level 2 Orchestration Profile Examples (`level-2/`)

Complete examples for creating your own Orchestration Profile:

| Example | Description |
|---------|-------------|
| Folder Structure | Repository structure for an Orchestration Profile |
| Agent Configuration | AI agent role assignments, prompts, and handoff patterns |
| Workflow Definitions | GitHub Actions for CI/CD aligned with AOSD practices |
| Invariant Definitions | Non-negotiable rules with verification and enforcement |

**Start here** when creating your Level 2 profile. These examples use a fictional "Acme Corp" scenario with AWS, Claude Code, and GitHub.

### AWS Platform Examples (`aws/`)

Implementations of AOSD patterns using AWS services:

| Example | Description |
|---------|-------------|
| AWS Factory Pattern | Centralized AWS service client creation |
| Multi-Tenant DynamoDB | Data isolation strategies for shared databases |
| Environment Strategy | Complete AWS environment architecture (EC2, SSM, IAM) |

### Workflow Examples (`workflows/`)

Development workflow implementations:

| Example | Description |
|---------|-------------|
| AWS + Claude Workflow | Full development cycle using Claude Code + ChatGPT on AWS |
| Claude + ChatGPT Prompting | Specific prompt templates for Claude/ChatGPT agents |
| Work Item Lifecycle | GitHub Issues workflow with branch naming, commit formats, PR patterns |

### Model Assignments (`model-assignments/`)

Examples of AI model role assignments for different contexts.

### Case Studies (`case-studies/`)

Narrative case studies showing AOSD principles in action:

| Example | Description |
|---------|-------------|
| Health Check API | Complete feature implementation narrative demonstrating slice-based development, TodoWrite, FCIS, and handling complications |

### Restrictive Environments (`restrictive-environments/`)

Patterns for constrained environments:

| Example | Description |
|---------|-------------|
| Limited AI Capability | Planner/Executor separation, single-file discipline, context management |

### Integrated Features (`integrated-features/`)

Examples of AI and training built into applications (AOSD Goals 5 & 6):

| Example | Description |
|---------|-------------|
| Integrated Training | Contextual help, AI-assisted onboarding, skill progression |
| Security Monitoring | AI-assisted threat detection, anomaly analysis, human escalation |

---

## How to Use These Examples

### For Learning

1. **Read the generalized principles first** - Start with documents in `MANUAL/`
2. **Study reference implementations** - See how principles translate to specific platforms
3. **Note adaptation patterns** - Observe how abstract concepts become concrete
4. **Don't copy-paste** - Understand and customize for your context

### For Creating Your Own Orchestration Profile

1. Start with [IMPLEMENTATION_TEMPLATE.md](../../IMPLEMENTATION_TEMPLATE.md)
2. Reference these examples for inspiration
3. Document your specific platform and tool choices
4. Adapt patterns to your organizational requirements

### For Different Platforms

These examples primarily use AWS and Claude/ChatGPT. For other platforms:

- **Azure/GCP**: Translate AWS concepts to equivalent services
- **Other AI Tools**: Map Claude/ChatGPT roles to your AI agents
- **Different Workflows**: Adapt process steps to your team's practices

The core AOSD principles remain the same regardless of platform.

---

## What's NOT Included

- Production code from real projects
- Specific business logic
- Proprietary organizational patterns
- Complete working applications
- Sensitive configuration details

---

## Related Documents

### Framework (Level 1)
- [AOSD Framework](../../AOSD_FRAMEWORK.md) - Core methodology
- [Development Workflow](../../DEVELOPMENT_WORKFLOW.md) - Workflow principles
- [Environment Strategy](../../ENVIRONMENT_STRATEGY.md) - Environment isolation principles
- [AI Prompting Patterns](../../AI_PROMPTING_PATTERNS.md) - Prompting principles

### Templates
- [Implementation Template](../../IMPLEMENTATION_TEMPLATE.md) - Create your own Level 2 profile
- [Slice Spec Template](../../../TEMPLATES/SLICE_SPEC_TEMPLATE.md) - Feature specification template

---

## Contributing Examples

When adding new reference implementations:

1. **Keep them sanitized** - Remove proprietary details
2. **Make them educational** - Show patterns, not production code
3. **Add context** - Explain the "why" behind choices
4. **Link to principles** - Reference the generalized documents
5. **Update this README** - Document new additions

---

**Note**: These are sanitized references for educational purposes. Real implementations will be more complex and tailored to specific organizational requirements.
