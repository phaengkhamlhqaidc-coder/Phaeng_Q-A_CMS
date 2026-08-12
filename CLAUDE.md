# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this folder is

Not a codebase. It is a documentation working folder holding one deliverable: a **bilingual (English / Lao) user manual**, built as a single-page admin console, for **Commercial Bank (CB)** staff *and their managers* who register customer accounts with the **Bank of the Lao PDR (BOL)** in CMS and then operate the Exporter and FDI modules.

There is no build, no test suite, no dependency manifest. Verification is done by running assertion scripts against the HTML (see **Validating a change**).

| File | Role |
|---|---|
| `cms-qa.html` | The deliverable — self-contained, ~598 KB, no external assets |
| `CMS_Commercial_Bank_API_Manual.html` | **Authoritative source** for anything technical. Linked from the sidebar |
| `Exim_Accounts_TEMPLATE.xlsx` | Intake template (step 2). Linked + rendered in panel B |
| `business-template-exporter.xlsx` | Cleaned template (step 3). Linked + rendered in panel C |
| `acdd-template.xlsx` | Special Product ACDD template. Linked + rendered in the ACDD panel |
| `specail-acdd-import-checks.html` | Source for the ACDD import validation rules (note the typo in the filename). Not linked from the page |

All five linked files must stay in the same folder — the sidebar hrefs are relative. They resolve when the page is opened from disk; **on the published artifact they will 404**, since only `cms-qa.html` is uploaded. If the artifact needs to work standalone, the templates would have to be embedded as data URIs.

## Publishing

`cms-qa.html` is published as a private Claude artifact:

```
https://claude.ai/code/artifact/80fc0223-6e70-4d91-8145-deaca32ed754
```

Republish **the same file path** to update in place. A different path — or omitting `url` from a fresh conversation — creates a *separate* artifact. Keep the `📋` favicon stable across redeploys.

Self-contained by necessity: a strict CSP blocks external fonts, scripts and stylesheets. All CSS is inline, the Lao font is embedded, and typefaces are otherwise system stacks.

## Source of truth

`CMS_Commercial_Bank_API_Manual.html` — the *CMS Commercial Bank API Technical Handbook*, document #004, issued by BOL with AIDC Tech Co., Ltd — outranks this manual on every technical point. When the two disagree, the handbook is correct and the page gets corrected. (Earlier drafts referenced a `CMS Delivery Document - BOL.html`; that name is retired and all references were repointed.)

### Reading the handbook programmatically

It is a single-page app, not static HTML — grepping rendered text finds nothing. All content sits in one JSON array assigned to `SECTIONS` (22 sections, `intro` → `constraints`). Extract by finding `SECTIONS`, walking from the next `[` to its matching `]`, and `json.loads`-ing the slice. Every string is an `{"en": …, "lo": …}` pair, so **the handbook is also the authority on official Lao terminology** — check house wording against it before inventing a translation.

`specail-acdd-import-checks.html` is plain static HTML; strip tags and read it directly.

**Do not invent technical detail.** Where a source is silent, the answer stays generic and points at the handbook. Several answers deliberately say "refer to the API manual" — that is intended behaviour, not a gap to fill.

## Hard constraint: credentials

UAT panel addresses and logins exist but **must never appear in published output**. Refer to panels generically ("log in to the BOL Panel"). The footer states that addresses and credentials are held separately — keep that true. Every validation run asserts that no credential or IP string is present.

**Browser access to the UAT panels does not work from here.** The hosts respond (HTTP 200 via curl) but Chrome renders an error page — most likely the extension lacks site permission for that host. Separately, Claude cannot type passwords into login forms under any circumstances. If panel screens need documenting, the user must log in themselves and leave the tabs open.

## The content facts that matter most

**There is one API, not one per module.** Exporter, Importer and FDI are values of a `businessType` parameter (`exporter`, `importer`, `importer&exporter`, `fdi`) carried on the *same* endpoints; it appears on `POST /tin` and `GET /cif-info` and is optional. An earlier draft said "FDI has fewer API endpoints" — that was **wrong** and was corrected throughout. Do not reintroduce it.

**FDI does not use Unlock, Exchange or Void.** Those endpoints exist and work; they are simply not part of the FDI flow, which is Sync Transaction → Approve or Reject. This is the single most common source of staff confusion and is stated in four places: the panel A callout, the Overview callout, a dedicated caution Q&A opening panel G, and a cross-reference from panel F. Preserve all four.

