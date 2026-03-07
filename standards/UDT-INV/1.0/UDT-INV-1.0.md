# OpenUDA Universal Document Type (UDT)
Invoice (UDT-INV) Version 1.0

UDT Code: UDT-INV
Version: 1.0
Status: Draft
Canonical Model: Encoding-agnostic
Publication Date: 2026-02-23

---

# 1. Introduction

This document defines the OpenUDA Universal Document Type for a **commercial Invoice** ("UDT-INV").

UDT-INV provides a canonical, interoperable definition of invoice structure and semantic meaning for business document exchange. The specification is encoding-agnostic and may be serialized using JSON, XML, or other formats via official OpenUDA encoding profiles.

The design emphasizes **low-entropy canonical structure** while supporting common North American tax scenarios and the operational requirements of the **IT supplier, distributor, VAR, and reseller ecosystem**.

Credit memos, adjustments, and returns often require additional structures such as reason codes, RMA references, and invoice linkage. Those scenarios SHOULD be represented using a specialized UDT (e.g., **UDT-CM**) rather than UDT-INV.

---

# 2. Scope

UDT-INV v1.0 defines:

• Core commercial invoice structure  
• Party identification  
• Line-level detail  
• Monetary totals  
• Line-level tax detail (0..N per line)  
• Optional aggregated tax summary  
• Cross-document references  
• Attachment references  
• Extension framework  

This specification does not define:

• Credit memo / adjustment document structures  
• Transport protocols (AS2, SFTP, REST, etc.)  
• Authentication mechanisms  
• Exchange network governance rules  
• Partner onboarding workflows  
• ERP-specific implementation logic  

---

# 3. Conformance

A document conforms to UDT-INV v1.0 if it:

• Declares **UDT Code = UDT-INV**  
• Declares **Version = 1.0**  
• Includes all required elements defined in this specification  
• Passes validation rules in Section 11  
• Uses extensions only as defined in Section 15  

---

# 4. Document Structure

A UDT-INV document SHALL contain the following sections:

• Metadata  
• Header  
• Parties  
• Lines (one or more)  
• Totals  

The following sections are OPTIONAL but normative if present:

• References  
• Tax Summary  
• Attachments  
• Extensions  

---

# 5. Data Types

## Primitive Types

| Type | Description | Example |
|-----|-------------|--------|
| String | Human-readable text | "Acme Corp" |
| Identifier | Scheme + value pair | `{ "scheme":"GST","value":"123456789RT0001" }` |
| Date | ISO-8601 date | "2026-02-23" |
| DateTime | ISO-8601 timestamp | "2026-02-23T10:00:00Z" |
| Decimal | Numeric value | 12.50 |
| CurrencyCode | ISO-4217 code | CAD |
| Money | `{ amount, currency }` | `{ "amount":150.00,"currency":"CAD" }` |

---

## Complex Types

### Address

street1 (String)  
street2 (String) OPTIONAL  
city (String)  
region (String) OPTIONAL  
postalCode (String)  
country (CountryCode)

---

### Contact

name (String) OPTIONAL  
email (String) OPTIONAL  
phone (String) OPTIONAL

---

### TaxDetail

Represents a single tax component applied to a line.

Fields:

| Field | Type | Requirement |
|-----|-----|-----|
| taxType | String | SHALL |
| jurisdiction | Identifier | SHALL |
| taxCode | Identifier | SHOULD |
| rate | Decimal | SHOULD |
| taxableAmount | Money | SHALL when taxAmount ≠ 0 |
| taxAmount | Money | SHALL |

Notes:

OpenUDA is **not the authority** for jurisdiction codes.  
Jurisdiction identifiers SHOULD originate from the sender's ERP or tax engine.

---

# 6. Metadata (Required)

Metadata provides document identification and routing information.

### Metadata SHALL include

| Field | Type |
|-----|-----|
| udtCode | String |
| version | String |
| documentId | UUID / URN |
| invoiceNumber | String |
| currency | CurrencyCode |
| environment | TEST or PRODUCTION |

### Metadata SHOULD include

| Field | Type |
|-----|-----|
| createdTimestamp | DateTime |
| senderIdentifier | Identifier |
| receiverIdentifier | Identifier |

---

# 7. Header (Required)

### Header SHALL include

| Field | Type |
|-----|-----|
| issueDate | Date |

### Header SHOULD include

| Field | Type |
|-----|-----|
| dueDate | Date |
| paymentTerms | String |
| servicePeriodStart | Date |
| servicePeriodEnd | Date |
| status | String |
| notes | String |

---

# 8. Parties (Required)

Roles:

Seller SHALL  
Buyer SHALL  
Bill-To SHOULD if different  
Ship-To SHOULD if applicable  
Remit-To SHOULD if applicable  

Each party SHALL include:

| Field |
|-----|
| legalName |
| primaryIdentifier |

Each party SHOULD include:

