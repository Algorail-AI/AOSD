# Workflow Reference Implementations

**AOSD Level 2 Examples - Development Workflows**

---

## Purpose

This directory contains reference implementations showing how AOSD workflow principles can be implemented with specific platforms and AI toolsets. These examples demonstrate patterns and approaches but are not meant to be copied directly.

---

## What's Included

### AWS + Claude Workflow
**File**: `aws-claude-workflow.md`

A complete development workflow using:
- AWS infrastructure (EC2, SSM, CloudWatch)
- Claude Code as primary builder
- ChatGPT as architect/reviewer
- Codex as optional fast synthesizer

Covers the full development cycle from slice specification to production deployment.

### Claude + ChatGPT Prompting Patterns
**File**: `claude-chatgpt-prompting-patterns.md`

Specific prompt templates for:
- Claude Code (builder)
- ChatGPT (architect/reviewer)
- Codex (fast synthesizer)
- Constrained models (constrained executor)

Includes task-specific templates and multi-agent coordination patterns.

### Work Item Lifecycle
**File**: `work-item-lifecycle-example.md`

A reference implementation for AOSD Principle 11 (Work Item Lifecycle) using GitHub Issues:
- Issue creation with templates and labels
- Branch naming conventions linked to work items
- Commit message formats referencing issues
- PR workflow with auto-closing issues
- Automation options for lifecycle stage transitions
- Platform adaptations for Jira, Azure DevOps, and Linear

---

## How to Use These Examples

### For Learning
1. Read each workflow to understand the approach
2. Note how AOSD principles are applied
3. Identify patterns relevant to your environment
4. Adapt concepts to your platform and toolset

### For Your Own Implementation
1. Start with the generalized principles in `MANUAL/`
2. Use these examples as reference
3. Create your own Level 2 Orchestration Profile
4. Document your specific tool and platform choices

### For Non-AWS/Non-Claude Environments
- Translate concepts to your platform
- Map AI roles to your available tools
- Core AOSD principles remain the same
- Implementation details will differ

---

## Related Documents

- [AOSD Framework](../../../AOSD_FRAMEWORK.md) - Core methodology
- [Development Workflow Principles](../../../DEVELOPMENT_WORKFLOW.md) - Generalized workflow
- [Environment Strategy Principles](../../../ENVIRONMENT_STRATEGY.md) - Environment isolation
- [AI Prompting Patterns](../../../AI_PROMPTING_PATTERNS.md) - Generalized prompting

### Related Examples

- [Case Studies](../case-studies/) - Narrative examples showing workflows in action
- [Restrictive Environments](../restrictive-environments/) - Patterns for constrained environments
- [Integrated Features](../integrated-features/) - AI and training integrated into applications

---

**Note**: These are sanitized reference implementations. Real implementations will be more complex and tailored to specific organizational requirements.
