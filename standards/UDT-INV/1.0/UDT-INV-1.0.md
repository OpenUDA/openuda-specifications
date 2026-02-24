# OpenUDA Universal Document Type (UDT)
## Invoice (UDT-INV) Version 1.0

**UDT Code:** UDT-INV
**Version:** 1.0
**Status:** Draft
**Canonical Model:** Encoding-agnostic
**Publication Date:** 2026-02-23

---

## 1. Introduction

This document defines the OpenUDA Universal Document Type for an Invoice ("UDT-INV").

UDT-INV provides a canonical, interoperable definition of invoice structure and semantic meaning for business document exchange. The specification is encoding-agnostic and may be serialized using JSON, XML, or other formats via official OpenUDA encoding profiles.

---

## 2. Scope

UDT-INV v1.0 defines:

- Core commercial invoice structure
- Party identification
- Line-level detail
- Monetary totals
- Tax summary
- Cross-document references
- Extension framework

This specification does not define:

- Transport protocols (e.g., AS2, SFTP, REST)
- Authentication mechanisms
- Network governance rules
- Partner onboarding workflows
- ERP-specific implementation logic

---

## 3. Conformance

A document conforms to UDT-INV v1.0 if it:

1. Declares UDT Code = `UDT-INV`
2. Declares Version = `1.0`
3. Includes all required elements defined in this specification
4. Passes all validation rules in Section 11
5. Uses extensions only as defined in Section 12

---

## 4. Document Structure (Normative)

A UDT-INV document SHALL contain the following sections:

1. Metadata
2. Header
3. Parties
4. Lines (one or more)
5. Totals

The following sections are OPTIONAL but normative if present:

6. References
7. Tax Summary
8. Attachments
9. Extensions

---

## 5. Data Types

The following conceptual data types are used throughout this specification:

| Type | Description | Example |
|------|-------------|---------|
| String | Human-readable text | `"Acme Corporation"` |
| Identifier | Scheme + value pair | `{ "scheme": "GST", "value": "123456789RT0001" }` |
| Date | ISO 8601 calendar date | `"2026-02-23"` |
| DateTime | ISO 8601 timestamp with timezone | `"2026-02-23T10:00:00Z"` |
| Decimal | Numeric value with fractional precision | `12.50` |
| CurrencyCode | ISO 4217 three-letter code | `"CAD"` |
| CountryCode | ISO 3166-1 alpha-2 code | `"CA"` |
| Money | `{ "amount": Decimal, "currency": CurrencyCode }` | `{ "amount": 150.00, "currency": "CAD" }` |

Encoding formats MAY represent these types differently; semantic meaning and constraints defined here remain normative.

**Sign convention:** Monetary amounts use positive values for standard charges and negative values for credits, discounts, and reversals. The invoice type (CREDIT, DEBIT) does not implicitly negate amounts — signs must be explicit on each field.

---

## 6. Metadata (Required)

Metadata provides document identification and routing information.

Metadata SHALL include:

| Field | Type | Description |
|-------|------|-------------|
| UDT Code | String | Must equal `UDT-INV` |
| Version | String | Must equal `1.0` |
| Document ID | String | Globally unique document identifier. SHALL be a UUID conforming to RFC 4122 or a URN containing a UUID (e.g., urn:uuid:<uuid>). |
| Invoice Number | String | Human-readable invoice number |
| Currency | CurrencyCode | Default currency for all monetary values in this document |
| Environment | String | Must equal `TEST` or `PRODUCTION`. Prevents test documents from entering production pipelines. |

Metadata SHOULD include:

| Field | Type | Description |
|-------|------|-------------|
| Created Timestamp | DateTime | Date and time the document was created |
| Sender Identifier | Identifier | Identifier of the party transmitting this document |
| Receiver Identifier | Identifier | Identifier of the intended recipient. Optional at creation — the exchange network MAY resolve this during transmission. |

---

## 7. Header (Required)

Header captures invoice-level business meaning.

Header SHALL include:

| Field | Type | Description |
|-------|------|-------------|
| Invoice Type | String | One of: `STANDARD`, `CREDIT`, `DEBIT`, `PROFORMA`. See Section 7.1. |
| Issue Date | Date | Date the invoice was issued |

