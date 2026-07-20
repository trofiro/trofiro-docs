---
name: trofiro-docs
description: >-
  Generate the product documentation for the feature on the current branch: resolve the
  branch to its Linear issue (must carry the "Product Documentation" label), research the
  issues in that issue's project, verify shipped behavior in `../trofiro-frontend`, then
  create or update the guide page in all languages (en + pt-BR), FAQ and troubleshooting
  sections if needed, introduction/quickstart touch-ups, and a changelog entry. Use when the user
  asks to "document this feature", "generate docs for this branch/feature", "write the
  feature docs", or "trofiro-docs". Writes local files only — no commit, no PR, and Linear
  is read-only.
---

# trofiro-docs

Generate the complete documentation package for the feature tied to the current branch:
a guide page, FAQ and troubleshooting additions where warranted, introduction and
quickstart touch-ups, and a changelog entry — always in **all languages** (`en/` and
`pt-BR/`). Linear provides the feature map; the `trofiro-frontend` source provides the
facts.

## When to use

On a docs-repo feature branch whose Linear issue is a documentation issue (labeled
**Product Documentation**), when the user wants the feature's docs generated or refreshed.
Requires the **Linear MCP** (`get_issue`, `list_issues`) and a local checkout of the
sibling repo `../trofiro-frontend`.

## 0. Prerequisites (verify first)

- **Linear MCP** available (`get_issue`, `list_issues`).
- **`../trofiro-frontend` exists locally** — it is the source of truth for behavior and UI
  strings. If missing, stop and tell the user.
- Run from the repo root.
- **Read `CLAUDE.md` before writing anything.** It is the authority on terminology (en and
  pt-BR tables), style, the page pattern, and content boundaries. This skill does not
  restate those rules — it defers to them.

## 1. Guardrails (stop early if these fail)

1. **Not on the default branch.** `git branch --show-current` must not be `main` (nor
   `master`). If it is, stop — the user needs a feature branch.
2. **Resolve the issue id.** First `([A-Za-z]{2,}-[0-9]+)` match in the branch name,
   **uppercased** (`lucas/tro-114-...` → `TRO-114`). If no match, stop and ask the user
   for the id.
3. **Fetch and sanity-check.** `get_issue(<ID>)`; confirm `gitBranchName` equals the
   current branch. If it doesn't, surface the mismatch and ask before continuing.
4. **Label guard.** The issue must carry the **Product Documentation** label. If it
   doesn't, stop and tell the user to either apply the label in Linear or switch to the
   right branch. Do not proceed without it.
5. **Project guard.** The issue must belong to a project — the project defines the feature
   being documented. If it has none, stop and ask the user which feature this documents.

## 2. Research the project

1. `list_issues(project: <the issue's project>)` — fetch everything.
2. **Filter to product-relevant issues.** Skip: archived issues, `Design:` sub-issues and
   design-labeled work, and process chores (docs entries, changelog entries, TDD/setup
   tasks). Keep feature work, improvements, and bug fixes that shaped user-visible
   behavior.
3. **Read the substantive descriptions in full** — `list_issues` truncates; call
   `get_issue` on anything cut off. Many descriptions are empty or just file embeds;
   that's expected — titles still map the feature surface.
4. Produce a **feature map**: what the feature does, its user-visible states and flows,
   and known fixed bugs (candidate troubleshooting symptoms — see step 4.4).

## 3. Verify in source

Issue descriptions are **leads, not facts**. Before documenting any behavior, confirm it
in `../trofiro-frontend` — CLAUDE.md's rule: document only shipped behavior verified in
source.

- Verbatim UI strings come from `../trofiro-frontend/apps/web/app/locales/en/` and
  `.../locales/pt-BR/` (`appointments.json`, `base.json`). Quote exactly, bold them, and
  verify the string renders as visible text.
- Icon-only controls and strings hardcoded outside the locale files are **described from
  source**, never quoted from memory.