**Exporter carries the full set:** ACDD matching, Track Transaction, Unlock, Exchange, Repay Debt, Void. Repay Debt happens in the **mobile app**, not the panel.

Documented endpoints: `POST /login`, `POST /tin`, `GET /cif-info`, `POST /cms-ids`, `POST /transactions`, `POST /unlock`, `POST /exchange`, `POST /repay-debts`, `GET /bank-accounts`, `GET /bank-info`, `GET /exchange-rate`. **Void has no endpoint of its own** — it reuses Unlock with `cmsTrackId` sent empty. Auth is JWT; every call also requires `Content-Type: application/json`.

## Page architecture

A panel-switching single-page app — **not** a scrolling document. `<div class="app" data-lang="…">` wraps a sidebar and a content area; each category is a `<section class="panel" id="panel-…">` and only one carries `.active` at a time. JS at the foot of the file handles panel switching, hash routing (`#g` opens FDI directly), the language toggle, expand/collapse-all, the filter box, and the regime-code checker.

Twelve panels, **122 Q&A pairs**:

| Panel | Title | Q&A | Tables |
|---|---|---|---|
| `overview` | Overview & Module Scope | 0 | 1 |
| `a` | Getting Started / Documentation | 11 | 1 |
| `b` | Data Preparation & Templates | 7 | 1 |
| `c` | Data Cleaning & TIN Verification | 19 | 2 |
| `d` | Registration in CMS (BOL Panel) | 10 | 0 |
| `e` | Troubleshooting Failed Registrations | 19 | 2 |
| `f` | Using the Exporter Module | 19 | 0 |
| `acdd` | Special Product — ACDD | 15 | 2 |
| `g` | Using the FDI Module | 11 | 2 |
| `report` | Reporting — What Is Registered | 11 | 2 |
| `terms` | Terms & Abbreviations | 0 | 1 |
| `ref` | Quick Reference Table | 0 | 1 |

A–G is the client's taxonomy — don't renumber. `overview`, `acdd`, `report`, `terms` and `ref` are additions and carry no letter.

## Q&A markup

Each entry is a `<details>` accordion. **This replaced an earlier `<article class="qa">` / `.pane` structure — that markup no longer exists.**

```html
<details class="qa branch">
  <summary><span class="qmk">Q</span><span class="qtext">
    <span lang="en"><span class="cond">If no export occurred</span>Question text</span>
    <span lang="lo"><span class="cond">ກໍລະນີບໍ່ມີການສົ່ງອອກ</span>…</span>
  </span><span class="chev"></span></summary>
  <div class="abody">
    <div lang="en">Answer text<span class="tech"><span class="techlab">Detail</span>…</span></div>
    <div lang="lo">…</div>
  </div>
</details>
```

Variants: `.qa.branch` (amber rail, one conditional path — **38 in use**), `.qa.caution` (a trap to avoid, not a fork — **6 in use**), plus `open` for the four entries that should start expanded. `.cond` labels the condition; `.caut` labels a caution.

Conditional branches are the load-bearing convention: where the workflow forks, each branch is its **own** Q&A, never several conditions merged into one sentence.

## Writing standard

The audience is **CB staff and managers/executives**, so every answer follows one rule:

**Plain business language first; technical detail second, inside `<span class="tech">`.** A manager reads the opening and stops; staff read on. 44 such blocks exist (22 answers × 2 languages). The label is `Detail` / `ລາຍລະອຽດ`.

Substitute jargon: "hit the API endpoint" → "send the request to the system"; "Base URL / Login URL" → "the two web addresses BOL needs to reach your system". Where a technical term must appear, gloss it — SWIFT as "the international bank-transfer network", JWT as "a secure sign-in pass".

## Abbreviations

Because the page is **paged**, a reader can enter at any panel — so "explain on first use" has to mean *first use in that panel*. Two mechanisms:

- **`.abbrbar`** — a strip at the top of all 10 content panels listing only the abbreviations that panel actually uses, with expansions. Generated by scanning each panel's English text; regenerate it if a panel gains a new abbreviation.
- **The `terms` panel** — the full glossary, one sidebar click from anywhere. It used to sit inside panel A, where readers who never opened A could not find it.

## Bilingual structure

Every Q&A carries `<div lang="en">` and `<div lang="lo">`; every heading, note, table cell and footer pairs an `[lang="en"]` span with a `[lang="lo"]` one. **Both must exist for every item** — an unpaired element leaves a blank in one mode.

