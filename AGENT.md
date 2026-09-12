# Agent workflow: Internship/Job Application Autopilot

Read this whole file before doing anything. Load the `webcmd-browser` skill
before starting Phase 2 — it is not needed for Phase 1.

Load `profile.json` in this folder (copy it from `profile.example.json` if it
doesn't exist yet and ask the human to fill it in before continuing).

## Hard rule — read this twice

**Never click, tap, or trigger a final "Submit," "Apply," "Send Application,"
or equivalent action on any portal without explicit human approval for that
specific listing, given after this run.** A prior approval for a different
listing does not count. If you are ever unsure whether a button is the final
submit action, treat it as if it is and stop.

## Phase 0 — Setup check

1. Confirm `profile.json` exists and has `search_criteria`, `applicant`, and
   at least one enabled portal.
2. For each enabled portal, confirm a webcmd session can be opened under the
   `webcmd_profile` named in `profile.json`:
   ```
   webcmd --profile <webcmd_profile> session create "job-autopilot" -f json
   ```
   Keep the returned session id for that portal for the rest of the run.
3. If login is not already active in that profile (page redirects to a login
   screen), stop and tell the human: "Not logged into <portal> under profile
   <webcmd_profile> — please log in manually, then re-run." Do not attempt to
   enter credentials yourself.

## Phase 1 — Search

For each enabled portal, in order:

1. Navigate to the portal's `base_url` using
   `webcmd --profile <p> --session <id> browser run --file ...` (inspect the
   page first; use the sitemap memory if webcmd already knows this portal's
   search flow, otherwise explore live and let webcmd learn it).
2. Enter `search_criteria.role_keywords` and `search_criteria.locations` into
   the portal's search/filter UI.
3. Collect the resulting listing URLs, titles, and any stipend/salary text
   shown on the results page.
4. Filter out listings that match `search_criteria.exclude_keywords`.
5. Cap the list at `run_limits.max_applications_per_portal` for this portal,
   and stop collecting across all portals once
   `run_limits.max_total_applications` is reached.
6. Print the shortlist to the human before opening any listing:
   ```
   Found N matching roles on <portal>:
   1. <title> — <company> — <location> — <url>
   ...
   ```

## Phase 2 — Fill (per listing, one at a time)

For each shortlisted listing:

1. Open the listing page. Read the full job description and note any
   portal-specific quirks (extra required fields, screening questions,
   assessments).
2. If the application form is new to webcmd for this portal, explore it live
   and let webcmd record the field layout for reuse on the next listing.
   If webcmd already has a learned path for this portal's form, use it and
   verify the fields still match before typing anything (sites drift).
3. Fill the form using `profile.json.applicant` data:
   - Name, email, phone, location → direct fill.
   - Resume upload → upload `applicant.resume_file`.
   - Cover letter / "why are you interested" free-text fields → draft a short
     answer grounded in the actual job description you just read, referencing
     the role and company by name. Keep it under 150 words. Do not invent
     experience, skills, or credentials not present in the profile or resume.
   - Any screening question you cannot answer confidently from `profile.json`
     (e.g. "expected salary," "visa sponsorship needed," an unfamiliar
     required field, or a CAPTCHA) → **do not guess.** Stop this listing and
     report it to the human as needing manual input instead of filling it in.
4. **Do not click anything that submits, sends, or finalizes the
   application.** Fill only.
5. Take a screenshot or extract a text summary of the completed, unsubmitted
   form and show it to the human:
   ```
   Ready to submit — <title> at <company> (<portal>)
   Fields filled: name, email, phone, resume, cover letter (see below)
   Cover letter draft:
   "<the drafted text>"
   Unanswered/flagged fields: <none, or list>

   Reply with:
     approve  → submit this application now
     edit     → tell me what to change, I will update and show you again
     skip     → leave this one unsubmitted and move to the next listing
   ```
6. **Wait for the human's reply. Do not proceed on your own.**

## Phase 3 — Submit (only after explicit "approve")

1. Re-verify you are still looking at the same listing/form you showed the
   human (pages can time out or redirect) before clicking submit.
2. Click the final submit action for that listing only.
3. Confirm success (look for a confirmation message/page) and report it:
   `Submitted: <title> at <company> — confirmation: <text or "none shown">`.
4. If the submit click fails, errors out, or shows a CAPTCHA at this stage,
   stop and report the failure — do not retry blindly, and do not attempt to
   solve a CAPTCHA yourself.
5. Move to the next approved/pending listing. Repeat Phase 2 → Phase 3 for
   each one individually — one approval covers exactly one listing.

## End of run

Print a final summary: portals visited, listings found, listings submitted
(with confirmations), listings skipped, and any that were flagged for manual
follow-up (unanswered screening questions, CAPTCHAs, login issues).

## Demo notes (for the recording)

- Run one portal end-to-end first (a "cold" portal webcmd hasn't learned yet)
  so the exploration + learning is visible.
- Run a second portal after it, ideally showing webcmd reusing a previously
  learned path faster than the first pass, if you've primed it earlier.
- Trigger one deliberate recovery moment — an unfamiliar required field or a
  CAPTCHA on a test listing — so the agent's "stop and flag instead of
  guessing" behavior is on camera, not just claimed in the pitch.
- Let the approval prompt sit on screen for a beat before typing `approve` —
  that pause is the point.
