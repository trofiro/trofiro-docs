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

## Terminology

- **practitioner** — the professional who uses Trofiro. Never "nutritionist", "doctor", "provider", or "host" in prose. Exception: verbatim UI quotes (for example the sidebar role label "**Nutritionist**" or the join screen's card).
- **patient** — the person the practitioner meets with. Never "client", "guest", "participant", or "user".
- **live meeting** — the feature name on first reference; "meeting" thereafter.
- **consultation** — the clinical session held inside a meeting. Also used when echoing product copy ("**Consultation ended**").
- **call** — only when quoting UI text ("**End call**", "{name} joined the call").
- **appointment** — the scheduled event and lobby context ("Appointment room"). Not a synonym for the meeting itself.
- **invite link** — the URL a patient uses to join. Not "join link" or "meeting link".
- **meeting room** — the practitioner's room where meetings happen.
- UI strings are canonical from `trofiro-frontend/apps/web/app/locales/en/` (`appointments.json`, `base.json`). Quote them exactly and bold them.
- Docs are written in English. The product serves the Brazilian market, but no pt-BR appears in these docs.

## Style preferences

- Use active voice and second person ("you")
- Keep sentences concise — one idea per sentence
- Use sentence case for headings
- Bold for UI elements: Click **Settings**
- Code formatting for file names, commands, paths, and code references
- Frontmatter on every page: `title` (long, SEO-friendly), `sidebarTitle` (short nav label), `description` (full sentence containing "Trofiro")

## Content boundaries

- Document only shipped behavior verified in the `trofiro-frontend` source.
- Never document as available: waiting-room admission, recording, background blur, reactions, hand raising, whiteboard, file sharing, in-meeting notes, transcription or captions, a participant roster panel, muting or removing other participants, locking a meeting, layout switching, a fullscreen toggle, reconnection handling, participant limits, scheduling or calendar features, public booking, the Patients/Communication/Assistant/Finance/Research modules, or any public API.
- Do not describe an admit/deny lobby flow. The join screen's "your host will admit you" note is aspirational copy — patients with a valid link join directly.
- Never promise outputs of post-call "processing" (no recordings, transcripts, or summaries exist).
- Never name internal technology in docs prose: LiveKit, WorkOS, HMAC, environment variable names.
- Do not state a number for invite link expiry — it is deployment-configured. Say links "expire after a set period".
- No images or screenshots until real product assets exist. Never fabricate screenshots.
- No roadmap content, timelines, or "coming soon" promises.
