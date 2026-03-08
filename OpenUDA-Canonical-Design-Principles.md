# OpenUDA Canonical Design Principles

**Status:** Draft  
**Applies to:** All OpenUDA Universal Document Types (UDTs)  
**Canonical Model:** Encoding-agnostic  

---

## 1. Purpose

OpenUDA defines canonical document structures for business document exchange.

The purpose of these design principles is to ensure that all OpenUDA Universal Document Types (UDTs) are:

- predictable
- interoperable
- low-entropy
- extensible
- implementation-friendly

These principles guide the design of all current and future UDTs.

---

## 2. Normative Language

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**,  
**SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document  
are to be interpreted as described in RFC 2119 and RFC 8174 when, and only when,  
they appear in all capitals.

These terms indicate requirement levels for OpenUDA specifications.

### Normative Statements

Normative statements define requirements that implementations are expected to follow in order to claim conformance with an OpenUDA specification.

Examples:

- A UDT **MUST** define its validation rules.
- Extensions **MUST NOT** redefine core fields.

### Informative Statements

Informative statements provide guidance, explanation, or examples that help implementers understand the specification but are not mandatory for conformance.

Examples include:

- explanatory notes
- implementation guidance
- illustrative JSON examples

---
  
## 3. Core Design Goal

OpenUDA is designed to reduce integration entropy.

Traditional business document standards often create complexity through:

- inconsistent document structures
- excessive optionality
- deep nesting
- multiple overlapping document variants
- weak separation between standard and custom data

OpenUDA aims to provide a simpler canonical model that preserves business meaning while remaining practical for implementation.

---

## 4. Common Canonical Backbone

All OpenUDA UDTs SHOULD follow a common structural backbone wherever applicable.

The canonical backbone is:

- Metadata
- Header
- Parties
- Lines
- Totals

Optional companion sections MAY include:

- References
- Tax Summary
- Attachments
- Extensions
- Custom

This common backbone is intended to make OpenUDA documents feel structurally familiar across document types.

**Design objective:** once an implementer understands one UDT, they should be able to understand other UDTs with minimal relearning.

---

## 5. Stable Core, Flexible Edge

OpenUDA separates document content into three layers:

### 5.1 Core
Core fields are the fields defined directly in a UDT specification.

Core fields:

- represent common and stable business meaning
- are expected to be broadly reusable
- MUST remain semantically stable within a major version
- MUST NOT be overridden by extensions or custom data

### 5.2 Extensions
Extensions are standardized optional fields defined outside the core UDT to support:

- country-specific requirements
- industry-specific requirements
- community-approved additions

Extensions allow OpenUDA to expand without destabilizing the core.

### 5.3 Custom
Custom fields are organization-specific fields defined by an individual company or implementation.

Custom fields allow adoption without waiting for OpenUDA governance to standardize every requirement.

---

## 6. Core Fields Must Be Minimal and Universal

A field SHOULD be added to the core only when it is:

- broadly applicable
- semantically stable
- implementation-relevant
- unlikely to vary significantly across industries

Core fields SHOULD NOT be used to model narrow industry requirements unless those requirements become broadly universal.

Examples of core-style fields include:

- document identifiers
- dates
- parties
- line items
- monetary totals
- tax amounts

---

## 7. Canonical Semantics Over Storage Model

OpenUDA models business meaning, not internal database design.

Implementations MAY store the same document in different internal table structures, document stores, or APIs, but the OpenUDA semantic model MUST remain intact.

Examples:

- tax may be stored internally as separate records, but canonically it may still belong to a line
- parties may be normalized into separate tables internally, but canonically they remain part of the document structure

---

## 8. Structural Consistency Across UDTs

New UDTs SHOULD reuse the same top-level structural grammar whenever possible.

For example:

- `metadata`
- `header`
- `parties`
- `lines`
- `totals`

Specialized business requirements SHOULD be added in contained specialized sections rather than by inventing a completely different top-level structure.

Preferred pattern:

```json
{
  "metadata": {},
  "header": {},
  "parties": {},
  "lines": [],
  "totals": {},
  "specializedSection": {}
}
```
Avoid patterns where every new UDT introduces an entirely different structural shape.

---

## 9. Derived Values

Derived values SHOULD be defined clearly.

Where useful for interoperability, derived values MAY be transmitted explicitly, but they MUST remain subject to validation.

Examples:

- `lineAmount = quantity × unitPrice`
- `grandTotal = subtotal + discountTotal + taxTotal + shippingTotal + roundingAdjustment`

Derived values MUST NOT conflict with their declared formulas beyond the tolerance defined by the relevant UDT.

---

## 10. Extensions

Extensions allow OpenUDA to support broader use cases without bloating the core.

### 10.1 Extension Rules

Extensions:

- MUST be namespaced
- MUST NOT redefine a core field
- MUST NOT change the meaning of a core field
- MUST NOT invalidate core validation rules
- SHOULD remain optional unless a specific extension profile states otherwise

### 10.2 Extension Namespaces

Recommended namespace patterns include:

- `openuda:country.<ISO2>`
- `openuda:industry.<name>`
- `openuda:community.<name>`

Examples:

- `openuda:country.ca`
- `openuda:country.us`
- `openuda:industry.it`
- `openuda:industry.healthcare`

