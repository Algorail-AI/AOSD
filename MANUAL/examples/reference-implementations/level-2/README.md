# Level 2 Reference Examples

**Orchestration Profile Examples and Patterns**

---

## Purpose

This directory contains reference examples for creating Level 2 Orchestration Profiles. These examples show how to structure and document your specific AOSD implementation choices.

Use these as starting points and inspiration—adapt to your context rather than copying verbatim.

---

## Contents

| File | Description |
|------|-------------|
| `folder-structure-example.md` | Example repository structure for an Orchestration Profile |
| `agent-configuration-example.md` | AI agent role assignments and configuration |
| `workflow-example.md` | GitHub Actions workflow definitions |
| `invariants-example.md` | Non-negotiable architectural rules |

---

## Quick Start

To create your own Orchestration Profile:

1. **Start with the template**: Use [IMPLEMENTATION_TEMPLATE.md](../../../IMPLEMENTATION_TEMPLATE.md)
2. **Review decision checklist**: Complete the Decision Points Checklist section
3. **Study these examples**: Understand patterns before adapting
4. **Create your repository**: Typically named `{org-name}-aosd` or `{your-name}-aosd`
5. **Document your choices**: Fill in each section with your specific decisions

---

## Example Scenario

These examples use a fictional "Acme Corp" scenario:

- **Team**: Small team (3 developers)
- **Platform**: AWS (single cloud)
- **AI Agents**: Claude Code (Builder) + ChatGPT (Reviewer)
- **Workflow**: GitHub-native (Issues, PRs, Actions)
- **Environment**: Dev + Staging + Production

This scenario is representative of many teams starting with AOSD.

---

## Related Documents

- [IMPLEMENTATION_TEMPLATE.md](../../../IMPLEMENTATION_TEMPLATE.md) - Full template for creating profiles
- [AI_GUIDE.md](../../../../AI_GUIDE.md) - AI agent guide for applying AOSD
- [AOSD_FRAMEWORK.md](../../../AOSD_FRAMEWORK.md) - Core methodology reference

---

## Anti-Patterns

**Do NOT**:
- Copy examples without understanding them
- Skip the Decision Points Checklist
- Document decisions you haven't actually made
- Create Level 2 content before understanding Level 1 principles

**DO**:
- Read AOSD_FRAMEWORK.md first
- Make deliberate decisions for your context
- Start minimal and add sections as needed
- Keep documentation in sync with actual practices

---

**End of README**
