# RAG Guide

**Operational Guidance for AOSD-Aware Retrieval Systems**

---

> **RAG_GUIDE.md is normative about retrieval behavior, not about retrieval technology.**
>
> This document specifies *what* retrieval systems must do, not *how* to implement it.

---

## 1. Purpose

This guide provides operational requirements for engineers building retrieval systems against AOSD documentation. It ensures that:

- Retrieval respects the AOSD level hierarchy (L1 > L2 > L3)
- Dependency relationships from META_GUIDE.md are honored
- Normative content takes precedence over non-normative
- Results are semantically correct for governance queries

**Audience**: Engineers building RAG systems, knowledge bases, agent tooling, or search infrastructure that consumes AOSD documentation.

---

## 2. Metadata Inference from Paths

Retrieval systems MUST infer metadata from file paths to determine document level and normative status.

### Path-to-Metadata Mapping

| Path Pattern | Level | Category | Normative |
|--------------|-------|----------|-----------|
| `MANUAL/AOSD_FRAMEWORK.md` | L1 | Core Framework | Yes |
| `MANUAL/*.md` (except examples/) | L2 | Operational | Yes |
| `MANUAL/examples/**` | L3 | Reference | No |
| `SYLLABUS/**` | L2 | Training | Yes |
| `TEMPLATES/**` | L2 | Templates | Yes |
| `AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md` | — | Historical | No |

### Implementation Notes

- Path inference MUST happen at indexing time
- Metadata MUST be stored with chunks for filtering and ranking
- Unknown paths default to L3, non-normative

---

## 3. Precedence Rules

AOSD has a strict precedence hierarchy. Retrieval systems MUST respect this hierarchy in ranking and conflict resolution.

### Hierarchy (Highest to Lowest)

1. **L1 (Framework)**: AOSD_FRAMEWORK.md — source of truth
2. **L2 (Operational)**: MANUAL/, SYLLABUS/, TEMPLATES/ — elaborates L1
3. **L3 (Reference)**: examples/, application-specific — illustrates L2

### Retrieval Behavior

| Scenario | Required Behavior |
|----------|-------------------|
| L1 and L2 both match query | L1 content ranks higher |
| L2 and L3 both match query | L2 content ranks higher |
| L3 content contradicts L1/L2 | L3 content MUST NOT override L1/L2 in response |
| Normative and non-normative match | Normative ranks higher |

### Critical Rule

> Retrieval MUST NOT allow downstream content to contradict upstream content.

If an L3 example shows a pattern that conflicts with L1 principles, the retrieval system must either:
- Exclude the L3 content, or
- Explicitly note the precedence conflict in the response

---

## 4. Dependency Expansion

When retrieving content, systems MUST check META_GUIDE.md for declared dependencies and expand context accordingly.

### Why This Matters

META_GUIDE.md documents which artifacts depend on others. A query about a specific topic may require context from upstream documents to be semantically complete.

### Expansion Rules

| Query Target | Required Expansion |
|--------------|-------------------|
| SYLLABUS module content | Include relevant AOSD_FRAMEWORK.md section |
| Template usage | Include principle the template implements |
| Example implementation | Include pattern being demonstrated |

### Implementation

1. Parse META_GUIDE.md dependency map at indexing time
2. Store dependency relationships with each chunk
3. When a chunk is retrieved, include upstream normative context
4. Order results: upstream context first, then target content

> **Dependency expansion is explicit semantic intent, not optional enhancement.**

---

## 5. Chunking Guidance

Retrieval systems MUST chunk AOSD documents in ways that preserve semantic coherence.

### Chunking Rules