A three-way toggle (`English` / `ລາວ` / `EN + ລາວ`) sets `data-lang` on `.app`. Two rules do all the switching:

```css
.app[data-lang="en"] [lang="lo"] { display: none; }
.app[data-lang="lo"] [lang="en"] { display: none; }
```

Default is `both`; the answer body splits into two columns above 66rem.

**Two traps:**

1. **Never tag English text inside a Lao block with `lang="en"`** — it vanishes in Lao mode. Leave system labels (Unlock, Exchange, Void, Track Transaction, file names) untagged; they inherit `lang="lo"` and fall back per-glyph to the Latin sans. This is deliberate: the CMS interface is English, so a Lao reader still sees the button name they will click.
2. **Lao text that is *data*, not translation** — e.g. the business name `ບໍລິສັດ ນ້ຳຕານ ສະຫວັນນະເຂດ` in a template table — must use **`class="laotext"`**, never `lang="lo"`, or the cell empties in English mode. 8 such cells exist.

## Lao typography

`Noto Sans Lao` v2.003 (variable, weight 100–900, width 62.5–100%) is embedded as a base64 `data:` URI — 230 KB of the file's 598 KB. Required, not preference: the CSP blocks font CDNs and a linked webfont would silently fall back.

Source: `https://raw.githubusercontent.com/google/fonts/main/ofl/notosanslao/NotoSansLao%5Bwdth,wght%5D.ttf`. On this machine `curl` needs `--ssl-no-revoke` (the TLS revocation server is unreachable). To re-embed, keep a `__LAO_FONT_B64__` placeholder in the `src:` line and substitute with Python `base64.b64encode` — never paste the blob by hand.

Lao gets `line-height: 1.85` (vowels and tone marks stack above and below the baseline). `.cond`, `.caut`, `.techlab` and table captions all drop `text-transform: uppercase` and letter-spacing under `[lang="lo"]` — neither applies to Lao script.

**Watch for spacing when bulk-replacing Lao strings.** A previous global swap produced `ຄູ່ມື APIວ່າ` with no space in seven places. After any `str.replace` on Lao text, check `re.findall(r'ຄູ່ມື API[\u0E80-\u0EFF]', h)` returns empty.

## Styling

Token-based theming, three viewer states: bare `:root` = full light palette; `@media (prefers-color-scheme: dark)` guarded by `:root:not([data-theme="light"])`; `:root[data-theme="dark"]` again so an explicit toggle wins both ways. **Never declare a color only inside a media or `[data-theme]` block** — it will not apply in the un-stamped default state.

Teal accent (`--accent`) on a green-biased neutral; amber (`--branch`) reserved for conditional paths and cautions. Serif display, system sans body, mono for filenames and table keys. Wide tables live in `.scroller` (`overflow-x: auto`) so the body never scrolls sideways.

## Step numbering

The Quick Reference table uses **number for step, letter for sub-step**: `1 · 2 · 3a · 3b · 3c · 4a · 4b · 5a · 5b · 5c · 5d · 5e · 5f`. Steps 1–4 are one-time set-up; 5a–5f are daily work.

This replaced dotted numbers (`5.1.1`) and then a phase-symbol scheme (◆ ▶ ■) — **the user rejected both**. The symbols were confusing because they encoded phase *and* order in one glyph, requiring a legend to read a column that should be readable at a glance; the Exporter/FDI split they encoded is already visible in the Tool column. Don't reintroduce symbols. The API endpoint table keeps `6b` / `6c` (handbook sections 6.2 / 6.3) under the same letter convention.

## The non-tracked regime codes

The ACDD panel carries a `.codebox` of 42 regime / customs procedure codes. A transaction with any of them is never tracked and never appears in Track Transaction — the correct action is to **Void directly instead of waiting**:

```
2000s  2000 2100
3000s  3050 3054 3055 3056 3057 3082 3085
4000s  4051 4052 4054 4071 4072 4082 4700
5000s  5000 5081 5085 5400 5481 5500 5600 5681 5685 5700 5781 5785
6000s  6020 6021 6025
7000s  7100 7171 7175 7200 7271 7272
8000s  8100 8200 8300 8340 8500
```

The rendered `<code class="cc">` chips are the **single source of truth** — the checker JS reads `#codegrid .cc` at runtime rather than holding a second copy. Edit the markup only; never hard-code an array in the script. If the list changes, update the "42 codes" count in three places (branch answer, dedicated Q&A, codebox footnote).

## Special Product / ACDD import

