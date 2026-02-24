# OpenUDA Specifications

The Open Universal Documents Association (OpenUDA) publishes open, encoding-agnostic specifications for interoperable business document exchange.

This repository contains the official OpenUDA specifications, including:

- Universal Document Type (UDT) standards
- Encoding profiles (JSON, XML)
- Mapping profiles (EDI, PEPPOL, etc.)
- Governance and versioning policies

OpenUDA defines the **canonical semantic model** for business documents.  
Encoding formats and transport mechanisms are defined separately and do not alter the normative meaning of a UDT.

---

## Current Standards

| UDT Code | Name     | Version | Status |
|----------|----------|---------|--------|
| UDT-INV  | Invoice  | 1.0     | Draft  |

---

## Repository Structure

- `/standards` — Normative UDT specifications
- `/encoding-profiles` — Format-specific serialization profiles
- `/mapping-profiles` — Interoperability mapping guides
- `/governance` — Governance and versioning policies

---

## Contributing

Proposals for changes or new UDTs should be submitted via GitHub Issues.  
All normative changes follow the OpenUDA governance process.

---

## License

This repository is licensed under the Apache 2.0 License.
