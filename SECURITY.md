# Security notice — keep this repository private

**This repository must remain private.** It holds a central bank's internal API
documentation and real customer identifiers. It is safe as a private working
repo; it is not safe to publish, fork publicly, or transfer without first
removing the material listed below **and rewriting the history**.

ໜ່ວຍງານນີ້ຕ້ອງເປັນ repository ແບບສ່ວນຕົວສະເໝີ. ພາຍໃນມີເອກະສານ API ພາຍໃນຂອງ
ທະນາຄານກາງ ແລະ ຂໍ້ມູນລະບຸຕົວຕົນຂອງລູກຄ້າຈິງ. ປອດໄພໃນຖານະ repo ສ່ວນຕົວ ແຕ່ບໍ່ປອດໄພ
ທີ່ຈະເຜີຍແຜ່ສາທາລະນະ, fork ອອກສາທາລະນະ ຫລື ໂອນຍ້າຍ ໂດຍບໍ່ໄດ້ລຶບເນື້ອຫາຂ້າງລຸ່ມ
ແລະ ຂຽນປະຫວັດ commit ຄືນໃໝ່ກ່ອນ.

## What makes it sensitive

The source documents live in `Attachment/`; `cms-qa.html` renders identifiers from them.

| File | Sensitive content |
|---|---|
| `Attachment/CMS_Commercial_Bank_API_Manual.html` | Bank of the Lao PDR internal technical handbook, document #004 — every API endpoint, the authentication scheme, BOL contact email and telephone, and the vendor's name |
| `Attachment/specail-acdd-import-checks.html` | Internal implementation detail — source file paths, controller and component names, and line numbers across six files of the CMS codebase |
| `cms-qa.html` | Renders real identifiers in its tables: business tax numbers, bank account numbers, an owner's national ID number, a business email address, named companies, database schema object names, and a local disk path |
| `Attachment/Exim_Accounts_TEMPLATE.xlsx` | Sample rows carrying real tax numbers, account numbers, CIF IDs and company names |
| `Attachment/business-template-exporter.xlsx` | A named company with its tax number, two bank account numbers, owner national ID and contact email |
| `Attachment/acdd-template.xlsx` | A named exporter with its tax number and six months of real invoice values |

The individual values are deliberately not repeated here — copying them into this
file would defeat its purpose. Open the files themselves if you need to see them.

## What is *not* in the repository

No passwords, API tokens, private keys or JWT values are committed. The UAT panel
addresses and logins are held outside this repository and must stay that way.
`.claude/settings.local.json` contains only tool permissions and a skill override.

## Before you change anything about access

Each of these exposes the full contents, including everything ever committed:

- **Making the repository public** — including as a side effect of transferring
  it to an organisation whose default visibility is public.
- **Adding a collaborator** — read access is access to the entire history, not
  just the current files.
- **Forking** — a fork carries the history with it and survives changes to the
  original.

## If material must be removed

Deleting a file in a new commit **does not remove it**. It stays in the history
and remains retrievable from any clone or fork. Removing it properly means
rewriting history:

```bash
# example — remove one file from every commit
git filter-repo --path Attachment/CMS_Commercial_Bank_API_Manual.html --invert-paths
git push --force origin main
```

Then have every collaborator re-clone; their existing clones still hold the old
history. Treat anything that was pushed as disclosed to everyone who had access
during that window, and judge whether it needs reporting on that basis.

## A worked example from this repository's own history

Commit `34e4646` included a line in `CLAUDE.md` that embedded two UAT password
prefixes and the internal panel IP address inside a validation regular
expression. The intent was defensive — a pattern to *check* that credentials had
not leaked into the published page — but writing the pattern down put the
credentials into a tracked file, and pushing it put them on GitHub.

It was found by a scan of the committed files immediately after the push and
corrected in the following commit, which replaced the literal values with a
generic private-IP pattern.

Two lessons worth keeping:

1. **A rule that names a secret becomes a copy of that secret.** Write checks
   that describe the shape of what you are looking for, never the value.
2. **Scan what you are about to commit, not what you meant to commit.** The
   credentials had been kept out of the deliverable carefully and for weeks;
   they reached the repository through the guidance file instead.

Because the corrected commit does not rewrite history, those prefixes remain in
commit `34e4646`. Given the repository is private and the values are partial and
belong to a UAT environment, the working decision was to leave the history
intact. **If this repository is ever made public or shared more widely, rewrite
the history first** — see the section above.

## Reporting

Raise anything found here with the repository owner directly rather than opening
an issue, since issue text is visible to everyone with repository access.
