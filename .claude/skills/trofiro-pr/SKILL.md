---
name: trofiro-pr
description: >-
  Push the current branch to GitHub and open (or update) a pull request for it against the repo's
  default branch, with the title `[<Linear issue id>] <Linear issue title>` and a body that is only a concise
  summary of the technical code changes. Use when the user asks to "open/create a PR", "raise a
  pull request", "push this branch as a PR", "trofiro-pr", or "PR this branch". Targets the
  repository the current directory belongs to (resolved from its git remote). Reads the Linear
  issue (title only) — never changes it.
---

# trofiro-pr

Push the current feature branch and open a pull request against the repo's default branch in a **strict, minimal
format**. The PR **title** is the Linear issue id in brackets followed by the issue title; the PR
**body** is nothing but a concise summary of the technical code changes.

## When to use

After committing work on a `TRO-*` feature branch, when the user wants that branch pushed and a PR
opened (or an existing PR for the branch updated). Requires the **`gh` CLI** (authed for an
account with access to the repo's git remote) and the **Linear MCP**.

## 0. Prerequisites (verify first)

- **Resolve the target repository and default branch from the `origin` remote.** Run
  `gh repo view "$(git remote get-url origin)" --json nameWithOwner,defaultBranchRef` — resolve
  from the `origin` URL **explicitly**, because no-argument `gh` commands follow the
  `gh`-configured default repo (`gh repo set-default`), which can differ from `origin`. This
  yields the repo `<owner>/<repo>` and the default branch `<base>` (usually `main`). Use both
  everywhere below — pass `--repo <owner>/<repo>` to **every** `gh pr` command, and use `<base>`
  for guardrails, diffs, and the PR base. On failure:
  - **No git remote configured** → stop and tell the user to add one.
  - ***"Could not resolve to a Repository"*** → `gh` is authed to an account without access to
    that remote (e.g. a personal/other-org login) — stop and tell the user to
    `gh auth switch` / `gh auth login` to the right account, then retry. (Git push authenticates
    via the remote URL — e.g. an SSH host alias — independently of the `gh` account, so a push
    can succeed while `gh` API calls fail — always verify `gh` access, don't assume.)
- **Linear MCP** available (`get_issue`).
- Run from the repo root.

## 1. Guardrails (stop early if these fail)

1. **Not on the default branch.** `git branch --show-current` must not be `<base>` (the default
   branch resolved in step 0). If it is, stop — the user needs a feature branch.
2. **Committed work only.** `git status --porcelain` (ignore untracked `.claude/`). If there are
   uncommitted tracked changes, **stop and ask the user to commit first** — do **not** auto-commit,
   and do not include them.

## 2. Resolve the Linear issue → PR title

1. Read the branch name (`git branch --show-current`).
2. Extract the issue id: the **first** `([A-Za-z]{2,}-[0-9]+)` match in the branch, **uppercased**.
   `lucas/tro-47-live-patient-section` → `TRO-47`. If no match, stop and ask the user for the id.
3. `get_issue(<ID>)` → `title` (and sanity-check `gitBranchName` equals the current branch).
4. **PR title** = `[<ID>] <title>` — e.g. `[TRO-47] Live Patient Section`.
   - Bracketed id so automation can parse it back out.
   - **No** `feat:`/conventional-commit prefix, no extra decoration.

## 3. Build the PR body — a technical-changes summary only

Gather the branch diff vs the default branch (`<base>` from step 0):
`git log origin/<base>..HEAD --oneline`, `git diff --stat origin/<base>...HEAD`, and
`git diff origin/<base>...HEAD` (read the actual changes, not just the stat).

Write a **concise, factual** summary of the **technical code changes**, grouped by feature/area as
a short bullet list (or tight prose). Then, **strictly**:

- **Exclude test changes** — tests are assumed to have been done to support the code; never mention
  them.
- **No other sections** — no Tests, Screenshots, Notes, Checklist, "How to test", etc.
- **No "why" and no resource links** — the rationale and any resources live in the Linear issue
  description, not the PR.
- **No footer** — do **not** append the "Generated with Claude Code" line or any attribution.
- Summarize; do not paste a file-by-file dump.

Write the body to a scratchpad file (e.g. `<scratchpad>/pr-body.md`) and pass it with
`--body-file` so newlines survive shell-escaping.

## 4. Push the branch

`git push -u origin HEAD` (first push sets upstream; push auth follows the remote URL and is
independent of the `gh` account).

## 5. Create or update the PR (idempotent)

Check for an existing PR on this branch first:
`gh pr view --repo <owner>/<repo> --json number,url,state 2>/dev/null` (or
`gh pr list --repo <owner>/<repo> --head "$(git branch --show-current)" --json number,url`).

- **No PR yet** → create it **as a draft** (`--draft`):
  ```
  gh pr create --repo <owner>/<repo> --draft --base <base> --head "<branch>" --title "[<ID>] <title>" --body-file <scratchpad>/pr-body.md
  ```
  Always pass `--repo <owner>/<repo>` and `--base <base>` (the values resolved in step 0) — never
  rely on `gh` auto-detection. The PR opens as a draft on purpose — the author marks it **Ready for
  review** when it's ready, and that draft→ready event is what triggers cubic's review and Linear's
  move to **In Review**.
- **PR already exists** → update it in place (never open a duplicate):
  ```
  gh pr edit <number> --repo <owner>/<repo> --title "[<ID>] <title>" --body-file <scratchpad>/pr-body.md
  ```
  `gh pr edit` does not change draft state — don't flip an existing PR's draft/ready status.

Print the resulting PR URL to the user.

## 6. Do NOT touch the Linear issue

Linear is used **read-only** (`get_issue`, for the title). This skill must **never** write to
Linear — do not change the issue **status**, assignee, description, or post comments/attachments
(no `save_issue` / `save_comment` / status transitions). Linear auto-associates the PR with the
issue via the branch name and the `[TRO-…]` title prefix, so no Linear write is needed.

## Gotchas

- **Wrong `gh` account → "Could not resolve to a Repository".** A push can succeed (remote-URL
  auth) while `gh` API calls fail (a `gh` account without access to the remote's repo). Always
  verify with `gh repo view` in step 0; remediate with `gh auth switch`/`gh auth login`.
- **`gh` default repo ≠ `origin`.** `gh repo set-default` can point no-argument `gh` commands at a
  different repository than the one being pushed to — resolve the repo from the `origin` URL in
  step 0 and pass `--repo <owner>/<repo>` to every `gh pr` command.
- **Default branch may not be `main`** — use `<base>` (`defaultBranchRef.name` from step 0) for the
  guardrail, the diffs, and `--base`; never hardcode `main`.
- **Branch → id parsing**: first `[A-Za-z]{2,}-[0-9]+` match, uppercased.
- **Format is the whole point** — title `[<ID>] <title>`; body = technical-changes summary only, no
  tests / sections / footer / rationale.
- **Idempotent** — update the branch's existing PR instead of creating a second one.
- **New PRs open as draft** (`--draft`); the author marks Ready for review to fire cubic's review
  and Linear's In Review transition. Don't flip an existing PR's draft state on update.
- **Refuse on the default branch**, push committed work only, and leave Linear untouched.

## Reference: what "good" looks like

Branch `lucas/tro-47-live-patient-section` →
- **Title:** `[TRO-47] Live Patient Section`
- **Body:**
  ```
  - Add the practitioner-only Patient chart panel to the live screen (right sidebar on desktop,
    overlay card on mobile), gated by `showPatientPanel`.
  - Add `patient/` components: panel shell with section state, header, section selector, and the
    Recommendations empty state.
  - Move the in-call controls into a dedicated `control-bar/` folder and wire the patient toggle.
  ```
  (No tests mentioned, no extra sections, no footer.)
