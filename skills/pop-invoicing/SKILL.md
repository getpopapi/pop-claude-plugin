---
description: Use POP Cloud API tools to create, validate, submit, retrieve, or preserve Italian SdI/FatturaPA, Peppol, and PDF invoices. Also guides account onboarding via the 5-step OTP flow.
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

## POP Account Onboarding

Use this flow when the user wants to create or set up a POP account. The sequence is always:

1. `pop_onboarding_request_otp` — Send an OTP to the user's email. The OTP code is returned inline in the response (`data.otp_code`) — read it directly, do not ask the user to check email. For administrator accounts no OTP is issued; use the admin password as the otp field in step 2.
2. `pop_onboarding_verify_otp` — Verify the OTP. Returns an `onboarding_token` (48-char string, valid 30 minutes). Save it for all subsequent calls. If the token expires, restart from step 1.
3. `pop_onboarding_get_status` — Optional. Check the current wizard state, required fields, and integration availability.
4. `pop_onboarding_get_account_setup` — Retrieve the full account payload: current field values, lookup tables (countries, tax regimes, Peppol schemes), capabilities, and field locks. Call this before saving to know what is already set and what fields are locked.
5. `pop_onboarding_save_account_setup` — Save the account configuration and activate integrations.

Key onboarding rules:

- SdI (Italy/San Marino) and Peppol are mutually exclusive. Never set both `active_sdipop_integration=1` and `active_peppol_integration=1` at the same time.
- `general_store_vat_number` must be unique on the target environment. Returns 422 if already in use.
- `general_store_country` is locked after the first save and cannot be changed.
- Fields marked as locked (`field_locks=true` in `get_account_setup`) cannot be modified; the API will reject them.
- Once the wizard is complete (`wizard_completed=true`), calling `save_account_setup` again returns the current state without writing (`applied_changes=false`).
- For Peppol: once the legal entity UUID is set, all Peppol fields are permanently locked.
- No API key required for request-otp and verify-otp. All other onboarding calls need only the onboarding token, not POP_API_KEY.

