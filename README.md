# Phaeng_Q-A_CMS

> ⚠️ **Private repository — keep it private.** Contains Bank of the Lao PDR
> internal API documentation and real customer identifiers.
> Read [SECURITY.md](SECURITY.md) before making this public, adding a
> collaborator, or transferring ownership.

Bilingual (English / Lao) user manual for **Commercial Bank (CB)** staff and
management covering account registration in **CMS** and day-to-day operation of
the **Exporter** and **FDI** modules.

## Contents

| File | What it is |
|---|---|
| `cms-qa.html` | **The deliverable** — 122 Q&A pairs across 12 panels, self-contained, opens in any browser |
| `CMS_Commercial_Bank_API_Manual.html` | Authoritative technical source (BOL document #004). Linked from the manual's sidebar |
| `specail-acdd-import-checks.html` | Source for the ACDD import validation rules |
| `Exim_Accounts_TEMPLATE.xlsx` | Intake template — step 2 |
| `business-template-exporter.xlsx` | Cleaned-data template — step 3 |
| `acdd-template.xlsx` | Special Product ACDD template |
| `CLAUDE.md` | Guidance for future work on this folder |

## Using it

Open `cms-qa.html` in a browser. **Keep all files in the same folder** — the
sidebar links to the API manual and the three templates are relative and will
not resolve otherwise.

The page switches between **English**, **ລາວ** and both side by side; the filter
box searches within a category, and any category can be opened directly by its
anchor, e.g. `#g` for the FDI module.
