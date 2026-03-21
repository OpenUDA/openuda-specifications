# OpenUDA UD Lifecycle Governance

**Status:** Draft  
**Applies to:** All OpenUDA Universal Documents (UDs)  

---

# 1. Purpose

This document defines the governance process for the lifecycle of OpenUDA Universal Documents (UDs).

The goal of this process is to ensure that new documents and revisions are introduced in a controlled manner that preserves:

- interoperability
- stability
- low entropy
- long-term maintainability

This governance model allows OpenUDA to evolve while preventing uncontrolled schema proliferation.

---

# 2. UD Lifecycle Overview

Each Universal Document progresses through the following lifecycle stages:

Proposal → Draft → Review → Approved → Published → Deprecated → Retired

These stages ensure that new documents are properly evaluated before becoming part of the OpenUDA specification family.

---

# 3. Proposal Stage

A new UD begins with a proposal.

A proposal SHOULD include:

- the proposed document name
- the business purpose of the document
- industries or ecosystems that require the document
- justification for why the document cannot be represented by an existing UD
- an initial draft structure

Example proposal topics:

- Credit Memo
- Purchase Order
- Shipment Notice
- Payment Remittance

Proposals SHOULD be submitted through the OpenUDA repository using:

- a GitHub issue
- a pull request containing an initial draft specification

---

# 4. Draft Stage

Once accepted for exploration, the document enters Draft status.

During this stage:

- the document structure is defined
- core fields are identified
- validation rules are proposed
- relationships to existing UDs are evaluated

Draft documents SHOULD follow the OpenUDA Canonical Design Principles.

Draft documents are considered experimental and may change significantly.

---

# 5. Review Stage

During the Review stage, the proposed UD is evaluated by the OpenUDA community.

Review activities may include:

- interoperability evaluation
- schema simplification
- alignment with canonical design principles
- validation rule testing
- industry feedback

Review MUST confirm that:

- the UD defines a clear and minimal canonical core
- the core structure is stable and reusable across implementations
- extension points are clearly defined
- the design does not introduce alternative structural patterns

Major structural issues SHOULD be resolved during this phase.

---

# 6. Approved Stage

Once a UD has passed review, it may be marked as Approved.

Approval indicates that:

- the document structure is stable
- validation rules are well defined
- no major design objections remain
- the canonical core is clearly defined and enforceable

A UD SHOULD NOT be approved unless its core boundaries and extension model are clearly defined.

Approved documents may still undergo minor refinements before publication.

---

# 7. Published Stage

A UD is considered Published when:

- the specification is finalized
- the version is assigned
- the document is placed in the official OpenUDA UD directory

Example:


ud/
UD-INV-1.0.md


Published UDs are considered stable and suitable for production implementations.

---

# 8. Versioning

Each UD follows semantic versioning.

### Major Versions

Major versions introduce structural changes.

Examples:

- adding or removing sections
- redefining validation rules
- changing canonical semantics

Major version changes may require migration guidance.

### Minor Versions

Minor versions may:

- add optional fields
- clarify definitions
- improve documentation

Minor versions MUST NOT change the meaning of existing fields.

---

# 9. Deprecation

A UD may be marked as Deprecated when:

- a superior replacement exists
- the document is no longer widely used
- the design is found to have fundamental limitations

Deprecated UDs remain documented for backward compatibility but SHOULD NOT be used for new implementations.

---

# 10. Retirement

A UD may eventually be retired.

Retirement occurs when:

- the document is no longer maintained
- usage has significantly declined
- a newer document fully replaces it

Retired UDs remain archived for historical reference.

---

# 11. Relationship to Extension Profiles

UDs define the **core canonical structure and semantics**.

Extension Profiles define:

- country-specific requirements
- industry-specific fields
- regulatory additions
- stricter validation rules

Extension Profiles MUST:

- preserve the full core structure of the base UD
- NOT remove or redefine core fields
- NOT weaken core validation rules

Extension Profiles MAY:

- require optional core fields
- introduce additional extension fields
- define stricter constraints

A document conforming to a profile MUST also conform to the base UD.

---

# 12. Conformance

An implementation may claim conformance with a UD if it:

- supports all required core fields
- preserves the canonical structure
- respects the validation rules defined by the specification
- preserves canonical semantics

Extensions or custom fields do not invalidate conformance unless they violate core rules.

---

# 13. Governance Model

The OpenUDA project maintains the official UD specifications through its repository.

Changes SHOULD occur through:

- pull requests
- community review
- maintainers' approval

Governance decisions SHOULD prioritize:

- interoperability
- long-term stability
- minimal complexity
- preservation of canonical structure

---

# 14. Repository Structure

The OpenUDA repository organizes specifications as follows:

```text
docs/
  OpenUDA-Canonical-Design-Principles.md
  OpenUDA-Extension-Profile-Specification.md
  OpenUDA-UD-Lifecycle-Governance.md

ud/
  UD-INV-1.0.md
  UD-CM-1.0.md
  UD-PO-1.0.md

extensions/
  industry-it.md
  country-ca.md
  country-us.md
```
---

# 15. Change Log

| Version | Date | Notes |
|--------|------|------|
| 1.0 | 2026-03-07 | Initial draft |
| 1.1 | 2026-03-20 | Updated to UD terminology and added core enforcement rules 
