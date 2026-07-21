# Trofiro documentation

Product documentation for Trofiro, a web application for practitioners who love their patients. The docs are built with [Mintlify](https://mintlify.com) and written in two languages: English and Brazilian Portuguese.

## What's documented

The docs currently cover **live meetings** — secure video appointments with patients, straight from the browser, with no downloads and no patient account required. Practitioners start a meeting from their meeting room, invite a patient with an invite link, share their screen, chat, keep the patient chart open beside the call, and use audio-only mode for in-person appointments.

Content is organized into:

- **Get started** — introduction and quickstart
- **Guides** — live meetings
- **Help** — FAQ and troubleshooting
- **Changelog** — feature announcements

Other areas of the application (Appointments, Patients, Communication, Assistant, Finance, Research) get documented pages as they ship.

## Structure

```
en/          English content (default language)
pt-BR/       Brazilian Portuguese content
docs.json    Site config and per-language navigation
CLAUDE.md    Writing conventions, terminology, and content rules
```

Two rules hold everything together:

- File slugs are identical across languages (`en/quickstart.mdx` ↔ `pt-BR/quickstart.mdx`) — the language switcher depends on it, so file names are never localized.
- Every content change ships in both languages in the same change.

## Local development

Install the [Mintlify CLI](https://www.npmjs.com/package/mint) and run the dev server at the repo root:

```bash
npm i -g mint
mint dev
```

Preview at `http://localhost:3000`. After content changes, check for broken links and anchors:

```bash
npx mint broken-links
```

If the dev server misbehaves, run `mint update` to get the latest CLI.

## Writing conventions

[CLAUDE.md](CLAUDE.md) is the source of truth for terminology (practitioner, patient, live meeting, invite link, and their pt-BR equivalents), style, page patterns, and content boundaries. Only shipped behavior gets documented — everything is verified against the application source in the sibling `trofiro-frontend` repo, where the locale files are also the canonical source for quoted UI strings.

## Workflow

Two Claude Code skills drive the day-to-day work:

- `trofiro-docs` — generates the full documentation package for the feature on the current branch (guide, FAQ and troubleshooting sections, changelog entry, in both languages), researched from its Linear issue and verified against `trofiro-frontend`.
- `trofiro-pr` — pushes the branch and opens a draft PR titled `[<Linear issue id>] <issue title>`.

## Publishing

Changes deploy automatically through the Mintlify GitHub app after merging to `main`.