Header SHOULD include:

| Field | Type | Description |
|-------|------|-------------|
| Due Date | Date | Date payment is due |
| Payment Terms | String | Structured or textual payment terms (e.g., `NET30`) |
| Service Period Start | Date | Start of the service or billing period |
| Service Period End | Date | End of the service or billing period |
| Status | String | One of: `ISSUED`, `PAID`, `PARTIALLY_PAID`, `VOID` |
| Notes | String | Remittance instructions or general notes |

### 7.1 Invoice Type Semantics

- **STANDARD** — A standard commercial invoice for goods or services rendered.
- **CREDIT** — A credit note reducing a prior amount owed. Line amounts and totals SHOULD use negative values to reflect the reversal. A Reference to the original invoice SHOULD be present (see Section 12).
- **DEBIT** — A debit note increasing a prior amount owed. A Reference to the original invoice SHOULD be present (see Section 12).
- **PROFORMA** — A preliminary invoice issued before delivery. Not a demand for payment.

---

## 8. Parties (Required)

Parties SHALL include:

| Role | Requirement | Description |
|------|-------------|-------------|
| Seller | SHALL | The invoicing party |
| Buyer | SHALL | The billed party |
| Bill-To | SHOULD if different from Buyer | Party responsible for payment |
| Ship-To | SHOULD if applicable | Delivery destination |
| Remit-To | SHOULD if applicable | Payment recipient address or banking details |

Each Party SHALL include:

| Field | Type | Description |
|-------|------|-------------|
| Legal Name | String | Registered legal name of the party |
| Primary Identifier | Identifier | OpenUDA Party ID or recognized external identifier |

Each Party SHOULD include:

| Field | Type | Description |
|-------|------|-------------|
| Tax Identifiers | Identifier[] | VAT, GST/HST, or other applicable tax registration numbers |
| Address | Address | Street address including postal code and country |
| Contact | Contact | Name, email, and/or phone number |

---

## 9. Lines (Required)

An Invoice SHALL contain at least one Line.

Each Line SHALL include:

| Field | Type | Description |
|-------|------|-------------|
| Line ID | String | Unique identifier within this invoice |
| Description | String | Human-readable description of the goods or service |
| Quantity | Decimal | Number of units |
| Unit Price | Money | Price per unit |
| Line Amount | Money | Derived value: `Quantity × Unit Price`. Negative for credits or reversals. |

Each Line SHOULD include:

| Field | Type | Description |
|-------|------|-------------|
| Unit of Measure | String | UN/CEFACT Recommendation 20 preferred (e.g., `EA`, `HR`, `KG`) |
| SKU | String | Seller's internal item identifier |
| GTIN | String | Global Trade Item Number |
| Buyer Part Number | String | Buyer's internal item identifier |
| Line Discount | Money | Discount applied at the line level. Negative value. Does not alter Line Amount. |
| Line Tax | TaxEntry | Tax type, rate, and amount applicable to this line |
| PO Line Reference | String | Purchase Order line number this line fulfills |

**Line Amount derivation:** Line Amount SHALL equal `Quantity × Unit Price`. A document SHALL fail validation if the absolute difference between the declared Line Amount and the derived value exceeds 0.01 in the invoice currency. Line Discount is represented separately and does not modify Line Amount.

---

## 10. Totals (Required)

Totals SHALL include:

| Field | Type | Description |
|-------|------|-------------|
| Subtotal | Money | SHALL equal the sum of all Line Amount values. SHALL NOT include Line Discount values, taxes, shipping, or rounding adjustments. |
| Grand Total | Money | Final total after all adjustments |
| Amount Due | Money | Amount currently owed. SHALL be less than or equal to Grand Total unless a prepayment or credit is explicitly represented. |

Totals SHOULD include where applicable:

| Field | Type | Description |
|-------|------|-------------|
| Discount Total | Money | Sum of all discounts. Negative value. |
| Tax Total | Money | Sum of all taxes |
| Shipping Total | Money | Shipping and handling charges |
| Rounding Adjustment | Money | Any rounding applied to reach the Grand Total |

---

## 11. Validation Rules (Normative)

A UDT-INV v1.0 document SHALL be considered valid if all of the following conditions are met.

