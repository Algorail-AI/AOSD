# Restrictive Environment Reference Implementations

**AOSD Level 2 Examples - Constrained Environments**

---

## Purpose

This directory contains reference implementations showing how AOSD principles can be adapted for restrictive or constrained environments where full AI capabilities are unavailable. These examples demonstrate patterns for maintaining AOSD compliance when facing limitations.

---

## What's Included

### Limited AI Capability Patterns
**File**: `limited-ai-capability-patterns.md`

Patterns for working with constrained AI capabilities:
- Planner + Executor separation
- Single-file change discipline
- Context window management
- Human-augmented reasoning strategies
- Degraded capability fallbacks

---

## How to Use These Examples

### For Learning
1. Identify which constraints apply to your environment
2. Study the patterns that address your specific limitations
3. Combine patterns as needed for your unique situation
4. Adapt terminology and tools to your context

### For Your Own Implementation
1. Document your environment's specific constraints
2. Map AOSD principles to available capabilities
3. Create explicit fallback strategies
4. Train team members on constraint-aware workflows

---

## Common Restrictive Environment Types

| Environment Type | Key Constraints | Recommended Patterns |
|------------------|-----------------|----------------------|
| **Network-Isolated** | Limited external network | Local models, manual sync |
| **Limited AI Models** | Limited AI models | Planner/Executor separation |
| **Corporate Restricted** | Approved tools only | Work within approved toolchain |
| **Low-Bandwidth** | Slow/expensive network | Aggressive caching, offline-first |
| **High-Security** | Audit requirements | Explicit logging, approval gates |

---

## Related Documents

- [Adapting AOSD to Your Environment](../../../AOSD_FRAMEWORK.md#-adapting-aosd-to-your-environment) - Generalized principles
- [Troubleshooting - Restrictive Environments](../../../TROUBLESHOOTING.md#restrictive-or-offline-environments) - Common issues and solutions

---

**Note**: These are sanitized reference implementations. Real implementations will require adaptation for specific organizational and regulatory requirements.
