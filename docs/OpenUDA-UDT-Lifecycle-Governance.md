# OpenUDA UDT Lifecycle Governance

**Status:** Draft  
**Applies to:** All OpenUDA Universal Document Types (UDTs)  

---

# 1. Purpose

This document defines the governance process for the lifecycle of OpenUDA Universal Document Types (UDTs).

The goal of this process is to ensure that new document types and revisions are introduced in a controlled manner that preserves:

- interoperability
- stability
- low entropy
- long-term maintainability

This governance model allows OpenUDA to evolve while preventing uncontrolled schema proliferation.

---

# 2. UDT Lifecycle Overview

Each Universal Document Type progresses through the following lifecycle stages:


Proposal → Draft → Review → Approved → Published → Deprecated → Retired


These stages ensure that new document types are properly evaluated before becoming part of the OpenUDA specification family.

---

# 3. Proposal Stage

A new UDT begins with a proposal.

A proposal SHOULD include:

- the proposed document name
- the business purpose of the document
- industries or ecosystems that require the document
- justification for why the document cannot be represented by an existing UDT
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
- relationships to existing UDTs are evaluated

Draft documents SHOULD follow the OpenUDA Canonical Design Principles.

Draft documents are considered experimental and may change significantly.

---

# 5. Review Stage

During the Review stage, the proposed UDT is evaluated by the OpenUDA community.

Review activities may include:

- interoperability evaluation
- schema simplification
- alignment with canonical design principles
- validation rule testing
- industry feedback

Major structural issues SHOULD be resolved during this phase.

---

# 6. Approved Stage

Once a UDT has passed review, it may be marked as Approved.

Approval indicates that:

- the document structure is stable
- validation rules are well defined
- no major design objections remain

Approved documents may still undergo minor refinements before publication.

---

# 7. Published Stage

A UDT is considered Published when:

- the specification is finalized
- the version is assigned
- the document is placed in the official OpenUDA UDT directory

Example:


udt/
UDT-INV-1.0.md


Published UDTs are considered stable and suitable for production implementations.

---

# 8. Versioning

Each UDT follows semantic versioning.

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

A UDT may be marked as Deprecated when:

- a superior replacement exists
- the document is no longer widely used
- the design is found to have fundamental limitations

Deprecated UDTs remain documented for backward compatibility but SHOULD NOT be used for new implementations.

---

# 10. Retirement

A UDT may eventually be retired.

Retirement occurs when:

- the document is no longer maintained
- usage has significantly declined
- a newer document type fully replaces it

Retired UDTs remain archived for historical reference.

---

# 11. Relationship to Extension Profiles

UDTs define the **core document structure**.

Extension Profiles define:

- country-specific requirements
- industry-specific fields
- regulatory additions

Extension Profiles MUST NOT redefine the meaning of UDT core fields.

Extension Profiles may introduce additional fields that apply to specific ecosystems.

---

# 12. Conformance

An implementation may claim conformance with a UDT if it:

- supports all required core fields
- respects the validation rules defined by the specification
- preserves canonical semantics

Extensions or custom fields do not invalidate conformance unless they violate core rules.

---

# 13. Governance Model

The OpenUDA project maintains the official UDT specifications through its repository.

Changes SHOULD occur through:

- pull requests
- community review
- maintainers' approval

Governance decisions SHOULD prioritize:

- interoperability
- long-term stability
- minimal complexity

---

# 14. Repository Structure

The OpenUDA repository organizes specifications as follows:


docs/
OpenUDA-Canonical-Design-Principles.md
OpenUDA-Extension-Profile-Specification.md
OpenUDA-UDT-Lifecycle-Governance.md

udt/
UDT-INV-1.0.md
UDT-CM-1.0.md
UDT-PO-1.0.md

extensions/
industry-it.md
country-ca.md
country-us.md


This structure separates:

- governance documentation
- document type specifications
- extension profiles

---

# 15. Change Log

| Version | Date | Notes |
|--------|------|------|
| 1.0 | 2026-03-07 | Initial draft |