### 10.3 Extension Placement

Extensions MAY appear at multiple levels of a document, including:

- header
- lines
- parties
- attachments

Examples:

- `header.extensions`
- `lines[].extensions`
- `parties.seller.extensions`

### 10.4 Extension Shape

Extensions SHOULD be represented as namespaced objects rather than flat key-value arrays.

Preferred pattern:

```json
{
  "extensions": {
    "openuda:industry.it": {
      "dealRegistrationId": "DR-33445",
      "contractNumber": "ITC-9983"
    }
  }
}
```
This pattern improves readability, validation, and future machine processing.

### 10.5 Extension Optionality Principle

Extensions SHOULD remain optional wherever possible.

Extensions MUST NOT introduce mandatory fields that prevent a document from being processed using only the core UDT structure unless the requirement is defined by an explicitly published extension profile.

An extension profile MAY define additional required fields for a specific ecosystem, such as:

- a country regulatory profile
- an industry interoperability profile
- a bilateral trading agreement

However, such requirements MUST be declared within the extension profile itself and MUST NOT alter the conformance requirements of the base UDT specification.

Implementations that do not support a given extension profile SHOULD still be able to process the core UDT document.

This rule preserves interoperability across OpenUDA implementations while allowing ecosystems to evolve specialized requirements.

---

### 11. Custom Data

Custom data supports organization-specific needs that are not yet part of OpenUDA core or extension standards.

### 11.1 Custom Rules

Custom data:

MUST be namespaced using the organization identifier

MUST NOT redefine or duplicate core fields

SHOULD be optional for receivers

MAY later be promoted into an extension or core field through governance

### 11.2 Custom Namespaces

Recommended pattern:

org:<orgId>

Examples:

org:cisco

org:acme

org:globex

### 11.3 Custom Placement

Custom fields MAY appear at multiple levels, including:

header

lines

parties

attachments

Examples:

header.custom

lines[].custom

### 11.4 Custom Shape

Preferred pattern:

{
  "custom": {
    "org:cisco": {
      "partnerTier": "Gold",
      "specialPricingCode": "SP-4451"
    }
  }
}


---

### 12. Extension and Custom Data Must Be Ignorable

A receiver that does not recognize an extension or custom field SHOULD still be able to process the document using only the core fields.

This principle is essential for:

forward compatibility

interoperability

low-friction partner onboarding

Unrecognized extension or custom content SHOULD NOT break core processing unless a specific bilateral agreement requires it.

---

### 13. Promotion Path

OpenUDA supports a natural field maturity path:

Custom → Extension → Core

This allows the standard to evolve gradually.

A field may begin as:

a company-specific custom field

then become a recognized industry or country extension

then eventually become part of the core if it proves broadly universal

Promotion SHOULD occur through documented review and versioning processes.

---

### 14. Low-Entropy Taxonomy

OpenUDA SHOULD avoid unnecessary document proliferation.

Separate UDTs SHOULD be introduced only when the business semantics or required structures are materially different.

Examples of reasons to introduce a separate UDT may include:

materially different validation rules

materially different required business objects

materially different workflow semantics

Differences in internal ERP storage alone SHOULD NOT force creation of a separate UDT.

---

### 15. Versioning Principles

OpenUDA follows major/minor versioning.

### 15.1 Minor Versions

Minor versions:

MAY add optional fields

MAY clarify definitions

SHOULD preserve backward compatibility

MUST NOT change the meaning of existing fields

### 15.2 Major Versions

Major versions:

MAY introduce structural changes

MAY remove or replace deprecated constructs

MUST include migration guidance where practical

---

### 16. Validation Principles

Each UDT SHOULD define explicit validation rules.

Validation rules SHOULD cover:

required field presence

type constraints

derived value formulas

currency consistency

cross-section consistency where needed

Validation SHOULD be deterministic and implementation-friendly.

---

### 17. Human Readability and Machine Usability

OpenUDA specifications SHOULD be understandable by:

integration architects

developers

business analysts

implementation leads

Specifications SHOULD balance:

semantic clarity

machine processability

implementation practicality

OpenUDA should avoid unnecessary abstraction when a simpler expression preserves the same meaning.

---

### 18. JSON Shape Guidance

Although OpenUDA is encoding-agnostic, JSON examples SHOULD follow a consistent style.

Recommended practices:

lowerCamelCase field names

arrays for repeating business objects

objects for structured concepts

namespaced objects for extensions and custom data

explicit money objects for monetary values

Example:

{
  "header": {
    "issueDate": "2026-02-23",
    "extensions": {
      "openuda:industry.it": {
        "contractNumber": "ITC-9983"
      }
    },
    "custom": {
      "org:acme": {
        "salesRegion": "NorthEast"
      }
    }
  }
}

---

### 19. Governance and Future Evolution

These principles are intended to prevent schema drift and preserve coherence across the OpenUDA ecosystem.

They provide a foundation for:

new UDT design

extension design

custom field handling

future governance processes

All future OpenUDA specifications SHOULD align with these principles unless a documented exception is explicitly justified.


---

### 20. Change Log
| Version | Date | Notes |
|--------|------|------|
| 1.0 | 2026-03-07 | Initial draft |
