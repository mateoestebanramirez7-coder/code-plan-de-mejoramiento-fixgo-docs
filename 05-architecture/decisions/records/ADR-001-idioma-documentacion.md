# ADR-001 — Documentation Language

| Field | Value |
|---|---|
| ID | ADR-001 |
| Date | 2026-09-11 |
| Status | Accepted |
| Authors | Johan — Tech Lead |
| Reviewers | Mateo Ramirez — Gabriel Tijaro — Juan David Romero — Development team |

---

## Context

The software industry standard operates in English across tooling, documentation, libraries, and frameworks. In FixGo, mixing languages creates ambiguity, slows development, and complicates maintainability. A single clear standard established early prevents naming inconsistencies and documentation drift.

---

## Evaluated alternatives

### Alternative A — Everything in English (CHOSEN)
- **Pros:** Aligns with industry standards; simplifies library integration; natively supported by GitHub, Stack Overflow, and AI tools; ensures clean codebase maintenance.
- **Cons:** Requires active communication discipline for non-native English speakers.

### Alternative B — Everything in Spanish
- **Pros:** Direct communication among local team members.
- **Cons:** Creates awkward syntax clashes with reserved keywords (if, return, for); incompatible with international engineering review standards.

### Alternative C — Split by layer (discarded)
- **Pros:** Contextual language targeting.
- **Cons:** High maintenance overhead; causes permanent translation lag between documentation and implementation.

---

## Decision

**Alternative A:** Adopt Alternative A: Use English as the primary source of truth for all documentation, architecture records, and code artifacts, supported by Spanish operational notes for team alignment.

| Artifact | Language | Reason |
|----------|----------|--------|
| Variables, functions, classes in code | English | Consistency with libraries and frameworks |
| Table and column names in DB | English | Coherence with the code that maps them |
| Commits (Conventional Commits) | English | Established standard, readable on GitHub |
| Git branch names | English | Consistent with commits |
| Markdown documentation | English | Eliminates the translation boundary; searchable |
| OpenAPI contracts (descriptions) | English | Readable by any future contributor |
| End-user error messages | English (or localized) | Localization layer handles language at runtime |
| Internal system logs | English | Facilitates search in library documentation and alerts |
| ADRs and technical documentation | English | Single source of truth, no translation boundary |

---

## Consequences

**Positive:**
- A single language across all artifacts eliminates the cognitive translation boundary
- New team members have one clear rule from day 1
- External contributors and AI tools work without friction
- Domain terms have one canonical form (the English one in the code)

**Negative:**
- Team members who are less confident in English need to invest more initially
- Some business terms may require careful translation decisions

**Mitigation:**
- Maintain a domain glossary with the canonical English translation for each business term: `01-context/glossary.md`
- When a term has a debatable translation, document it in the glossary before using it in code

---

## References

- Team documentation conventions → `00-governance/documentation-rules.md`
- Domain term glossary → `01-context/glossary.md`
