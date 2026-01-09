# AGENT.md

**Tool-Agnostic Operator Contract for AI Agents**

This document provides guidance for any AI agent (Claude Code, Codex CLI, ChatGPT, Gemini, Cursor, Amazon Q, Copilot, etc.) operating on the AOSD Framework repository.

---

## What This Repository Is

This is the **AOSD Framework** (Level 1) - a universal, environment-agnostic methodology for AI Orchestrated Systems Development. It contains principles, patterns, and templates that anyone can use to adopt AOSD.

**This repository is Level 1** - the universal framework.

---

## What This Repository Is NOT

This repository is NOT:
- A Level 2 Orchestration Profile (platform-specific implementation)
- A Level 3 application repository (project-specific documentation)
- A place to build systems using AOSD (use Level 2/3 for that)

**For guidance on applying AOSD methodology to build systems, see [AI_GUIDE.md](AI_GUIDE.md).**

---

## AOSD Three-Level Architecture

### Level 1: AOSD Framework (This Repository)
- **Universal methodology**: Environment-agnostic principles
- **For**: Anyone learning or adopting AOSD
- **Contains**: Core principles, templates, examples, training

### Level 2: Orchestration Profile
- **Specific implementation**: Cloud platform, AI agents, tools
- **For**: Organization or individual's projects
- **Location**: Separate repository (e.g., your-org-aosd)
- **Created using**: IMPLEMENTATION_TEMPLATE.md from this repo

### Level 3: Application-Specific
- **Project-specific**: Unique patterns for each application
- **For**: Individual applications
- **Location**: In application repo at `AOSD/` folder
- **Created using**: APP_TEMPLATE.md from this repo

---

## When Working on This Repository

This repository is the **AOSD Framework** itself. When making changes:

### Purpose
- Keep content **environment-agnostic** and **universal**
- Provide **principles, not prescriptions**
- Offer **templates and examples**, not specific implementations
- Enable anyone to adapt AOSD to their context

### What Belongs Here

**Include**:
- Core AOSD methodology and principles
- Environment-agnostic patterns (e.g., Environment Factory concept)
- Universal templates (slice specs, ADRs, app templates)
- Reference implementations showing how to apply patterns
- Training curriculum (SYLLABUS)
- Generic workflow guidance

**Do NOT Include**:
- Specific cloud platform implementations (those go in Level 2)
- Specific AI agent configurations (those go in Level 2)
- Specific organizational patterns (those go in Level 2)
- Application code examples (those go in Level 3)
- Proprietary implementation details

### Making Changes

When updating framework documentation:
1. Keep language platform-agnostic ("cloud platform" not "AWS")
2. Provide options rather than mandates ("consider X or Y")
3. Explain the "why" behind principles
4. Use examples to illustrate, not prescribe
5. Update both documentation and templates as needed

**Important**: Consult **[META_GUIDE.md](MANUAL/META_GUIDE.md)** for propagation rules. No update is complete unless all dependent artifacts are updated.

---

## Repository Structure

```
AOSD/                               # AOSD Framework (Level 1)
├── README.md                       # Overview and getting started
├── AGENT.md                        # This file - tool-agnostic operator contract
├── AI_GUIDE.md                     # AI agent entrypoint for AOSD methodology
├── CHANGELOG.md                    # Version history
├── DECISIONS.md                    # Strategic framework decisions
├── AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md  # Non-normative: influences, citations
├── MANUAL/
│   ├── AOSD_FRAMEWORK.md           # Core methodology (KEY DOCUMENT)
│   ├── MATURITY_MODEL.md           # Adoption levels (extracted from framework)
│   ├── ARCHITECTURE_PATTERNS.md    # Reference patterns (extracted from framework)
│   ├── IMPLEMENTATION_TEMPLATE.md  # For creating Orchestration Profiles
│   ├── META_GUIDE.md               # AOSD for maintaining AOSD
│   ├── TROUBLESHOOTING.md          # AI issues, drift, teams, restricted environments
│   ├── DEVELOPMENT_WORKFLOW.md     # Workflow principles (platform-agnostic)
│   ├── ENVIRONMENT_STRATEGY.md     # Environment isolation principles
│   ├── AI_PROMPTING_PATTERNS.md    # Prompting principles (agent-agnostic)
│   ├── MODEL_ROLES.md              # AI agent role definitions
│   ├── LESSONS_LEARNED.md          # Working memory for emerging patterns
│   ├── RAG_GUIDE.md                # Retrieval system implementation guidance
│   └── examples/
│       └── reference-implementations/
│           ├── level-2/            # Orchestration Profile examples
│           ├── aws/                # AWS platform examples
│           ├── workflows/          # Workflow implementation examples
│           └── model-assignments/  # Model role assignment examples
├── TEMPLATES/
│   ├── APP_TEMPLATE.md             # Level 3 application docs
│   ├── AOSD_FOLDER_STRUCTURE.md    # Level 3 folder structure
│   ├── ADR_TEMPLATE.md
│   ├── SLICE_SPEC_TEMPLATE.md
│   ├── TODO_WRITE_TEMPLATE.md
│   ├── REVIEW_CHECKLIST_TEMPLATE.md
│   ├── SPEC_TEMPLATE.md
│   └── model_evaluation_template.md
└── SYLLABUS/                       # AI-powered learning curriculum
    ├── COURSE_OVERVIEW.md
    ├── MODULE_01_FOUNDATIONS.md
    └── ... (modules 01-11)
```