### 11.1 Required Presence

All fields marked SHALL in Sections 6–10 are present and non-empty.

### 11.2 Type and Format Constraints

- Dates are valid ISO 8601 calendar dates (`YYYY-MM-DD`)
- DateTimes are valid ISO 8601 timestamps with a timezone offset
- Currency codes are valid ISO 4217 values
- Decimal values do not contain non-numeric characters (no currency symbols or thousands separators)
- Environment equals `TEST` or `PRODUCTION`
- Invoice Type is one of: `STANDARD`, `CREDIT`, `DEBIT`, `PROFORMA`

### 11.3 Line Amount Derivation

For each line: `Line Amount = Quantity × Unit Price`

A document SHALL fail validation if the absolute difference between the declared Line Amount and the derived value exceeds 0.01 in the invoice currency.

### 11.4 Totals Consistency

Grand Total SHALL satisfy:

```
Grand Total = Subtotal 
            + Discount Total 
            + Tax Total 
            + Shipping Total 
            + Rounding Adjustment
```

Absent optional fields are treated as zero. A document SHALL fail validation if the absolute difference exceeds 0.01 in the invoice currency.
All monetary components in the formula SHALL use the invoice currency.
A document SHALL fail validation if the absolute difference between the declared Grand Total and the computed value exceeds 0.01 in the invoice currency.

### 11.5 Conditional Rules

- If Invoice Type is `CREDIT` or `DEBIT`, a Reference of type `INVOICE` SHOULD be present identifying the originating invoice.
- If a Line includes a PO Line Reference, the References section SHOULD contain a corresponding `PO` reference.

### 11.6 Currency Consistency

- All Money.currency values throughout the document SHALL equal Metadata.Currency.
- A document SHALL fail validation if any Money.currency value differs from Metadata.Currency.
- Future versions MAY introduce structured multi-currency extensions; such extensions SHALL NOT alter the currency of core monetary fields defined in this version.

### 11.7 Tax Consistency
- If Tax Summary is present:
  - Tax Total SHALL equal the sum of all Tax Entry.Tax Amount values.
  - A document SHALL fail validation if the absolute difference between Tax Total and the aggregated Tax Summary total exceeds 0.01 in the invoice currency.
- If Tax Summary is absent:
  - Tax Total MAY be present but SHALL NOT exceed 0.01 unless explicitly justified by an extension.

---

## 12. References (Optional, Normative if Present)

References provide cross-document linkage.

Each Reference SHALL include:

| Field | Type | Description |
|-------|------|-------------|
| Reference Type | String | One of: `PO`, `SHIPMENT`, `CONTRACT`, `INVOICE` |
| Reference ID | String | Identifier of the referenced document |

Each Reference MAY include:

| Field | Type | Description |
|-------|------|-------------|
| Referenced Line ID | String | Line within the referenced document |
| Issue Date | Date | Issue date of the referenced document |

---

## 13. Tax Summary (Optional, Normative if Present)

If present, Tax Summary SHALL include one or more entries.

Each Tax Entry SHALL include:

| Field | Type | Description |
|-------|------|-------------|
| Tax Type | String | e.g., `VAT`, `GST`, `HST`, `SALES_TAX` |
| Tax Amount | Money | Total tax collected for this entry |

Each Tax Entry SHOULD include:

| Field | Type | Description |
|-------|------|-------------|
| Rate | Decimal | Tax rate as a percentage (e.g., `13.0` for 13%) |
| Jurisdiction | String | Country/region code (e.g., `CA-ON`, `DE`) |

---

## 14. Attachments (Optional, Normative if Present)

If present, each Attachment SHALL include:

| Field | Type | Description |
|-------|------|-------------|
| Attachment ID | String | Unique identifier within this invoice |
| Name | String | Human-readable filename or label |

Each Attachment MAY include:

| Field | Type | Description |
|-------|------|-------------|
| MIME Type | String | e.g., `application/pdf`, `image/png` |
| URL | String | Externally accessible location of the attachment |
| Hash | String | Checksum with algorithm prefix (e.g., `sha256:abc123...`) |

---

## 15. Extensions (Normative)

UDT-INV supports structured extensions for industry, country, and organization-specific requirements.

