# OpenUDA Industry Profile — IT Distribution

**Status:** Draft  
**Profile Identifier:** `openuda:profile.industry.it-distribution`  
**Version:** 1.0  
**Maintainer:** OpenUDA Working Group  
**Applies to:** UD-INV, UD-CM, UD-PO, UD-SHP  
**Canonical Model:** Encoding-agnostic  

---

# 1. Purpose

This document defines the OpenUDA industry profile for **IT Distribution**.

This profile standardizes how OpenUDA Universal Documents are used across the IT distribution ecosystem, including:

- IT distributors
- value-added resellers (VARs)
- resellers
- managed service providers (MSPs)
- vendors participating in distributor-led transactions

The purpose of this profile is to reduce onboarding variation by defining:

- required core fields
- required extension fields
- conditional rules
- profile-specific validation expectations

This profile builds on the OpenUDA base Universal Documents and MUST preserve their core structure and semantics.

---

# 2. Scope

This profile applies to commercial transactions involving:

- hardware
- software licenses
- subscriptions
- support entitlements
- distribution-channel pricing programs

This profile is intended for common B2B transactional flows such as:

- purchase orders
- shipments
- invoices
- credit memos

This profile does not define:

- transport protocols
- authentication
- ERP-specific API behavior
- tax jurisdiction code systems
- country-specific regulatory requirements beyond profile-level constraints

---

# 3. Conformance

A document conforms to this profile if it:

- conforms to the applicable base UD specification
- declares this profile in metadata
- satisfies all profile-required fields
- satisfies all profile validation rules defined in this document

A document conforming to this profile MUST still conform to the underlying base UD.

---

# 4. Profile Declaration

Documents conforming to this profile SHOULD declare the profile in metadata.

Preferred JSON example:

```json
{
  "metadata": {
    "udCode": "UD-INV",
    "version": "1.0",
    "profiles": [
      "openuda:profile.industry.it-distribution"
    ]
  }
}
```

If multiple profiles are declared, they MUST be additive and MUST NOT conflict.

---

# 5. Profile Design Principles

This profile follows these principles:

- preserve the base OpenUDA core
- standardize high-value interoperability fields
- require fields that commonly drive matching, pricing, logistics, tax, and returns workflows
- avoid introducing role-specific structural variants
- allow company-specific custom fields only at the edges

This profile MAY make optional base UD fields required.

This profile MUST NOT:

- remove a core section
- rename a core field
- redefine a core field
- weaken a base UD validation rule

---

# 6. Common IT Distribution Concepts

The IT distribution ecosystem commonly relies on the following identifiers and references:

- manufacturer part number
- seller SKU / distributor SKU
- buyer part number
- purchase order reference
- order line reference
- serial numbers
- ship date
- tax detail
- deal registration identifiers
- contract pricing references
- rebate / pricing program references
- RMA references for returns and credits

These concepts appear repeatedly across invoice, order, shipment, and credit workflows.

---

# 7. Required Core Fields by Document

## 7.1 UD-INV

For documents conforming to **UD-INV** under this profile, the following fields are REQUIRED in addition to the base UD-INV requirements.

### Header

| Field | Requirement |
|---|---|
| header.issueDate | REQUIRED |

### Lines

| Field | Requirement |
|---|---|
| lines[].description | REQUIRED |
| lines[].quantity | REQUIRED |
| lines[].unitPrice | REQUIRED |
| lines[].lineAmount | REQUIRED |
| lines[].manufacturerPartNumber | REQUIRED |
| lines[].orderReference.orderId | REQUIRED |
| lines[].unitOfMeasure | REQUIRED |

### Totals

| Field | Requirement |
|---|---|
| totals.subtotal | REQUIRED |
| totals.taxTotal | REQUIRED |
| totals.grandTotal | REQUIRED |
| totals.amountDue | REQUIRED |

### References

At least one purchase order reference SHOULD be present when the transaction originated from a customer order.

## 7.2 UD-CM

For documents conforming to **UD-CM** under this profile:

### Header

| Field | Requirement |
|---|---|
| header.issueDate | REQUIRED |
| header.reasonCode | REQUIRED |

### References

| Field | Requirement |
|---|---|
| references[] | REQUIRED |
| references[].referenceType = INVOICE | REQUIRED at least once |

### Lines

| Field | Requirement |
|---|---|
| lines[].description | REQUIRED |
| lines[].quantity | REQUIRED |
| lines[].unitPrice | REQUIRED |
| lines[].lineAmount | REQUIRED |
| lines[].manufacturerPartNumber | REQUIRED when product credit applies |
| lines[].orderReference.orderId | REQUIRED when order-based |

### Conditional

