> For Mintlify product knowledge (components, configuration, writing standards),
> install the Mintlify skill: `npx skills add https://mintlify.com/docs`

# Documentation project instructions

## About this project

- This is the product documentation site for **Trofiro**, a web platform for health practitioners (currently nutritionists), built on [Mintlify](https://mintlify.com)
- Pages are MDX files with YAML frontmatter
- Configuration lives in `docs.json`
- Use the Mintlify MCP server, `https://mcp.mintlify.com`, to edit content and settings via MCP
- Use the Mintlify docs MCP server, `https://www.mintlify.com/docs/mcp`, to query information about using Mintlify via MCP
- The application source of truth is the `trofiro-frontend` repo (sibling directory `../trofiro-frontend`). Verify behavior there before documenting it.
- After content changes, run `npx mint broken-links` to catch broken links and anchors.

## Terminology

- **practitioner** — the professional who uses Trofiro. Never "nutritionist", "doctor", "provider", or "host" in prose. Exception: verbatim UI quotes (for example the sidebar role label "**Nutritionist**" or the join screen's card).
- **patient** — the person the practitioner meets with. Never "client", "guest", "participant", or "user".
- **live meeting** — the feature name on first reference; "meeting" thereafter.
- **appointment** — the clinical session held inside a meeting, and the scheduled event and lobby context ("Appointment room"). The end-of-call screen is described as "a loading page", not quoted ("**Appointment ended**" never appears in docs).
- **call** — only when quoting UI text ("{name} joined the call", "**Your call has ended**") or in "the end-call button". The end-call button is a red icon-only phone button with no text label — never write "**End call**" as if it were a UI string.
- **invite link** — the URL a patient uses to join. Not "join link" or "meeting link".
- **meeting room** — the practitioner's room where meetings happen.
- The product tagline, used in the introduction's title area, is: "Trofiro is a platform for practitioners who love their patients."
- UI strings are canonical from `trofiro-frontend/apps/web/app/locales/en/` (`appointments.json`, `base.json`). Quote them exactly and bold them. Before quoting, verify the string exists as visible text — icon-only controls (like the end-call button) and hardcoded server messages are described, not quoted from memory.
- Docs are written in English. The product serves the Brazilian market, but no pt-BR appears in these docs.

## Style preferences

- Use active voice and second person ("you")
- Keep sentences concise — one idea per sentence
- Use sentence case for headings
- Bold for UI elements: Click **Settings**
- Code formatting for file names, commands, paths, and code references
- Describe transitions from the reader's point of view: "you're taken to your home page", not "Trofiro takes you to your home page"

## Page pattern

The docs follow a minimal style modeled on Linear's documentation. Every page has:

- `title` — short, usually identical to `sidebarTitle` ("Quickstart", "FAQ", "Live meetings"). No long SEO-style titles.
- `sidebarTitle` — short nav label
- `description` — one outcome-focused full sentence containing "Trofiro" ("Run your first live meeting with a patient in Trofiro.")
- A subheader: a short, engaging intro paragraph directly under the title that says what the page covers — and never repeats facts stated later on the same page

Help pages group content under platform-level sections so new areas can slot in later: the FAQ has "About Trofiro" then "Live meetings"; troubleshooting nests all current symptoms under a "Live meetings" section. The introduction opens with the tagline plus a one-sentence sweep of the sidebar areas, then a "Live meetings" section, then the "Explore the docs" card grid.

## Content boundaries

- Document only shipped behavior verified in the `trofiro-frontend` source.
- The sidebar areas (Appointments, Patients, Communication, Assistant, Finance, Research) may be named as areas of the platform — as the introduction does — but only areas with shipped behavior get documented pages or flows.
- Never name internal technology in docs prose: LiveKit, WorkOS, HMAC, environment variable names.
- No images or screenshots until real product assets exist. Never fabricate screenshots.
