# OpenUDA Country Profile — Canada

**Status:** Draft  
**Profile Identifier:** `openuda:profile.country.ca`  
**Version:** 1.0  
**Maintainer:** OpenUDA Working Group  
**Applies to:** UD-INV, UD-CM, UD-PO, UD-SHP  
**Canonical Model:** Encoding-agnostic  

---

# 1. Purpose

This document defines the OpenUDA country profile for **Canada**.

This profile standardizes how OpenUDA Universal Documents (UDs) are used in Canadian business transactions, with a focus on:

- indirect taxation (GST, HST, PST, QST)
- jurisdiction handling
- tax validation expectations
- Canadian invoice compliance expectations

This profile builds on OpenUDA core document structures and MUST NOT alter their semantics.

---

# 2. Scope

This profile applies to transactions occurring in Canada, including:

- domestic Canadian transactions
- interprovincial transactions
- imports where Canadian tax is applied at time of invoicing

This profile covers:

- invoices (UD-INV)
- credit memos (UD-CM)
- purchase orders (UD-PO)
- shipments (UD-SHP)

This profile does not define:

- income tax
- customs duties (beyond invoice representation)
- transport protocols
- ERP-specific logic

---

# 3. Conformance

A document conforms to this profile if it:

- conforms to the base UD specification
- declares this profile in metadata
- satisfies all rules defined in this profile

A document conforming to this profile MUST still conform to the base UD.

---

# 4. Profile Declaration

Documents SHOULD declare this profile in metadata.

Example:

```json
{
  "metadata": {
    "udCode": "UD-INV",
    "version": "1.0",
    "profiles": [
      "openuda:profile.country.ca"
    ]
  }
}
```

---

# 5. Canadian Tax Model Overview

Canada uses a multi-layer indirect tax system:

- GST (federal)
- HST (harmonized federal + provincial)
- PST (provincial, separate systems)
- QST (Quebec provincial tax)

Taxes may be:

- applied per line
- stacked (multiple taxes per line)
- jurisdiction-dependent based on ship-to location

---

# 6. Tax Structure Requirements
## 6.1 Line-Level Tax Detail

Tax MUST be represented at the line level using:

```lines[].taxDetail[]```

Each applicable tax MUST be represented as a separate TaxDetail entry.

## 6.2 Multiple Taxes Per Line

Lines MAY contain multiple tax entries.

Example:

- GST + PST
- GST + QST

Each tax MUST be represented independently.

## 6.3 Required Tax Fields

For each taxDetail entry:

- Field	Requirement
- taxType	REQUIRED
- jurisdiction	REQUIRED
- taxAmount	REQUIRED
- taxableAmount	REQUIRED when taxAmount ≠ 0
- rate	REQUIRED when available

---

# 7. Supported Tax Types

The following taxType values SHOULD be used:

- Tax Type	Description
- GST	Federal Goods and Services Tax
- HST	Harmonized Sales Tax
- PST	Provincial Sales Tax
- QST	Quebec Sales Tax

Other values MAY be used if required by the sender’s tax system.

---

# 8. Jurisdiction Handling

## 8.1 Jurisdiction Identifier

Jurisdiction MUST be represented as:

{
  "scheme": "erp:<system>",
  "value": "<jurisdiction_code>"
}

Example:

{
  "scheme": "erp:sap",
  "value": "CA-ON-HST"
}

## 8.2 Authority

OpenUDA does NOT define jurisdiction code systems.

Jurisdiction values MUST originate from:

- the sender’s ERP
- a tax calculation engine
- a regulatory system

---

# 9. Tax Calculation Rules

## 9.1 Tax Source of Truth

Tax MUST be derived from line-level tax detail.

totals.taxTotal = sum(all lines[].taxDetail[].taxAmount)

## 9.2 Currency Consistency

All tax amounts MUST use the document currency.

## 9.3 Zero-Tax Lines

If a line is non-taxable:

- taxDetail MAY be omitted
- OR taxAmount MAY be zero

## 9.4 Rounding

Tax calculations MUST be accurate within the tolerance defined in the base UD (typically 0.01).

---

# 10. Invoice Requirements (UD-INV)

For Canadian invoices:

## 10.1 Tax Presence

If tax applies:

- at least one line MUST include taxDetail
- totals.taxTotal MUST be greater than zero

## 10.2 Tax Breakdown Consistency

The sum of all line-level tax MUST equal totals.taxTotal within tolerance.

---

# 11. Credit Memo Requirements (UD-CM)

For Canadian credit memos:

## 11.1 Tax Reversal

Tax MUST be reversed proportionally to the credited lines.

- taxAmount SHOULD be negative for credits
- taxableAmount SHOULD reflect the reversed base

## 11.2 Original Invoice Reference

Credit memos SHOULD reference the original invoice when tax is being reversed.

---

# 12. Address Requirements

## 12.1 Ship-To Importance

Tax determination SHOULD be based on:

parties.shipTo.address

This is critical for:

- HST vs GST/PST determination
- interprovincial tax rules

## 12.2 Required Address Fields

Ship-To address SHOULD include:

- province (region)
- country (CA)
- postal code

---

# 13. Interoperability Notes

This profile is designed to:

- support Canadian ERP systems
- support tax engines (Avalara, Vertex, native ERP tax)
- support distributor and reseller workflows

This profile does not enforce a single tax engine or jurisdiction system.

---

# 14. Relationship to Industry Profiles

This profile MAY be used alongside:

openuda:profile.industry.it-distribution

When combined:

- industry profile defines commercial fields
- country profile defines tax rules

Both profiles MUST be satisfied.

---

# 15. Relationship to Custom Fields

Organizations MAY include custom tax-related fields using:

org:<orgId>

However:

custom fields MUST NOT replace core tax structure
custom fields MUST NOT conflict with this profile

---

# 16. Change Log
Version	Date	Notes
1.0	2026-03-20	Initial draft
