> For Mintlify product knowledge (components, configuration, writing standards),
> install the Mintlify skill: `npx skills add https://mintlify.com/docs`

# Documentation project instructions

## About this project

- This is the product documentation site for **Trofiro**, a web application for health practitioners (currently nutritionists), built on [Mintlify](https://mintlify.com)
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
- **application** — how Trofiro is described in prose ("a web application for health practitioners"). Never "platform". Exception: verbatim UI quotes (the landing page reads "Plataforma para Nutricionistas"). pt-BR: **aplicativo**. The desktop-vs-mobile sense of "platform" (as in the "Platforms" table column) is unrelated and stays.
- The product tagline, which opens the introduction's subheader paragraph, is: "Trofiro is an application for practitioners who love their patients."
- UI strings are canonical from `trofiro-frontend/apps/web/app/locales/en/` (`appointments.json`, `base.json`). Quote them exactly and bold them. Before quoting, verify the string exists as visible text — icon-only controls (like the end-call button) and strings hardcoded outside the locale files are described or quoted from source, not from memory.

## Languages

- The docs are bilingual: English (default) lives in `en/`, Brazilian Portuguese in `pt-BR/`. File slugs are identical across languages (`en/quickstart.mdx` ↔ `pt-BR/quickstart.mdx`) — the language switcher depends on it, so never localize file names.
- Every content change ships in both languages in the same change.
- Internal links always carry the language prefix (`/en/...`, `/pt-BR/...`) and never cross languages.
- Navigation lives in `docs.json` under `navigation.languages`; each language has its own group names and navbar.
- pt-BR UI quotes are canonical from `trofiro-frontend/apps/web/app/locales/pt-BR/` (`appointments.json`, `base.json`) — same rule as English, from the pt-BR locale files.
- The pt-BR tagline is: "A Trofiro é um aplicativo para profissionais que amam seus pacientes." The brand takes the feminine article: "a Trofiro", "na Trofiro" (per frontend usage, e.g. "Saiba mais sobre a Trofiro").
- pt-BR page descriptions contain "Trofiro", like English ones.
- The support address is per-language: `support@trofiro.com` in English docs, `suporte@trofiro.com` in pt-BR docs (navbar and mailto links).

### pt-BR terminology

| English canonical | pt-BR canonical |
| --- | --- |
| practitioner | **profissional** — never "nutricionista" in prose; verbatim UI quotes excepted ("**Nutricionista**") |
| patient | **paciente** |
| live meeting | **atendimento ao vivo** on first reference; "atendimento" thereafter |
| appointment | **consulta** (matches UI "Consulta presencial") |
| call | **chamada** — only in UI quotes ("{name} entrou na chamada", "**Sua chamada foi encerrada**") or "o botão de encerrar a chamada" |
| invite link | **link de convite** |
| meeting room | **sala de atendimento** |
| patient chart | **prontuário** in prose; "**Prontuário clínico**" as UI quote |

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

Help pages group content under application-level sections so new areas can slot in later: the FAQ has "About Trofiro" then "Live meetings"; troubleshooting nests all current symptoms under a "Live meetings" section. The introduction opens with the tagline plus a one-sentence sweep of the sidebar areas, then a "Live meetings" section, then the "Explore the docs" card grid.

## Content boundaries

- Document only shipped behavior verified in the `trofiro-frontend` source.
- The sidebar areas (Appointments, Patients, Communication, Assistant, Finance, Research) may be named as areas of the application — as the introduction does — but only areas with shipped behavior get documented pages or flows.
- Never name internal technology in docs prose: LiveKit, WorkOS, HMAC, environment variable names.
- No images or screenshots until real product assets exist. Never fabricate screenshots.
