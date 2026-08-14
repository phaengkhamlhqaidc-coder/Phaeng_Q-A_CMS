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
| `cms-qa.html` | **The deliverable** — 122 Q&A pairs across 13 panels, self-contained, opens in any browser |
| `Attachment/CMS_Commercial_Bank_API_Manual.html` | Authoritative technical source (BOL document #004). Reproduced in full inside the manual, as the *API Manual — full text* panel |
| `Attachment/specail-acdd-import-checks.html` | Source for the ACDD import validation rules |
| `Attachment/Exim_Accounts_TEMPLATE.xlsx` | Intake template — step 2 |
| `Attachment/business-template-exporter.xlsx` | Cleaned-data template — step 3 |
| `Attachment/acdd-template.xlsx` | Special Product ACDD template |
| `CLAUDE.md` | Guidance for future work on this folder |

## Using it

Open `cms-qa.html` in a browser. **That one file is all you need** — the API
manual is rendered inside it and the three templates are embedded in it, so you
can email or copy the page on its own. `Attachment/` holds the originals those
were generated from; keep it in the repository, but the page does not read it.

Nothing in the page opens a second tab or a separate file. The API manual is the
last item in the sidebar's *Reference* group: it opens as a page, with an index
of its 22 sections at the top. The templates download straight from the sidebar.

The page switches between **English**, **ລາວ** and both side by side; the filter
box searches within a category — including the full text of the API manual — and
any category can be opened directly by its anchor, e.g. `#g` for the FDI module
or `#manual` for the handbook.