Extension rules:
1. Extensions SHALL NOT override, redefine, or reinterpret any core field defined in this specification.
2. Extensions SHALL NOT duplicate existing core fields.
3. Extensions SHALL be namespaced.
4. Extensions SHOULD be optional. A receiver that does not recognize an extension SHOULD process the document using core fields only.
5. Extensions SHALL NOT change the semantic meaning of required elements.
6. Extensions SHALL NOT alter required validation rules defined in Section 11.

Namespace conventions:

| Pattern | Purpose |
|---------|---------|
| `openuda:country.<ISO2>` | Country-specific fields (e.g., `openuda:country.ca`) |
| `openuda:industry.<n>` | Industry vertical fields (e.g., `openuda:industry.healthcare`) |
| `org:<orgId>` | Organization-specific fields |

---

## 16. Minimal JSON Example (Informative)

The following is a minimal conforming UDT-INV document serialized as JSON. Optional fields are omitted.

```json
{
  "metadata": {
    "udtCode": "UDT-INV",
    "version": "1.0",
    "documentId": "urn:uuid:f47ac10b-58cc-4372-a567-0e02b2c3d479",
    "invoiceNumber": "INV-2026-0042",
    "currency": "CAD",
    "environment": "PRODUCTION",
    "createdAt": "2026-02-23T10:00:00Z"
  },
  "header": {
    "invoiceType": "STANDARD",
    "issueDate": "2026-02-23",
    "dueDate": "2026-03-25",
    "paymentTerms": "NET30",
    "status": "ISSUED"
  },
  "parties": {
    "seller": {
      "legalName": "Acme Corporation Inc.",
      "primaryIdentifier": { "scheme": "GST", "value": "123456789RT0001" }
    },
    "buyer": {
      "legalName": "Globex Canada Ltd.",
      "primaryIdentifier": { "scheme": "GST", "value": "987654321RT0001" }
    }
  },
  "lines": [
    {
      "lineId": "1",
      "description": "Software consulting services — February 2026",
      "quantity": 10,
      "unitPrice": { "amount": 150.00, "currency": "CAD" },
      "lineAmount": { "amount": 1500.00, "currency": "CAD" },
      "unitOfMeasure": "HR"
    },
    {
      "lineId": "2",
      "description": "Travel expense reimbursement",
      "quantity": 1,
      "unitPrice": { "amount": 225.50, "currency": "CAD" },
      "lineAmount": { "amount": 225.50, "currency": "CAD" },
      "unitOfMeasure": "EA"
    }
  ],
  "totals": {
    "subtotal": { "amount": 1725.50, "currency": "CAD" },
    "taxTotal": { "amount": 224.32, "currency": "CAD" },
    "grandTotal": { "amount": 1949.82, "currency": "CAD" },
    "amountDue": { "amount": 1949.82, "currency": "CAD" }
  },
  "taxSummary": [
    {
      "taxType": "HST",
      "rate": 13.0,
      "jurisdiction": "CA-ON",
      "taxAmount": { "amount": 224.32, "currency": "CAD" }
    }
  ]
}
```

**Credit note example:** For a CREDIT invoice reversing line 1, unit price and line amount use negative values:

```json
{
  "lineId": "1",
  "description": "Credit: Software consulting services — February 2026",
  "quantity": 10,
  "unitPrice": { "amount": -150.00, "currency": "CAD" },
  "lineAmount": { "amount": -1500.00, "currency": "CAD" }
}
```

---

## 17. Versioning Policy

Minor versions (1.x):

- MAY add optional elements
- MAY clarify existing definitions
- SHALL NOT remove or rename required elements
- SHALL NOT change the validation behavior of existing required fields

Major versions (2.0+):

- MAY introduce structural changes
- SHALL include migration guidance from the prior major version

---

## 18. Security Considerations (Informative)

Implementations should:

- Protect sensitive tax identifiers and banking details
- Apply access controls appropriate to financial documents
- Maintain audit trails for document creation, transmission, and status changes where required by law or policy
- Validate all incoming documents against this specification before processing

---

## 19. Change Log

| Version | Date | Notes |
|---------|------|-------|
| 1.0 | 2026-02-23 | Initial Draft |
