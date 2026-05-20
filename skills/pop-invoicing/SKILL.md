---
description: Use POP Cloud API tools to create, validate, submit, retrieve, or preserve Italian SdI/FatturaPA, Peppol, and PDF invoices.
---

# POP Invoicing

Use this skill when the user wants to work with invoices through POP.

Before creating an invoice, collect the minimum fiscal data needed for the chosen invoice type:

- Supplier: legal name, VAT or tax ID, address, tax regime, contact details if available.
- Customer: legal name, customer type, VAT or fiscal code, address, and SDI or PA office code when applicable.
- Invoice: type, date, number, currency, line items, VAT rates, totals, and payment details.
- Submission: whether to generate only or submit to SdI or Peppol.

Key rules:

- For Italian private customers without an SDI code, use `sdi_type` `0000000` and include `tax_id_code`.
- For Public Administration, use FatturaPA version `FPA12` and the 6-character PA office code.
- For standard B2B/B2C invoices, use FatturaPA version `FPR12`.
- When VAT rate is `0`, include the correct `nature` code.
- For bank transfer payment method `MP05`, include beneficiary, financial institution, and IBAN.
- Peppol supports company and freelance customers, not private individuals.
- PDF email delivery supports up to 3 recipients and requires a paid plan.
- Preserve SdI documents only after an accepted/deliverable SdI status such as RC or MC.

Prefer draft/validation flows before submission when data is incomplete or the user is unsure. Never invent fiscal identifiers; ask for missing VAT numbers, fiscal codes, SDI codes, IBANs, or addresses when required for a valid invoice.

