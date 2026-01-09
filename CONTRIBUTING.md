# Contributing to AOSD

Thank you for your interest in contributing to AOSD. This document explains how to contribute effectively.

---

## Before You Contribute

Please read [GOVERNANCE.md](GOVERNANCE.md) to understand how AOSD is maintained. Key points:

- Algorail AI is the steward of AOSD
- The roadmap is not crowdsourced
- We welcome input but may decline changes that don't fit the framework's direction

---

## What We Accept

### Always Welcome

- **Documentation fixes** — Typos, broken links, grammar
- **Clarifications** — Making existing content clearer
- **Examples** — New examples that demonstrate AOSD principles
- **Bug reports** — Issues with documentation or examples

### Sometimes Welcome

- **New patterns** — Patterns validated through practice
- **Reference implementations** — Platform-specific examples
- **Template improvements** — Better templates for AOSD workflows

### Rarely Accepted

- **Core principle changes** — These require extensive validation
- **New invariants** — Must be proven through practice first
- **Architectural shifts** — Major direction changes

---

## How to Contribute

### 1. For Bug Reports and Questions

Open a GitHub Issue:

1. Check existing issues first
2. Use a clear, descriptive title
3. Provide context and examples
4. For bugs: describe expected vs actual behavior

### 2. For Documentation Fixes

Submit a Pull Request:

1. Fork the repository
2. Create a branch (`fix/typo-in-framework` or similar)
3. Make your changes
4. Submit PR with clear description

Small fixes (typos, broken links) can go directly to PR without an issue.

### 3. For New Examples or Content

Open an Issue first:

1. Describe what you want to add
2. Explain how it fits AOSD principles
3. Wait for feedback before investing time
4. If approved, submit PR

### 4. For Larger Changes

Definitely open an Issue first:

1. Describe the change and rationale
2. Explain how it's been validated in practice
3. Discuss scope and approach
4. Only proceed with PR after alignment

---

## Style Guidelines

### Writing

- Clear, concise language
- Active voice preferred
- Avoid jargon without explanation
- Include examples where helpful

### Formatting

- Use standard Markdown
- Consistent heading hierarchy
- Code blocks with language hints
- Tables for structured comparisons

### Content Principles

- **Platform-agnostic** — Avoid vendor-specific content in core docs
- **Practical** — Prefer tested patterns over theoretical ideas
- **Minimal** — Add essential value, avoid bloat
- **Consistent** — Align with existing style and principles

### Context Independence Rule

AOSD is an open-source framework. All documentation must be reusable by anyone, regardless of what projects they work on.

**When editing AOSD documentation:**

1. **Never reference specific projects, repos, or issue numbers** from any real codebase
2. **Use generic examples**: `#123`, `SLICE-042`, `feature-xyz` — not real identifiers
3. **Anonymize real cases**: If a real situation informs an example, strip identifying details
4. **Platform-agnostic language**: Minimize vendor-specific examples; when unavoidable (e.g., AWS in infrastructure examples), keep them clearly labeled as one option among many

**Zone definitions:**
- **Core docs** (MANUAL/, principles, patterns): Must be 100% generic
- **Case studies** (examples/case-studies/): May reference real patterns but must be anonymized or explicitly labeled
- **Templates**: Use placeholder values (`[description]`, `#XXX`, `your-project`)

**If you're unsure**: Ask before including project-specific references.

---

## Pull Request Process

1. **Branch naming**: `fix/description`, `docs/description`, `example/description`
2. **Commit messages**: Clear, descriptive (see existing commits for style)
3. **PR description**: Explain what and why
4. **Review**: All PRs require review before merge
5. **Changes requested**: Address feedback or discuss

### PR Checklist

Before submitting:

- [ ] Read GOVERNANCE.md and CONTRIBUTING.md
- [ ] Checked existing issues/PRs for duplicates
- [ ] Followed style guidelines
- [ ] Tested any examples or code
- [ ] Links work and point to correct locations
- [ ] No project-specific references (per Context Independence Rule)

---

## What Happens Next

After you contribute:

1. **Acknowledgment** — We'll respond to issues and PRs
2. **Review** — We'll review and provide feedback
3. **Iteration** — You may be asked to make changes
4. **Decision** — We'll merge, request changes, or explain why we're declining
5. **Attribution** — Contributors are acknowledged in relevant commits

---

## Questions?

- Check [GOVERNANCE.md](GOVERNANCE.md) for governance questions
- Open an Issue for contribution questions
- Review existing PRs for examples

---

*We appreciate your interest in AOSD. Even if a contribution isn't accepted, your engagement helps improve the framework.*
