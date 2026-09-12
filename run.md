# Live demo commands

Run these off-stage before recording:

```
npm install -g @agentrhq/webcmd
webcmd skills add                       # choose your coding agent's harness
webcmd profile create jobsearch
```

Log into Internshala, Naukri, and (optionally) LinkedIn manually inside the
`jobsearch` profile once, so the session cookies are saved before the demo
starts. This keeps the recording focused on the agent, not you typing a
password on camera.

Add `profile.json` (from `profile.example.json`) with your real resume path
and search criteria. **Add `profile.json` and any resume/cover-letter files
to `.gitignore` before pushing this folder to your hackathon repo** — that
file holds personal data and should not be committed.

## During the recording

Open Claude Code or Codex in this project folder, load the `webcmd-browser`
skill for the live segment, and say:

```
Read and follow AGENT.md using my `jobsearch` webcmd profile.
```

Then narrate along as the agent works through Phase 0 → 1 → 2, and when it
hits the approval prompt in Phase 2 step 6, pause on it before typing
`approve` — that's the moment the judges are scoring under "human approval
step for ... submissions."

## Suggested narration beats

1. "Here are three internship portals I use." (show the shortlist output)
2. "Watch it read the listing and fill the form." (Phase 2, steps 1-5)
3. "It stops here — every time — before submit." (the approval prompt)
4. Type `approve`. Show the confirmation.
5. Second portal: point out it's faster/leaner because webcmd already learned
   the form layout from the first pass.
6. Trigger the recovery case (CAPTCHA or unexpected field) and show the agent
   flagging it instead of guessing or failing silently.
