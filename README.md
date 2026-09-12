# Internship/Job Application Autopilot
Built on [webcmd](https://github.com/agentrhq/webcmd) — self-learning browser infra for AI agents.

## What it does

One agent, many job portals, one resume.

1. Reads your profile/resume criteria once from `profile.json`.
2. Logs into job platforms (Internshala, LinkedIn Jobs, Naukri, ...) using your
   saved webcmd browser profile (cookie jar) for each site.
3. Searches for roles matching your criteria.
4. Opens each matching listing and auto-fills the application form.
5. **Stops right before the final "Submit Application" click** and asks you
   for explicit approval. Nothing is ever submitted without a human saying yes.
6. On the first run for a new portal, webcmd learns that portal's form layout
   (field names, upload flow, quirks). On the second portal it reuses what it
   already learned, which is what you show in the demo.

## Why webcmd specifically

Webcmd's live browser control (`webcmd browser ...`) handles the actual
clicking/typing/uploading, and its sitemap memory layer means the agent isn't
re-discovering "where's the resume upload field on Naukri" from scratch every
run. That's the "technical depth & recovery" story for the judges: show a
learn-then-reuse pass across two different portals, plus one deliberate
recovery moment (CAPTCHA or unexpected field → agent pauses and asks instead
of failing silently).

## Project layout

```
job-application-autopilot/
├── README.md              this file — setup + demo script
├── AGENT.md                the actual agent prompt/workflow (the "code")
├── profile.example.json    your resume + search criteria, copy to profile.json
├── .env.example             environment variables template
└── scripts/
    └── run.md               exact commands to run live during the demo
```

## Setup

1. Install webcmd and the browser skill (one-time):
   ```
   npm install -g @agentrhq/webcmd
   webcmd skills add
   ```
   When prompted, pick your harness (Claude Code, Codex, etc.) and only load
   the `webcmd-browser` skill for the live portion of the demo.

2. Create a dedicated webcmd profile per portal so sessions/cookies don't mix:
   ```
   webcmd profile create jobsearch
   ```
   Log into Internshala / LinkedIn / Naukri once inside that profile so the
   session is saved (do this before the demo, off-stage).

3. Copy `profile.example.json` to `profile.json` and fill in your real resume
   data and search criteria (role keywords, location, experience level,
   resume file path). **This file holds personal data — keep it out of git**
   (see `.gitignore` note in `scripts/run.md`).

4. Copy `.env.example` to `.env` and fill in any portal-specific values you
   need (none are required to just run the demo).

5. Open your coding agent (Claude Code / Codex) in this folder, tag/load the
   `webcmd-browser` skill, and paste the contents of `AGENT.md` as your
   instruction — or literally say:
   ```
   Read and follow AGENT.md using my `jobsearch` webcmd profile.
   ```

## The hard rule, made visible

The approval gate is not a suggestion in a comment — it's structural: the
agent workflow in `AGENT.md` explicitly separates "fill form" from "submit
form" into two distinct steps, and step 2 is only ever taken after the agent
prints the filled-form summary and the human types a literal `approve` (or
`skip` / `edit`). This is what you point to on stage when you say "this is
the hard rule, not a bug."