| Field |
|-----|
| taxIdentifiers[] |
| address |
| contact |

---

# 9. Lines (Required)

Each invoice SHALL contain at least one line.

### Required Fields

| Field | Type |
|-----|-----|
| lineId | String |
| description | String |
| quantity | Decimal |
| unitPrice | Money |
| lineAmount | Money |

Line Amount = Quantity × Unit Price

---

### Recommended Fields (Common in IT Distribution)

| Field | Type |
|-----|-----|
| unitOfMeasure | String |
| SKU | String |
| manufacturerPartNumber | String |
| GTIN | String |
| buyerPartNumber | String |
| serialNumbers | String[] |
| shipDate | Date |
| lineDiscount | Money |
| orderReference | Object |
| taxDetail | TaxDetail[] |

---

### Order Reference


orderReference:
orderId
orderLineId


Allows mapping to purchase orders, sales orders, or drop-ship orders.

---

### Serial Numbers

Serialized hardware items MAY include:


serialNumbers[]


Example:


["FCW2148L0AB","FCW2148L0AC"]


---

### Tax Detail

Each line MAY include multiple taxDetail entries.

Example:


taxDetail[]


Supports stacked US tax jurisdictions and Canadian GST/PST/HST combinations.

---

# 10. Totals (Required)

Totals SHALL include:

| Field |
|-----|
| subtotal |
| taxTotal |
| grandTotal |
| amountDue |

Totals SHOULD include:

| Field |
|-----|
| discountTotal |
| shippingTotal |
| roundingAdjustment |

---

# 11. Validation Rules

## Required Presence

All SHALL fields must exist.

---

## Line Amount Validation


lineAmount = quantity × unitPrice


Tolerance: 0.01

---

## Grand Total Validation


grandTotal =
subtotal

discountTotal

taxTotal

shippingTotal

roundingAdjustment


Tolerance: 0.01

---

## Currency Consistency

All monetary values MUST match Metadata.currency.

---

## Tax Consistency

Rules:

1. Tax is sourced from line.taxDetail[]  
2. totals.taxTotal = sum(all taxDetail.taxAmount)  
3. If no taxDetail exists, taxTotal SHALL be 0  

Tolerance: 0.01

---

# 12. References

Optional section.

Each entry SHALL include:

| Field |
|-----|
| referenceType |
| referenceId |

Types:

PO  
SHIPMENT  
CONTRACT  

---

# 13. Tax Summary (Optional)

Optional aggregated tax totals.

Used for interoperability with systems that prefer header-level tax.

Source of truth remains **line.taxDetail[]**.

---

# 14. Attachments

Optional section.

Fields:

| Field |
|-----|
| attachmentId |
| name |
| mimeType |
| url |
| hash |

---

# 15. Extensions

Extensions SHALL:

• Not redefine core fields  
• Be namespaced  
• Be optional for receivers  

Namespace patterns:


openuda:country.<ISO2>
openuda:industry.<name>
org:<orgId>


---

# 16. Minimal JSON Example

```json
{
  "metadata":{
    "udtCode":"UDT-INV",
    "version":"1.0",
    "documentId":"urn:uuid:f47ac10b-58cc-4372-a567-0e02b2c3d479",
    "invoiceNumber":"INV-2026-0042",
    "currency":"CAD",
    "environment":"PRODUCTION"
  },

  "header":{
    "issueDate":"2026-02-23",
    "dueDate":"2026-03-25"
  },

  "lines":[
    {
      "lineId":"1",
      "description":"Cisco Catalyst 9300 Switch",
      "quantity":2,
      "manufacturerPartNumber":"C9300-24T-A",
      "unitPrice":{"amount":450,"currency":"CAD"},
      "lineAmount":{"amount":900,"currency":"CAD"},
      "serialNumbers":["FCW2148L0AB","FCW2148L0AC"],
      "taxDetail":[
        {
          "taxType":"HST",
          "jurisdiction":{"scheme":"erp:sap","value":"CAON_HST"},
          "rate":13,
          "taxableAmount":{"amount":900,"currency":"CAD"},
          "taxAmount":{"amount":117,"currency":"CAD"}
        }
      ]
    }
  ],

  "totals":{
    "subtotal":{"amount":900,"currency":"CAD"},
    "taxTotal":{"amount":117,"currency":"CAD"},
    "grandTotal":{"amount":1017,"currency":"CAD"},
    "amountDue":{"amount":1017,"currency":"CAD"}
  }
}
```

---

# 17. Versioning Policy

Minor versions (1.x):

• MAY add optional fields
• SHALL NOT change existing semantics

Major versions (2.x):

• MAY introduce structural changes
• MUST include migration guidance

---

# 18. Security Considerations

Implementations SHOULD:

• Protect tax identifiers
• Maintain audit trails
• Validate incoming documents

---

# 19. Change Log
Version	Date	Notes
1.0	2026-02-23	Initial draft