- Behavior a Linear issue promises but the source doesn't show is **not documented**.

## 4. Write the docs

Every item below ships in **all languages** (`en/` and `pt-BR/`) with **identical
slugs** — never localize file names. Use local Write/Edit on the current branch (not the Mintlify MCP
session flow).

1. **Guide.** Create `en/guides/<feature>.mdx` + `pt-BR/guides/<feature>.mdx` — or, if the
   feature already has a guide, **update it in place** (never a duplicate page). Follow
   the page pattern: short `title`/`sidebarTitle`, one outcome-focused `description`
   containing "Trofiro", a subheader paragraph that doesn't repeat facts stated later.
2. **`docs.json`.** Add the page to the "Guides" group (en) and "Guias" group (pt-BR)
   under `navigation.languages`. Add a bare-path redirect entry
   (`/guides/<feature>` → `/en/guides/<feature>`), mirroring the existing redirects.
3. **FAQ** (`help/faq.mdx`, all languages) — only if the feature raises real questions:
   add `<Accordion>` entries to the matching application-area section, or a new `##`
   section if the feature opens a new area.
4. **Troubleshooting** (`help/troubleshooting.mdx`, all languages) — only if there are
   real symptoms: `##` area → `###` group → `####` symptom entries. Fixed bugs from step 2
   qualify **only if the symptom can still occur** for a user (device issues, permissions,
   network) — not if the fix eliminated it entirely.
5. **Introduction** (all languages). Add or adjust the guide's `<Card>` in the
   "Explore the docs" grid. If the feature opened a new sidebar area, update the
   one-sentence area sweep under the tagline.
6. **Quickstart** (all languages). Review only — touch it solely if the feature changes
   the first-run flow.
7. **Changelog** (all languages). Add an entry, newest first:
   `<Update label="<Month DD, YYYY>" tags={["<Feature>"]}>` with an `## <feature>`
   heading, one summary sentence, capability bullets, and a closing language-prefixed
   link to the guide. pt-BR mirrors the structure with a translated date
   ("18 de julho de 2026" style) and translated tags.

## 5. Verify

- `npx mint broken-links` — must pass.
- Slug parity: every touched `en/` file has its `pt-BR/` twin (and vice versa).
- Every internal link carries its language prefix (`/en/...`, `/pt-BR/...`) and never
  crosses languages.
- Terminology self-check against the CLAUDE.md tables, in all languages.

## 6. Stop — no commit, no PR, no Linear writes

Leave the changes **uncommitted** and print a summary of files created/changed per
language. The user reviews and ships via `/trofiro-pr` themselves. Linear is **read-only**
throughout — never `save_issue`, `save_comment`, or status changes.

## Gotchas

- **The label guard fires until the label exists.** "Product Documentation" must be
  created in Linear and applied to docs issues; until then every run stops at step 1.4 by
  design.
- **Idempotent** — a rerun updates the existing guide, FAQ entries, and changelog entry
  for the feature; it never adds duplicates.
- **Empty issue descriptions are normal** — titles map the surface, source provides the
  facts. Don't pad docs with guesses to compensate.
- **Never name internal technology** in prose: LiveKit, WorkOS, HMAC, environment
  variable names.
- **No images or screenshots** — none until real product assets exist; never fabricate.
- **Support address is per-language**: `support@trofiro.com` (en),
  `suporte@trofiro.com` (pt-BR).

## Reference: what "good" looks like

The Live meetings feature is the model output: `en/guides/live-meetings.mdx` and
`pt-BR/guides/live-meetings.mdx` (guide), the "Live meetings" sections in
`help/faq.mdx` and `help/troubleshooting.mdx`, the `<Card>` in every language's
introduction, and the July 18, 2026 `<Update>` entry in every language's changelog. A run
of this skill for a new feature should leave the repo looking like that feature had always
been documented to the same standard.