---

## Key Documents

### Core Framework
- **MANUAL/AOSD_FRAMEWORK.md**: The heart of AOSD - core principles, patterns, and practices
- **MANUAL/IMPLEMENTATION_TEMPLATE.md**: Guide for creating Level 2 Orchestration Profiles

### Templates (for others to use)
- **TEMPLATES/APP_TEMPLATE.md**: Template for Level 3 app documentation
- **TEMPLATES/AOSD_FOLDER_STRUCTURE.md**: Structure guide for AOSD/ folders
- **TEMPLATES/SLICE_SPEC_TEMPLATE.md**: Slice specification template
- **TEMPLATES/ADR_TEMPLATE.md**: Architecture Decision Record template

### Supporting Documents
- **MANUAL/TROUBLESHOOTING.md**: Comprehensive troubleshooting including AI agent issues, integrated AI features, agent drift, restrictive environments, and team workflows
- **MANUAL/DEVELOPMENT_WORKFLOW.md**: Development workflow principles
- **MANUAL/ENVIRONMENT_STRATEGY.md**: Environment isolation principles
- **MANUAL/AI_PROMPTING_PATTERNS.md**: AI prompting principles
- **MANUAL/MODEL_ROLES.md**: AI agent role definitions
- **MANUAL/META_GUIDE.md**: AOSD for maintaining AOSD (propagation rules, dependency map)
- **MANUAL/RAG_GUIDE.md**: Guidance for building AOSD-aware retrieval systems