A Special Product is an export with no ordinary customs declaration to sync from ASYCUDA — electricity is the worked example. Its ACDD is loaded from `acdd-template.xlsx` instead. Tells: `Customs Office` = **BOL** with code **0**, own unit (`Volume (KWh)`), and **monthly** rows rather than one per shipment. ACDD ID format is `<TIN>-E<YYYY>-<MM>`. Verified arithmetic rule: **Total Invoice Amount = Volume × Unit Price** (holds on all six sample rows at 0.0695 USD/kWh).

Import validation, from `specail-acdd-import-checks.html`:

- **12 of 15 columns are enforced; 3 are never examined** — Product Name, Customs Office Code, Business Name go straight to the database. A declaration can be saved with a non-existent border code and the import reports success.
- **All or nothing.** One bad cell blocks the whole file; there is no partial import.
- **The Status tick only inspects 7 of 15 columns.** Errors on date, currency, unit price, exchange rate and volume never reach it — rows show green while Apply stays disabled. The error download is the only place the message appears.
- **Volume is mandatory on every row**, even non-electricity; blank/dash/zero all rejected, and the error is mislabelled `Invalid unit price`. Exchange rate reports under the same wrong label.
- **Dates must be text** `YYYY-MM-DD`. Excel silently stores `1/1/2026` as serial `46023`, which is what gets validated. Fix: format the column as Text, or prefix with an apostrophe.
- **A renamed or deleted header crashes the upload** with a server error rather than a readable message.

## Validating a change

There is no test runner; use a Python assertion script over the HTML. Every pass should check at minimum:

- Tag balance for `details`, `section`, `div`, `table`, `tr`, `td`, `th`, `span`, `p`, `a`
- `lang="en"` count == `lang="lo"` count, page-wide **and within every single `<details class="qa">`**
- Every `data-panel` resolves to a `panel-*` id, and every panel is reachable from the sidebar
- Exactly one `.panel.active`
- 42 regime-code chips; 6 ACDD sample rows; 11 `.abbrbar` strips (one per content panel)
- `.tech` block count is even (en/lo pairs)
- No credentials or internal addresses. Scan for private-range IPs with `\b(?:10|127|192\.168|172\.(?:1[6-9]|2\d|3[01]))\.\d{1,3}\.\d{1,3}\b`, and for the UAT panel passwords — **do not paste the literal passwords into this file or any tracked file**; they live only in the operator's own notes. A generic sweep that catches most cases: `(?i)(pass(word)?|pwd|token|secret|api[_-]?key)\s*[:=]`
- No stale strings: `Delivery Document`, `fewer API endpoints`, dotted step numbers, Lao spacing bug

**Beware over-broad extraction.** Lifting a block by searching for a closing `</div>` string matched far too late once and swallowed a whole panel. Balance the tags instead of string-matching the close.

## Open items

- **The Lao translation has not been reviewed by a native speaker.** Produced by Claude, not a translator. Cheapest review: diff this page's Lao against the handbook's `lo` strings — same institution, same house wording. Note the handbook abbreviates Bank of the Lao PDR as **ທຫລ** where this page uses the Latin **BOL**; align if the bank prefers. Also note the handbook's own Lao contains **Thai characters** in places (`พารามิเตอร์` in the 400 error and two System Constraints entries) — do not copy those through.

Resolved; recorded so they are not re-opened:

- `ACDD` = **Advance Cargo Declaration Document** (user-supplied, 2026-08-11).
- The Track Transaction "reference number outside the tracked category" branch was once inferred. It now carries the real rule — check the regime code against the 42 non-tracked codes and Void directly if it matches (2026-08-11).
- The FDI transaction fields table once had empty M/O and Type columns. Filled from the handbook (2026-08-12): `senderName` **M**, `originCountry` **M**, `bankNameOrigin` **O**, `bankAccountOrigin` **O**, `transactionDescription` **O** — all strings, the last three sourced from SWIFT.
- "FDI has fewer API endpoints" was corrected to the one-API / `businessType` model (2026-08-12).

FDI approval/rejection criteria are intentionally left to the bank's own policy — the source defines the mechanism, not the decision rules.

## Related work elsewhere

The systems described here have their own folders under `C:\Users\HP_PC\Downloads\` — notably `MOIC-TIN\AIDC-MOIC-TIN` (TIN batch search and validation, matching the panel C flow) and `PhaengCronJob_Automate`. Separate projects with their own `CLAUDE.md` files; this folder does not depend on them, but that is where the TIN tooling actually lives.
