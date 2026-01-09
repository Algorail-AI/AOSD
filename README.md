# AOSD Framework
**AI Orchestrated Systems Development**

**Copyright 2025–2026 Algorail AI, LLC**

**Author: Jim Wiedman, co-created with AI collaborators ChatGPT and Claude**

---

## What is AOSD?

**AOSD (AI Orchestrated Systems Development)** is a methodology for building software systems by orchestrating multiple AI agents. It provides:

- **Environment-agnostic principles** for AI-assisted development
- **Multi-agent orchestration** patterns and practices
- **Architectural invariants** that prevent AI drift
- **Security-first** design principles
- **Slice-based development** for incremental delivery
- **Human-in-the-loop** governance model

AOSD enables solo developers and small teams to build production-grade applications with AI tools like Claude Code, ChatGPT, and others.

---

## For AI Agents

- **Operating in this repository**: Read [AGENT.md](AGENT.md) — the canonical entry point for all AI agents
- **Using AOSD methodology**: Read [AI_GUIDE.md](AI_GUIDE.md) for applying AOSD to build systems

---

## Start Here: Minimal AOSD Setup

New to AOSD? Start with these three things:

1. **Read Goals & Principles** (30 min): Skim [AOSD_FRAMEWORK.md](MANUAL/AOSD_FRAMEWORK.md) sections "Why AOSD Exists" and the 12 Core Principles
2. **Write 5 Invariants** (15 min): Document non-negotiable rules for your project (e.g., "All API endpoints require authentication", "No direct database access from handlers")
3. **Create Your First Slice Spec** (15 min): Use [SLICE_SPEC_TEMPLATE.md](TEMPLATES/SLICE_SPEC_TEMPLATE.md) for one small feature

That's it. You're practicing AOSD. Everything else—multi-agent workflows, Level 2 profiles, advanced patterns—builds on these foundations.

