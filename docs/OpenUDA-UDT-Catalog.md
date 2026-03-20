# OpenUDA UDT Catalog

**Status:** Draft  
**Purpose:** Informational (Non-normative)  

---

# 1. Purpose

This document defines the current and planned set of OpenUDA Universal Document Types (UDTs).

The catalog provides a high-level view of:

- the OpenUDA document ecosystem
- relationships between document types
- current development status
- intended scope boundaries

This document is **informational** and does not define schema or validation rules.

---

# 2. Design Intent

OpenUDA aims to model the **core commercial transaction lifecycle** with a minimal, canonical set of document types.

The goal is to:

- avoid unnecessary document proliferation
- maintain clear semantic boundaries between UDTs
- support interoperability across industries
- prioritize real-world ERP integration patterns

---

# 3. Core Commercial Lifecycle

The OpenUDA UDT set is designed around two primary flows.

## 3.1 Order-to-Cash Flow


Purchase Order → Shipment → Invoice → Payment


## 3.2 Financial Adjustment Flow


Invoice → Credit Memo


These flows represent the most common business document exchanges across:

- IT suppliers
- distributors
- VARs
- resellers

---

# 4. UDT Catalog

## 4.1 Core UDTs

| UDT Code | Name | Description | Status |
|---------|------|------------|--------|
| UDT-INV | Invoice | Commercial invoice for goods and services | Draft |
| UDT-CM  | Credit Memo | Reversal or adjustment of a prior invoice | Planned |
| UDT-PO  | Purchase Order | Buyer-issued order for goods or services | Planned |
| UDT-SHP | Shipment / ASN | Notification of shipped goods | Planned |
| UDT-PAY | Payment / Remittance | Payment and remittance details | Planned |

---

# 5. UDT Responsibilities

Each UDT represents a **distinct business event**.

## 5.1 UDT-INV (Invoice)

Represents:

- billing for goods or services
- financial obligation creation

Does NOT represent:

- returns or reversals
- shipment details (beyond reference)

---

## 5.2 UDT-CM (Credit Memo)

Represents:

- invoice reversals
- returns
- pricing adjustments

Common use cases include:

- RMA (Return Merchandise Authorization)
- pricing corrections
- rebate adjustments

---

## 5.3 UDT-PO (Purchase Order)

Represents:

- buyer intent to purchase
- commitment to pricing and quantities

Drives downstream documents:

- shipment
- invoice

---

## 5.4 UDT-SHP (Shipment / ASN)

Represents:

- physical movement of goods
- shipment confirmation

Common data includes:

- tracking numbers
- serial numbers
- shipped quantities

---

## 5.5 UDT-PAY (Payment / Remittance)

Represents:

- settlement of invoices
- payment allocation

May include:

- remittance details
- invoice references
- partial payment allocation

---

# 6. Design Boundaries

To preserve low entropy, each UDT MUST maintain clear semantic boundaries.

Examples:

- Credit Memo functionality MUST NOT be embedded into Invoice
- Shipment details MUST NOT be embedded into Invoice beyond references
- Payment allocation MUST NOT be embedded into Invoice totals

If business semantics differ materially, a separate UDT SHOULD be used.

---

# 7. Relationship to Extensions

UDTs define the **core structure** of business documents.

Extensions and Extension Profiles allow:

- country-specific requirements
- industry-specific fields
- regulatory requirements

Extensions MUST NOT:

- redefine UDT semantics
- collapse multiple UDT responsibilities into one

---

# 8. Future UDT Considerations

Additional UDTs MAY be introduced when justified by:

- distinct business processes
- materially different validation rules
- clearly separate lifecycle stages

Potential future candidates include:

- Return Authorization (RMA)
- Inventory Adjustment
- Contract / Agreement
- Quote / Estimate

New UDTs MUST follow the OpenUDA UDT Lifecycle Governance process.

---

# 9. Status Definitions

| Status | Meaning |
|------|--------|
| Planned | Identified but not yet drafted |
| Draft | Initial specification in progress |
| Review | Under evaluation and refinement |
| Approved | Structurally stable |
| Published | Ready for production use |
| Deprecated | No longer recommended |
| Retired | Archived |

---

# 10. Change Log

| Version | Date | Notes |
|--------|------|------|
| 1.0 | 2026-03-07 | Initial draft |
