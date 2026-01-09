# Module 04  Environments & Deployments
**AOSD Curriculum**
**Development - (Staging) - Production + Constrained Environments**

---

## 1. Purpose of This Module

This module teaches the operational knowledge required to:

- work safely across **Dev**, **Prod**, and future **Staging**
- prevent environment drift
- apply the SSM-only access strategy
- integrate Playwright testing across environments
- deploy safely in an AOSD workflow
- understand environment boundaries for Claude Code & ChatGPT
- handle constrained environment restrictions using appropriate AI tools

You will learn how environments shape the way both humans and AIs build, deploy, and validate software.

---

# 2. The AOSD Environment Model (Jim's Workflow)

### **2.1 Primary Development Environment**
- All application code lives here
- All git operations occur here
- All `make test-*` commands run here
- All deployments to Dev environment originate here
- Editor = `vi`/`vim`
- AI implementer = **Claude Code** (running inside terminal)
- No VS Code, no SSH  **SSM-only access**

### **2.2 Prod EC2  Controlled Deployment Environment**
- Receives only code merged into `main`
- Only used for Staging/Prod deployment operations
- All Prod tests, security checks, and deployments happen here
- NEVER used for feature development
- Same SSM-only, terminal-based workflow

### **2.3 Constrained Environments**
- Accessed via secure channels
- Local editor = **VS Code** on restricted machine
- AI agents = **Constrained models with limited context**
- Only **atomic, single-file** changes allowed
- Architectural work must occur in primary development environment

### **2.4 Laptop  Thin Client**
- Runs Playwright tests
- Browser testing
- Terminal for SSM into EC2
- No authoritative code stored locally

---

# 3. Responsibilities by Environment

| Layer | Development | Staging | Production | Constrained |
|------|---------|---------|----------|---------------|
| Coding |  |  |  | Small fixes only |
| Testing | Fast/Med/Slow | Regression | Prod-safe | Minimal |
| Deployment | Dev-only | Yes | Yes | Environment-specific workflow only |
| AI Tools | Claude Code | Claude Code (optional) | None (deploy only) | Constrained models |
| Editor | vi/vim | vi/vim | vi/vim | VS Code |
| Risk | Low | Medium | High | High |

---

# 4. Deployment Lifecycle

## 4.1 Dev Deployment (Day-to-day)

Performed in development environment:

```
ENV=dev make deploy
```

Used for:

- feature development
- integration testing
- Playwright Dev testing

Human steps:

1. Implement slice via TodoWrite
2. Run fast/medium/slow tests
3. Deploy to Dev
4. Validate manually
5. Run Playwright Dev suite

AI steps:

- Claude Code may issue `ENV=dev make deploy` after tests pass
- Claude Code must NEVER deploy to Prod

---

# 5. Staging Deployment (Future Support)

When Staging is implemented:

Performed from Prod EC2:

```
ENV=stg make deploy
```

Used for:

- pre-production validation
- regression testing
- UAT
- Prod-like simulations

Staging should mirror Prod infrastructure 1:1 except for data.

---

# 6. Prod Deployment

Only performed on **Prod EC2** with strict safety gates.

### **6.1 Pre-Deployment Tests:**
```
ENV=prod make test
ENV=prod make security-pre-deploy
```

### **6.2 Deploy:**
```
ENV=prod make deploy
```

### **6.3 Validate with Smoke Playwright (from laptop)**

Run:

```
npx playwright test --config=playwright.prod.config.ts --grep @smoke
```

Prod playbooks must be **non-destructive**.

---

# 7. Playwright in Multi-Environment Validation (Jim Workflow)

### Dev:
- Full E2E flows
- Destructive tests allowed
- Used to validate slices end-to-end

### Staging:
- Full regression (future)
- Mirrors Prod behavior

### Prod:
- Only smoke tests
- No destructive operations

Playwright serves as the **outer loop** validation and final confirmation of correctness.

---

# 8. Using ChatGPT & Claude Code Across Environments

### **8.1 ChatGPT**
- Works outside EC2
- Writes slice specs
- Identifies architectural risks
- Reviews deployments
- Cannot run commands or modify environments
- Must be aware of Dev/Stg/Prod rules

### **8.2 Claude Code**
- Lives inside development environment terminal
- Implements slice logic
- Runs tests
- Deploys to Dev
- Prepares PRs
- Must never touch Prod without instruction

### **8.3 Constrained Environment AI Tools**
- Used only in constrained environments
- Keeps changes atomic
- Must warn if the change is too large or multi-file
- Never perform deployment operations

---

# 9. Constrained Environment: Special Considerations

Constrained environments require:

- Zero architectural changes
- Zero multi-file edits
- Short, explicit, safe patching
- Manual validation
- Manual sync-back to primary development environment afterward
- Planning tools for task decomposition
- Execution tools for minimal changes
- Constrained models for safe, bounded edits

Constrained environments participate in maintenance, not in architecture.

---

# 10. Security Considerations in Environments

- MFA required for all EC2 access
- SSM-only access (no SSH, no port 22)
- IAM least privilege
- No environment secrets on laptops
- No Dev � Prod shortcuts
- WAF must be deployed consistently across environments
- Multi-tenant isolation enforced everywhere

Security posture is a **design property**, not a separate phase.

---

# 11. Exercises

### Exercise 1  Deploy to Dev
Pick a slice and run a full Dev deploy cycle.

### Exercise 2  Simulate Staging
Explain how you would validate the slice if Staging existed today.

### Exercise 3  Prod Dry-Run
Explain which parts of the deploy process you can rehearse in Dev, and which must occur in Prod.

### Exercise 4  Constrained Environment Split
Take a medium-sized fix and split it into development environment work + constrained environment micro-fixes.

---

# 12. Completion Criteria

You've mastered this module when you can:

- Safely deploy to Dev without hesitation
- Understand how Staging will slot into the pipeline
- Perform controlled Prod deployments
- Run Dev and Prod Playwright tests appropriately
- Understand the absolute separation of responsibilities
- Avoid environment drift
- Understand how different AI tools participate in environment workflows
- Recognize when something must be done in development vs constrained environments
- Follow AOSD's environment strategy without thinking

When this feels natural, move on to:

**Module 05  Security & Compliance by Design**

---

**End of MODULE_04_ENVIRONMENTS_AND_DEPLOYMENTS.md**