**Already comfortable?** See [Quick Start](#quick-start) for full setup paths, or dive into the [SYLLABUS](SYLLABUS/) for structured learning.

---

## Three-Level Architecture

AOSD uses a three-tier structure to separate universal methodology from specific implementation choices:

### Level 1: AOSD Framework (This Repository)

**Universal methodology** - environment-agnostic principles anyone can adopt

- Core AOSD principles and patterns
- Multi-agent orchestration model
- Testing strategies
- Security and compliance guidelines
- Templates for creating implementations

**For**: Anyone learning or adopting AOSD methodology

---

### Level 2: Orchestration Profile

**Your specific implementation** - cloud platform, AI agents, tools, and environment setup

Examples of Level 2 documentation:
- Cloud platform choice (AWS, Azure, GCP, etc.)
- AI agent selection (Claude Code, ChatGPT, Copilot, etc.)
- Development environment setup
- Security and access patterns
- Organization-wide architectural invariants

**For**: Your organization's projects
**Location**: Your own repository (see [Implementation Template](MANUAL/IMPLEMENTATION_TEMPLATE.md))

**Reference Example**: See [AWS Reference Implementation](MANUAL/examples/reference-implementations/aws/) for patterns

---

### Level 3: Application-Specific

**Project-specific patterns** - unique invariants and decisions for each application

- Lives in each application repository in an `AOSD/` folder
- Application-specific invariants
- Project-specific patterns
- Architecture Decision Records (ADRs)
- Slice specifications

**For**: Each individual application
**Location**: In application repo at `AOSD/`
**Template**: [APP_TEMPLATE.md](TEMPLATES/APP_TEMPLATE.md) and [AOSD Folder Structure](TEMPLATES/AOSD_FOLDER_STRUCTURE.md)

---

## Quick Start

### Learning AOSD

1. **Read the Framework**: Start with [AOSD_FRAMEWORK.md](MANUAL/AOSD_FRAMEWORK.md)
2. **Review Examples**: Study [AWS Reference Implementation](MANUAL/examples/reference-implementations/aws/)
3. **Study the Syllabus**: Work through [SYLLABUS/](SYLLABUS/) for structured learning

### Creating Your Orchestration Profile (Level 2)

1. **Use the Template**: Copy [IMPLEMENTATION_TEMPLATE.md](MANUAL/IMPLEMENTATION_TEMPLATE.md)
2. **Document Your Choices**: Cloud platform, AI agents, environments, etc.
3. **Create ADRs**: Document major decisions
4. **Build Pattern Library**: Collect reusable patterns

### Starting an Application (Level 3)

1. **Create AOSD/ Folder**: In your application repository
2. **Use Templates**: Copy [APP_TEMPLATE.md](TEMPLATES/APP_TEMPLATE.md) to `AOSD/AOSD.md`
3. **Follow Structure Guide**: See [AOSD_FOLDER_STRUCTURE.md](TEMPLATES/AOSD_FOLDER_STRUCTURE.md)
4. **Document Specifics**: App-specific invariants and patterns

---

## Repository Structure

```
AOSD/                               # AOSD Framework (Level 1)
├── README.md                       # This file
├── AGENT.md                        # Tool-agnostic operator contract for AI agents
├── AI_GUIDE.md                     # AI agent guide for applying AOSD methodology
├── CHANGELOG.md                    # Version history
├── DECISIONS.md                    # Strategic framework decisions
├── MANUAL/
│   ├── AOSD_FRAMEWORK.md           # Core methodology
│   ├── IMPLEMENTATION_TEMPLATE.md  # Create your Orchestration Profile
│   ├── TROUBLESHOOTING.md          # Common issues and solutions
│   ├── DEVELOPMENT_WORKFLOW.md     # Workflow principles
│   ├── ENVIRONMENT_STRATEGY.md     # Environment isolation principles
│   ├── AI_PROMPTING_PATTERNS.md    # AI prompting principles
│   ├── MODEL_ROLES.md              # AI agent role definitions
│   └── examples/
│       └── reference-implementations/
│           ├── aws/                # AWS platform examples
│           ├── workflows/          # Workflow implementation examples
│           └── model-assignments/  # Model role assignment examples
├── TEMPLATES/
│   ├── APP_TEMPLATE.md             # Level 3 app documentation
│   ├── AOSD_FOLDER_STRUCTURE.md    # Level 3 folder structure guide
│   ├── ADR_TEMPLATE.md             # Architecture Decision Records
│   ├── SLICE_SPEC_TEMPLATE.md      # Slice specification template
│   ├── TODO_WRITE_TEMPLATE.md      # TodoWrite template
│   ├── REVIEW_CHECKLIST_TEMPLATE.md
│   └── SPEC_TEMPLATE.md
└── SYLLABUS/                       # AI-powered learning curriculum
    ├── COURSE_OVERVIEW.md
    ├── MODULE_01_FOUNDATIONS.md
    ├── MODULE_02_ARCHITECTURE.md
    └── ... (modules 01-11)
```

---

## Who Should Use AOSD?

### Solo Founders & Small Teams
- Build production applications with AI assistance
- Maintain quality without large teams
- Leverage AI for architecture and implementation

### Development Teams
- Adopt AI-native development methodology
- Maintain consistency across AI agent sessions
- Scale development with AI orchestration

### AI Agents (Claude Code, ChatGPT, etc.)
- Receive consistent context and constraints
- Follow architectural invariants
- Understand role boundaries

### Organizations
- Standardize AI-assisted development
- Create organization-wide patterns
- Ensure security and compliance

---

## Core Principles

From the [AOSD Framework](MANUAL/AOSD_FRAMEWORK.md):

1. **Multi-Agent Orchestration**: Different AI agents handle different aspects (architecture, implementation, review)
2. **Trustworthy-by-Design**: Security, reliability, and auditability as foundational requirements
3. **Environment Isolation**: Clear boundaries between dev, staging, and production
4. **Architectural Invariants**: Non-negotiable rules that prevent AI drift
5. **Slice-Based Development**: Vertical slices that deliver end-to-end value
6. **TodoWrite Workflow**: Explicit task planning and tracking
7. **Testing Strategy**: Fast/Medium/Slow test layers
8. **Human-in-the-Loop**: Humans maintain strategic control
9. **Functional Core, Imperative Shell**: Separate pure logic from side effects
10. **Version Control**: Systematic branching for multi-agent environments
11. **Work Item Lifecycle**: Trackable workflow from request to completion

---

## Example Use Case: Sally's Azure Implementation

Sally works at a different organization and wants to adopt AOSD for Azure:

1. **Learn**: Sally reads this repository (Level 1 Framework)
2. **Adapt**: Sally creates her own Orchestration Profile using [IMPLEMENTATION_TEMPLATE.md](MANUAL/IMPLEMENTATION_TEMPLATE.md)
3. **Document**: Sally documents her Azure choices, AI agent selections, and patterns
4. **Apply**: For each application, Sally uses [APP_TEMPLATE.md](TEMPLATES/APP_TEMPLATE.md)
5. **Reference**: Sally studies the [AWS examples](MANUAL/examples/reference-implementations/aws/) and translates patterns to Azure

Sally now has:
- **Level 1**: This framework (universal principles)
- **Level 2**: Her Azure Orchestration Profile (her specific choices)
- **Level 3**: Application-specific docs in each app's `AOSD/` folder

---

## AI-Powered Training

The [SYLLABUS/](SYLLABUS/) directory contains an AI-powered learning system designed for NotebookLM and similar tools:

**Core Modules (1-7)**:
- AOSD Foundations
- Architecture with Multi-Agent Orchestration
- Testing & TDD in AOSD
- Environments & Deployments
- Security & Compliance by Design
- Constrained Environments
- Advanced Multi-Agent Workflows

**Advanced Modules (8-11)**:
- AI as a Runtime Component
- Invariants Deep Dive
- Framework Evolution & Meta-Governance
- Parallel Multi-Agent Development

Use NotebookLM to ingest the syllabus for interactive, personalized AOSD training.

---

## Key Documents

### Framework Documentation
- [AOSD_FRAMEWORK.md](MANUAL/AOSD_FRAMEWORK.md) - Core methodology
- [IMPLEMENTATION_TEMPLATE.md](MANUAL/IMPLEMENTATION_TEMPLATE.md) - Create your profile
- [DECISIONS.md](DECISIONS.md) - Strategic framework decisions
- [TROUBLESHOOTING.md](MANUAL/TROUBLESHOOTING.md) - Common issues and solutions

### Templates
- [APP_TEMPLATE.md](TEMPLATES/APP_TEMPLATE.md) - Application documentation
- [AOSD_FOLDER_STRUCTURE.md](TEMPLATES/AOSD_FOLDER_STRUCTURE.md) - Application folder structure
- [SLICE_SPEC_TEMPLATE.md](TEMPLATES/SLICE_SPEC_TEMPLATE.md) - Slice specifications
- [ADR_TEMPLATE.md](TEMPLATES/ADR_TEMPLATE.md) - Architecture decisions

### Examples
- [Reference Implementations](MANUAL/examples/reference-implementations/) - Platform and workflow examples
  - [AWS Examples](MANUAL/examples/reference-implementations/aws/) - AWS platform patterns
  - [Workflow Examples](MANUAL/examples/reference-implementations/workflows/) - Development workflow implementations

### Training
- [SYLLABUS/](SYLLABUS/) - Complete AI-powered learning curriculum

---

## Platform Support

AOSD is **platform-agnostic**. The framework works with:

- ☁️ **Cloud Platforms**: AWS, Azure, GCP, on-premises, hybrid
- 🤖 **AI Agents**: Claude Code, ChatGPT, GitHub Copilot, Cursor, Continue, Amazon Q, or others
- 🏗️ **Architectures**: Serverless, containers, VMs, microservices, monoliths
- 💼 **Organizations**: Solo developers to large teams

The [reference implementations](MANUAL/examples/reference-implementations/) show how to apply AOSD to specific platforms.

---

## Contributing

AOSD is maintained by Algorail AI. We welcome:

- Bug reports and questions via GitHub Issues
- Documentation fixes and improvements
- New examples that demonstrate AOSD principles

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines and [GOVERNANCE.md](GOVERNANCE.md) for how AOSD is maintained.

You're also welcome to fork and adapt AOSD for your own use, or create your own Orchestration Profile using the framework as a foundation.

---

## License

Copyright 2025–2026 Algorail AI, LLC. Licensed under the [Apache License 2.0](LICENSE).

See [NOTICE](NOTICE) for attribution and trademark information.

---

## Trademark

AOSD™ and AI-Orchestrated Systems Development™ are trademarks of Algorail AI, LLC.

---

## Getting Help

- **Documentation**: Start with [AOSD_FRAMEWORK.md](MANUAL/AOSD_FRAMEWORK.md)
- **Examples**: Review [reference implementations](MANUAL/examples/reference-implementations/aws/)
- **Issues**: See [GitHub Issues](https://github.com/Algorail-AI/AOSD/issues)

---

## About

Created by **Jim Wiedman** in collaboration with AI agents (ChatGPT and Claude) to formalize and share the AOSD methodology.

AOSD represents a paradigm shift in software development, enabling humans to orchestrate AI agents to build secure, production-grade applications efficiently.

**Version**: 1.0 (see [CHANGELOG.md](CHANGELOG.md) for version history)
