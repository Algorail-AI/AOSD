# Module 05  Security & Compliance by Design (with AI)
**AOSD Curriculum**
**Security, Compliance, and Trustworthy Systems (NIST SP 800-160)**

---

## 1. Purpose of This Module

This module teaches how AOSD builds **trustworthy, secure systems** under an AOSD workflow.
You will learn:

- How to combine AOSD with **NIST SP 800-160**
- How AOSD enforces "trustworthy-by-design" principles
- How to think in **DevSecOps / shift-left security**
- How to ensure multi-tenant and identity boundaries remain safe
- How architectural invariants enforce system trustworthiness
- How to use AI to catch security problems early
- How to ensure compliance for CMMC, NIST 800-171, NIST 800-53, FedRAMP, etc.

Security here is not a bolt-on  it is **embedded into architecture**, AI prompting, and slice development.

---

# 2. Security as a Design Input (Not an Output)

Traditional engineering:
> Build � Test � Harden � Patch

AOSD engineering:
> **Design � Harden � Build � Test � Verify**

Security is integrated into:

- slice specs
- TodoWrite plans
- AI prompting patterns
- AWS Factory & bundling invariants
- DynamoDB partitioning
- WAF enforcement
- prompt builder structures
- environment rules
- deployment workflows

Every slice must consider **security & compliance** from the start.

---

# 3. NIST SP 800-160 Trustworthy Systems  How AOSD Applies It

The trustworthy computing principles in NIST SP 800-160 align perfectly with your AOSD workflow.

### **3.1 Trustworthiness as an Architectural Property**
- Multi-tenant separation
- IAM least privilege
- SSM-only access
- WAF everywhere
- No raw boto3
- Predictable bundling
- Versioned decisions
- Deterministic test structure

### **3.2 Defensive Architecture Everywhere**
- No ambiguous state transitions
- No mutable global state
- Every action logged
- Every version preserved
- Single-table DDB eliminates surprise join logic
- Prompt builders enforce explainability

### **3.3 Preventive Controls Before Code Runs**
AOSD development means 90% of defects are caught during:

- design
- slice spec
- TodoWrite
- ChatGPT architectural review
- adherence to invariants

By the time Claude Code touches code, the system is already secure-by-design.

### **3.4 Human Checkpoints (HITL)**
NIST SP 800-160 emphasizes oversight.
AOSD uses:

- PR reviews
- architectural reviews
- Prod EC2-only deployments
- manual UAT
- Playwright validation

Human controls aren't optional  they're foundational.

---

# 4. DevSecOps & "Shift Left" Security (AOSD Interpretation)

Security is woven throughout the workflow:

### **Design Phase**
- ChatGPT includes risk analysis
- Specs call out IAM/WAF/DDB implications
- Invariants applied up front

### **Implementation Phase**
- Claude Code follows invariants
- Raw boto3 calls blocked
- Prompt rules protect secrets and consistency
- Multi-tenant boundaries enforced

### **Test Phase**
- Negative tests required
- Permission tests
- Integration tests validate bundling (catching common AWS issues)
- Playwright smoke tests catch UI/behavioral issues

### **Deployment Phase**
- Prod EC2-only
- MFA/SSM
- Security pre-deploy checks
- No shortcuts

Security is continuous, not episodic.

---

# 5. Multi-Tenant Security (Critical)

AOSD's multi-tenant isolation is not optional; it is the backbone of trustworthiness.

### Key protections:
- PK/SK patterns ensure strict scoping
- Tenant validation enforced in handlers
- No horizontal privilege escalation
- No unscoped queries
- No cross-tenant scans
- Decision systems scoped per tenant
- Revision instructions scoped per control version only

AOSD development must NEVER alter tenant isolation patterns.

---

# 6. IAM & Access Boundary Patterns

### **SSM-only access � No SSH**
- MFA mandatory
- No keys on laptops
- No long-lived credentials

### **Least Privilege IAM**
Each Lambda can only:

- access its own tables
- write to specific log groups
- read from specific S3 prefixes
- call specific AWS services

No wildcard permissions unless absolutely necessary.

Claude Code and ChatGPT must **never** expand permissions casually.

---

# 7. WAF & API Perimeter Security

WAF is part of the **trust boundary**:

- Every API protected
- No bypasses
- Rate limits enforced
- IP allowlisting optional
- CDK-nag rules enforced

AI agents must always apply the WAF Pattern.
No exceptions.

---

# 8. Prompt Security & Data Flow Safety

Prompt builders are a **data exposure surface**.

Invariants must enforce:

- No sensitive data in system prompts
- No PII stored in prompt builders
- Revision Instructions included only where safe
- Decision injection structured and predictable
- ODV injection only for frameworks that require it
- Safe token usage (avoid over-expansion)
- Never embed secrets in prompts

AI models must treat prompts as **internal API boundaries**, not text blobs.

---

# 9. Compliance Alignment (CMMC, NIST 800-171, NIST 800-53, FedRAMP)

AOSD's patterns align intentionally with compliance frameworks.

### **CMMC & NIST 800-171**
- Multi-tenant boundaries
- Access control everywhere
- Logging, monitoring
- SSM-only
- WAF perimeter
- Decision Catalog aligns with policy-driven configuration

### **NIST SP 800-53 / FedRAMP**
- ODV / assignment parameter infrastructure
- Versioned decisions
- AI-assisted documentation generation
- Traceability through prompt builders

### **Constrained Environments**
- Patching with constrained AI models
- No architecture changes in constrained environments
- Human oversight before promotion

AOSD supports compliance **by design**, not by checklist.

---

# 10. Using AI to Strengthen Security

AI helps enforce security by:

- identifying risky architectural choices
- catching missing invariants
- auto-detecting missing client validation
- catching bypasses of AWS Factory
- evaluating IAM impacts
- warning about cross-tenant risk
- enforcing bounded diffs in constrained environments

AI becomes a **security co-pilot** as long as invariants are enforced.

---

# 11. Constrained Environment Security Constraints

The strictest rules apply here:

- Constrained models cannot make multi-file changes
- Execution tools perform minimal diffs
- Planning tools approve/refine tasks
- You must manually sync changes back to development environment
- No architecture work here
- No secrets on restricted machines
- No uncontrolled IAM changes

Constrained environments = *surgical changes only*.

---

# 12. Exercises

### Exercise 1  Security Review
Pick a slice spec and evaluate it purely through the lens of:
- trustworthiness
- isolation
- compliance
- fundamental invariants

### Exercise 2  Negative Testing
Design test cases that ensure:
- denied access is denied
- invalid inputs trigger validation
- PK/SK scoping is enforced

### Exercise 3  Security Risk Classification
Ask ChatGPT to classify risks in a proposed change.

### Exercise 4  Prompt Security Audit
Take a prompt builder and check it for:
- sensitive data
- missing revision injection
- missing decision injection

---

# 13. Completion Criteria

You've mastered this module when:

- You can evaluate slices with a security mindset
- You can articulate how each AOSD invariant supports trustworthy design
- You can catch AI-generated security mistakes easily
- You can design slices that are compliant from the start
- You can clearly differentiate what work must happen in development vs constrained environments
- You understand the relationship between AOSD and traditional compliance frameworks

Next:
**Module 06  Constrained Environment Development**

---

**End of MODULE_05_SECURITY_AND_COMPLIANCE_WITH_AI.md**