### Examples
- **MANUAL/examples/reference-implementations/**: Reference implementations
  - **level-2/**: Orchestration Profile examples (folder structure, agent config, workflows, invariants)
  - **aws/**: AWS platform examples (factory patterns, environment strategy)
  - **workflows/**: Workflow examples (AWS+Claude, prompting templates)
  - **model-assignments/**: AI model role assignment examples

### Training
- **SYLLABUS/**: Complete AI-powered learning curriculum (Modules 01-11)

### Strategic Decisions
- **DECISIONS.md**: Resolved framework-level design decisions with rationale and revisit triggers

### Non-Normative (Historical Context)
- **AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md**: Influences, prior art, acknowledgments, and citation guidance (does not define methodology requirements)

---

## For Users Adopting AOSD

If someone is using this framework to implement AOSD:

### They Should:
1. Read MANUAL/AOSD_FRAMEWORK.md to understand principles
2. Create their own Level 2 Orchestration Profile using IMPLEMENTATION_TEMPLATE.md
3. Study reference implementations as examples (not blueprints)
4. Use APP_TEMPLATE.md for each application's Level 3 docs
5. Adapt patterns to their specific context

### They Should NOT:
- Copy reference implementations verbatim
- Assume AWS-specific examples are requirements
- Skip documenting their own Level 2 choices
- Put application code in this repository

---

## When Helping Users

If a user is working on AOSD adoption:

### Ask About Their Level
- **Level 1 work**: They're updating the framework itself
- **Level 2 work**: They're creating/updating their Orchestration Profile
- **Level 3 work**: They're documenting a specific application

### Guide Appropriately
- Level 1: Keep it universal, platform-agnostic
- Level 2: Reference their platform docs, help document their choices
- Level 3: Focus on what makes their app unique

### Reference the Right Docs
- Learning AOSD → AOSD_FRAMEWORK.md
- Creating profile → IMPLEMENTATION_TEMPLATE.md
- Creating app docs → APP_TEMPLATE.md and AOSD_FOLDER_STRUCTURE.md
- Examples → reference-implementations/

---

## Framework Principles

From AOSD_FRAMEWORK.md, the core principles are:

1. **Multi-Agent Orchestration**: Humans orchestrate AI agents with specialized roles
2. **Trustworthy-by-Design**: Security, reliability, auditability as foundational
3. **Environment Isolation**: Clear boundaries between dev/staging/prod
4. **Architectural Invariants**: Non-negotiable rules prevent AI drift
5. **Slice-Based Development**: Vertical slices deliver end-to-end value
6. **TodoWrite Workflow**: Explicit planning and tracking
7. **Testing Strategy**: Fast/Medium/Slow test tiers
8. **Human-in-the-Loop**: Strategic human oversight
9. **Functional Core, Imperative Shell**: Separate pure logic from I/O
10. **Version Control**: Systematic branching for multi-agent work
11. **Work Item Lifecycle**: Trackable workflow from request to completion
12. **Self-Documenting Systems**: Systems explain themselves

**These are universal** - they apply regardless of cloud platform, AI agents, or tech stack.

---

## Tone and Style for Framework Docs

- **Inclusive**: "You can choose..." not "You must use AWS..."
- **Explanatory**: Explain the "why" behind principles
- **Flexible**: Offer options and tradeoffs
- **Concrete**: Use examples to illustrate concepts
- **Practical**: Provide templates and actionable guidance
- **Honest**: Acknowledge limitations and challenges

---

## Common Tasks

### Updating Core Framework
- Edit MANUAL/AOSD_FRAMEWORK.md
- Keep platform-agnostic
- **Check META_GUIDE.md for dependent artifacts that need updating**
- Bump version number
- Update CHANGELOG.md

### Adding New Templates
- Add to TEMPLATES/ folder
- Ensure they're generic/universal
- Document usage in README
- Update AI_GUIDE.md templates quick reference

### Creating New Examples
- Add to MANUAL/examples/reference-implementations/
- Sanitize (remove proprietary details)
- Show pattern, not production code
- Include README explaining context

### Updating Training
- Edit SYLLABUS/ modules
- Keep aligned with AOSD_FRAMEWORK.md
- Maintain AI-learning-friendly format
- Verify cross-references to framework

### Merging Changes
When merging a branch to main:
1. Ensure all changes are documented in CHANGELOG.md under `[Unreleased]` or a new version section
2. If this is a version release, update the version number and date
3. Update comparison links at the bottom of CHANGELOG.md
4. Verify CHANGELOG entry follows [Keep a Changelog](https://keepachangelog.com/) format

---

## Important Notes

- This framework is **maintained by Jim Wiedman**
- It represents a formalized methodology, not a work-in-progress
- Changes should enhance clarity and completeness
- Examples should be illustrative, not prescriptive
- The framework should remain accessible to all skill levels
- This repository contains **no executable code**—it's purely documentation

---

## For More Details

- **Core Methodology**: [AOSD_FRAMEWORK.md](MANUAL/AOSD_FRAMEWORK.md)
- **Applying AOSD Methodology**: [AI_GUIDE.md](AI_GUIDE.md)
- **Creating Profiles**: [IMPLEMENTATION_TEMPLATE.md](MANUAL/IMPLEMENTATION_TEMPLATE.md)
- **Application Setup**: [APP_TEMPLATE.md](TEMPLATES/APP_TEMPLATE.md)
- **Training**: [SYLLABUS/](SYLLABUS/)
- **Maintaining AOSD**: [META_GUIDE.md](MANUAL/META_GUIDE.md)
- **Strategic Decisions**: [DECISIONS.md](DECISIONS.md)
- **Influences & Citations**: [AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md](AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md) (non-normative)

---

**Remember**: This is Level 1 - the universal framework. Keep it platform-agnostic and accessible to all.
