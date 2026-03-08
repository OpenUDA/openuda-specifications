# OpenUDA Extension Profile Specification

**Status:** Draft  
**Applies to:** OpenUDA Extension Profiles  
**Canonical Model:** Encoding-agnostic  

---

# 1. Purpose

This document defines how OpenUDA **Extension Profiles** are structured, published, and referenced.

Extension Profiles allow industries, countries, and communities to introduce additional fields or structures without modifying the core Universal Document Types (UDTs).

Profiles ensure that extensions remain **predictable, interoperable, and governed**, preventing fragmentation of the OpenUDA ecosystem.

---

# 2. What Is an Extension Profile

An Extension Profile is a specification that defines a set of extensions applicable to one or more OpenUDA document types.

Profiles may represent:

- country requirements
- industry requirements
- regulatory requirements
- community standards

Examples:

- OpenUDA Country Profile — Canada
- OpenUDA Industry Profile — IT Distribution
- OpenUDA Industry Profile — Healthcare

Extension Profiles do not change the OpenUDA core specification.

They only define **additional optional fields or structures**.

---

# 3. Extension Profile Structure

Each Extension Profile MUST include the following sections.

### Profile Metadata

| Field | Description |
|------|-------------|
| Profile Name | Human-readable name |
| Profile Identifier | Unique identifier |
| Version | Profile version |
| Maintainer | Organization or working group maintaining the profile |
| Applicable UDTs | UDTs affected by the profile |

Example:

| Field | Example |
|------|---------|
| Profile Name | OpenUDA Industry Profile — IT Distribution |
| Profile Identifier | `openuda:profile.industry.it` |
| Version | 1.0 |
| Maintainer | OpenUDA Working Group |
| Applicable UDTs | UDT-INV |

---

# 4. Namespace Rules

All extensions defined within a profile MUST use a consistent namespace.

Recommended namespace patterns:


openuda:country.<ISO2>
openuda:industry.<name>
openuda:community.<name>


Examples:


openuda:country.ca
openuda:industry.it
openuda:industry.healthcare


Example usage in a document:

```json
{
  "header": {
    "extensions": {
      "openuda:industry.it": {
        "contractNumber": "ITC-9983"
      }
    }
  }
}
```

---

# 5. Extension Definition

Each extension field defined by a profile MUST specify:

| Field	| Description |
|-----|-----------|
| Field Name	| Extension field name |
| Data Type	| Expected type |
| Description	| Business meaning |
| Applies To	| Document section where it appears |
| Requirement Level	| OPTIONAL or REQUIRED within the profile |

Example:

| Field	| Value |
|-----|-----|
| Field Name	| contractNumber |
| Data Type	| String |
| Description	| Vendor contract identifier |
| Applies To	| header |
| Requirement Level	| OPTIONAL |

---

# 6. Profile Conformance

A document MAY declare that it conforms to a specific extension profile.

Profiles SHOULD be declared in the document metadata.

Example:

{
  "metadata": {
    "udtCode": "UDT-INV",
    "version": "1.0",
    "profile": "openuda:profile.industry.it"
  }
}

Declaring a profile signals that the document may include extensions defined by that profile.

Receivers that support the profile may enforce its additional rules.

Receivers that do not support the profile SHOULD still process the document using the core UDT structure.

---

# 7. Profile Versioning

Extension Profiles follow semantic versioning.

### Minor Versions

Minor versions MAY:
- add optional fields
- clarify definitions
Minor versions MUST NOT break compatibility with earlier profile versions.

### Major Versions

Major versions MAY:
- introduce structural changes
- remove deprecated fields
Migration guidance SHOULD be provided.

---

# 8. Profile Governance

Extension Profiles SHOULD be maintained by:
- an OpenUDA working group
- an industry consortium
- a regulatory body
- a recognized community group
Profiles MUST clearly identify the organization responsible for maintenance.

---

# 9. Profile Interoperability

Multiple profiles MAY be used together in a single document.

Example:

openuda:country.ca
openuda:industry.it

Implementations SHOULD treat profiles as additive.

Profiles MUST NOT redefine the meaning of core fields.

Profiles MUST follow the No Semantic Override rule defined in the OpenUDA Canonical Design Principles.

---

# 10. Relationship to Custom Fields

Extension Profiles represent standardized extensions.

Custom fields remain organization-specific and use the namespace pattern:

org:<orgId>

Custom fields may later be promoted into an Extension Profile.

---

# 11. Repository Structure

Extension profiles SHOULD be stored in the OpenUDA repository using the following structure.

extensions/
   country-ca.md
   country-us.md
   industry-it.md
   industry-healthcare.md

Each profile document SHOULD follow the structure defined in this specification.

---

# 12. Change Log

| Version	| Date	| Notes
|-------|----|-----|
| 1.0	| 2026-03-07	| Initial draft |