| Rule | Rationale |
|------|-----------|
| Chunk by markdown section (## heading) | Sections are semantic units |
| Respect `<!-- AOSD:SECTION:* -->` anchors | These are stable chunk boundaries |
| Keep code blocks atomic | Splitting code blocks loses meaning |
| Keep tables atomic | Splitting tables loses structure |
| Include section heading with chunk | Provides context for the chunk |

### Section Anchor Format

AOSD uses HTML comment anchors as stable identifiers:

```html
<!-- AOSD:SECTION:principle-4-invariants -->
## Principle 4: Architectural Invariants
```

These anchors:
- Remain stable even if headings change
- Provide machine-readable section identifiers
- Should be used as chunk boundaries when present

### Chunk Size Guidance

- Prefer semantic completeness over fixed token limits
- If a section exceeds context limits, split at subsection (###) boundaries
- Never split mid-paragraph or mid-list

---

## 6. Anchor Stability

AOSD uses stable anchors for cross-referencing. Retrieval systems SHOULD leverage these for linking and identification.

### Anchor Format

```html
<!-- AOSD:SECTION:<identifier> -->
```

### Usage in Retrieval

| Use Case | Recommendation |
|----------|----------------|
| Linking to content | Use anchor identifiers, not heading text |
| Chunk identification | Include anchor in chunk metadata |
| Cross-references | Resolve anchor references during expansion |

### Why Anchors Over Headings

- Headings may be reworded for clarity
- Anchors are explicitly stable identifiers
- Anchors enable programmatic cross-referencing

---

## 7. Normative vs Non-Normative Content

Retrieval systems MUST distinguish between normative (authoritative) and non-normative (illustrative) content.

### Classification

| Document Type | Normative Status | Retrieval Weight |
|---------------|------------------|------------------|
| AOSD_FRAMEWORK.md | Normative | Highest |
| MANUAL/*.md (operational) | Normative | High |
| SYLLABUS/*.md | Normative | High |
| TEMPLATES/*.md | Normative | High |
| examples/** | Non-normative | Medium |
| INFLUENCES_AND_ACKNOWLEDGMENTS.md | Non-normative | Low |
| Archived/deprecated content | Non-normative | Lowest (or filtered) |

### Retrieval Behavior

| Scenario | Behavior |
|----------|----------|
| Query about AOSD requirements | Prioritize normative sources |
| Query about implementation examples | Include non-normative but note status |
| Query returns only non-normative | Flag that no authoritative source matched |

### Deprecation Handling

Content marked as deprecated or archived:
- SHOULD be deprioritized in results
- MAY be filtered entirely for governance queries
- MUST be labeled if included in responses

---

## 8. Query Classification

Different query types require different retrieval strategies.

### Query Types

| Type | Example | Strategy |
|------|---------|----------|
| **Definitional** | "What is an invariant?" | L1 first, expand with L2 |
| **Procedural** | "How do I create a slice spec?" | TEMPLATES first, include principle context |
| **Governance** | "What are the rules for X?" | Normative only, strict precedence |
| **Example-seeking** | "Show me an example of..." | L3 examples, but include L1/L2 context |

### Implementation Notes

- Query classification can be keyword-based or semantic
- Governance queries SHOULD filter to normative content
- Example queries SHOULD include upstream context to prevent misuse

---

## 9. Explicit Non-Goals

This guide intentionally excludes:

| Exclusion | Rationale |
|-----------|-----------|
| Platform-specific guidance | No AWS, Azure, GCP, or vendor references |
| Embedding model recommendations | Technology choice is implementation-specific |
| Vector database guidance | Technology choice is implementation-specific |
| Prompt engineering for RAG | Depends on LLM and use case |
| Performance optimization | Depends on infrastructure |

### Where Technology Guidance Belongs

Technology-specific implementation guidance belongs in:
- Your organization's L2 Orchestration Profile
- Separate implementation documentation
- Vendor-specific integration guides

---

## 10. Validation Checklist

Use this checklist to validate an AOSD-aware retrieval implementation:

### Metadata and Classification

- [ ] Path-based metadata inference implemented
- [ ] Level (L1/L2/L3) stored with each chunk
- [ ] Normative status stored with each chunk
- [ ] Document category (core/operational/training/template/example) tracked

### Precedence

- [ ] L1 content ranks above L2 for same query
- [ ] L2 content ranks above L3 for same query
- [ ] Normative content ranks above non-normative
- [ ] Conflicts are detected and handled (not silently merged)

### Dependency Expansion

- [ ] META_GUIDE.md dependencies parsed
- [ ] Upstream context included with downstream content
- [ ] Expansion happens automatically, not on demand

### Chunking

- [ ] Chunks respect section boundaries
- [ ] AOSD:SECTION anchors used as boundaries when present
- [ ] Code blocks and tables kept atomic
- [ ] Section headings included with chunks

### Content Handling

- [ ] Deprecated content deprioritized or filtered
- [ ] Non-normative content labeled in responses
- [ ] Query type influences retrieval strategy

---

## 11. Related Documents

- [META_GUIDE.md](./META_GUIDE.md) — Artifact dependency map and propagation rules
- [AOSD_FRAMEWORK.md](./AOSD_FRAMEWORK.md) — L1 source of truth
- [AI_GUIDE.md](../AI_GUIDE.md) — AI agent consumption guidance (different audience)

---

**End of RAG_GUIDE.md**