- `lines[].rmaNumber` is REQUIRED when reasonCode indicates return
- serial numbers are REQUIRED when applicable

## 7.3 UD-PO

For documents conforming to UD-PO under this profile, the following fields are REQUIRED in addition to the base UD-PO requirements.

### Header
| Field | Requirement |
|---|---|
| header.issueDate |	REQUIRED |

### Lines
| Field | Requirement |
|---|---|
| lines[].description	| REQUIRED |
| lines[].quantity	| REQUIRED |
| lines[].unitPrice	| REQUIRED when known |
| lines[].manufacturerPartNumber	| REQUIRED |
| lines[].unitOfMeasure	| REQUIRED |
| lines[].orderReference.orderLineId	| REQUIRED when line-level order numbering is used by sender |

## 7.4 UD-SHP

For documents conforming to UD-SHP under this profile, the following fields are REQUIRED in addition to the base UD-SHP requirements.

### Header
| Field | Requirement |
|---|---|
| header.issueDate |	REQUIRED |
### Lines
| Field | Requirement |
|---|---|
| lines[].description	| REQUIRED |
| lines[].quantity	| REQUIRED |
| lines[].manufacturerPartNumber	| REQUIRED |
| lines[].shipDate	| REQUIRED |
| lines[].orderReference.orderId	| REQUIRED when shipment fulfills an order |

### Conditional
- serial numbers are REQUIRED for serialized hardware when available to the sender
- tracking references SHOULD be present when shipment is carrier-based

---

# 8. Required Extension Fields

This profile defines the following standardized extension namespace:

`openuda:industry.it-distribution`

Extensions under this profile MAY appear at:

- header.extensions
- lines[].extensions

## 8.1 Header Extensions

The following extension fields are defined at header level.

| Field | Type | Requirement |	Description |
|---|---|---|---|
| dealRegistrationId	| String	| OPTIONAL	| Channel deal registration identifier |
| contractNumber	| String	| OPTIONAL	| Vendor or channel contract number |
| pricingProgramId	| String	| OPTIONAL	| Identifier for pricing program, rebate program, or special pricing arrangement |

## 8.2 Line Extensions

The following extension fields are defined at line level.

| Field | Type | Requirement |	Description |
|---|---|---|---|
| vendorProgramLineId	| String	| OPTIONAL	| Program-specific line identifier |
| distributorSku	| String	| OPTIONAL	| Distributor-specific SKU when distinct from base SKU |
| rebateReference	| String	| OPTIONAL	| Rebate, MDF, or pricing claim reference |
| warrantyReference	| String	| OPTIONAL	| Warranty-related reference when applicable |

---

# 9. Conditional Rules

## 9.1 Serialized Hardware

If a line represents serialized hardware and serial numbers are available, `lines[].serialNumbers[]` MUST be populated.

## 9.2 Order-Based Transactions

If the document originates from an order flow:

- `lines[].orderReference.orderId` MUST be populated
- `lines[].orderReference.orderLineId` SHOULD be populated when line matching is required

## 9.3 Credit Memo Returns

If a credit memo represents a physical return:

- `lines[].rmaNumber` SHOULD be present
- `lines[].serialNumbers[]` SHOULD be present when applicable
- a reference to the originating invoice SHOULD be included

---

# 10. Validation Rules

In addition to the base UD validation rules, this profile defines the following constraints.

## 10.1 Manufacturer Part Number

`manufacturerPartNumber` MUST be present where applicable.

## 10.2 Unit of Measure

`unitOfMeasure` MUST be populated for all applicable lines.

## 10.3 Profile/Core Consistency

A document that fails the base UD conformance rules cannot claim profile conformance.

## 10.4 Tax Handling

Tax structure and validation are defined in the base UD and applicable country profiles.

This profile does not introduce additional tax rules.

---

# 11. Relationship to Custom Data

Organizations MAY still use custom fields under:

```json
org:<orgId>
```

However:

- custom fields MUST NOT replace required profile fields
- custom fields MUST NOT duplicate profile-defined extension fields
- custom fields SHOULD be limited to organization-specific edge requirements

Fields that become common across multiple organizations SHOULD be proposed for future profile standardization.

---

# 12. Interoperability Intent

This profile is intended to reduce partner-by-partner variation within the IT distribution ecosystem.

Its purpose is to move implementations from:

- bespoke partner onboarding
- ad hoc mapping agreements
- undocumented field expectations

toward:

- shared field requirements
- repeatable validation
- reusable mapping templates
- predictable partner conformance

---

# 13. Recommended Repository Placement

This profile SHOULD be stored at:

```json
extensions/industry-it-distribution.md
```

---

# 14. Change Log
| Version	| Date	| Notes |
|---|---|---|
| 1.0	| 2026-03-20	|Initial draft |
