# Procedure: how this tool grades a PR package

<!--
These are the operating steps the tool executes. They turn the rubric
(what to decide) and the evidence guide (where each fact lives) into a
repeatable grade: two executors following them reach the same verdict
on the same PR package. The load-bearing reads this week are
side-by-sides — the diff against the plan, the evidence against the
test plan, the description against the diff — so the read order puts
the thing a check is measured AGAINST into hand before the thing being
measured.
-->

## Read order

Read the package in this order, because each check is a side-by-side
and the baseline must be in hand before the candidate is judged
against it.

1. **The issue and its repro evidence first.** Note the one reported
   behavior, and — critically — the **observable the repro pins down**
   (the exact output, value, exit code, or symptom that distinguishes
   fixed from not-fixed). This is what the test evidence is later
   judged against.
2. **The repo-facts block second.** Note the **required PR-template
   sections** (issue link / `Closes #`, any checklist, any required
   changelog/whatsnew entry) and the **contribution / AI-use policy**
   (treat every candidate PR as AI-assisted). This is what
   standards-met reads against.
3. **The plan third.** Note its **scope pair** (in-scope / not-in-scope),
   its **named files**, its **test plan**, and any **Deviations** note.
   Write the one-line "what this PR is allowed to change, and what it
   must prove" before opening the diff, so the diff is judged against
   the plan and not the other way around.
4. **The candidate PR's diff and commits fourth.** Read what actually
   changed: each hunk's functional effect, and separately any
   non-functional content (debug lines, commented-out code, dead code,
   formatting churn, unrelated hunks).
5. **The description and title last.** Read them the way a maintainer
   reading the opened PR will: as claims to be checked against the diff
   (fidelity) and against the repo's asks (standards), and as the
   evidence block to be checked against the plan's test plan.

In live mode, read `scope.md` before any of the above (refuse a PR
targeting any repo but the scoped one; stop if its `Repo:` line is an
unfilled placeholder), and read `voice-guide.md` to hold the draft
title and description against the student's voice rules. In eval mode,
ignore both; the bundle is the whole world and nothing is fetched.

## Evidence gathering

For each check, pull its fact from exactly these places (see
`references/evidence-guide.md` for what good looks like in each):

- **plan-fidelity:** list every FUNCTIONAL change the diff makes (the
  files touched and what each hunk does), then set that list beside the
  plan's scope pair, named files, and Deviations note from read-order
  step 3. Record (a) any diff change that falls outside the plan's
  scope with no deviation note, (b) any part of the plan's change
  missing from the diff with no note, and (c) whether the description's
  statements about what changed are true to the diff. The check turns
  on whether the functional diff and the plan agree, with honest
  deviations re-tying any gap.
- **test-evidence:** take the observable noted in read-order step 1 and
  look for it in the PR's test-evidence section as a before→after on
  the changed path, and look for the repo's required checks/suite
  outcome. Record the specific artifact shown (or its absence) and
  whether it matches the plan's test plan.
- **diff-reviewable:** scan the diff and commit list for NON-FUNCTIONAL
  content only. Record any debug leftover, commented-out or
  "first-attempt" block, dead/unused helper, formatting-only churn, or
  unrelated functional hunk that buries the change. (A change that is
  out of plan scope is recorded for plan-fidelity, not here — though a
  hunk can be noted under both; the grades are made independently.)
- **standards-met:** take the required template sections and policy
  from read-order step 2 and check each against the description, title,
  and diff. Record each required section as present-with-content or
  ignored/boilerplate, whether a required changelog/whatsnew entry
  appears in the diff, and — treating the PR as AI-assisted — whether a
  required AI disclosure is present or absent.
- **claims-calibrated:** record any confidence word in the description
  ("fully tested", "guaranteed", "100%") that the diff and evidence do
  not back.

When a check's evidence is genuinely absent from the package, that
absence IS the finding: grade on what the absence means for the pass
condition (no before/after fails test-evidence; a required disclosure
simply not present fails standards-met). Do not hunt in other files or
invent the missing part.

## Check execution

1. Grade the four `required` checks first, in table order:
   plan-fidelity, test-evidence, diff-reviewable, standards-met. Then
   grade the `preferred` claims-calibrated check.
2. Grade each check only against the evidence named for it in the
   gathering step above; do not let a weakness found for one check
   bleed into another's grade. A PR can be a faithful match to the plan
   and still fail test-evidence; it can be decisively tested and clean
   and still fail standards-met; it can be perfectly scoped and fail
   only on debris. Keep the two orthogonality rules: plan-fidelity
   reads functional changes, diff-reviewable reads non-functional
   debris; description-vs-diff is plan-fidelity, template/policy-asks is
   standards-met.
3. For each check, name the single fact or quote that decides it. If
   the pass condition is met on that fact, grade `pass`; if it is
   violated, grade `fail`; if the package genuinely does not contain
   enough to decide, grade `unclear` (the verdict rule treats that as
   fail).
4. A check may be graded from its gathered evidence without re-reading
   the whole package, except plan-fidelity and standards-met, which are
   always read with both halves in hand (diff AND description for
   plan-fidelity; every required section AND the policy for
   standards-met) before grading.

## Verdict assembly

1. Apply the rubric's verdict rule: the verdict is `accept` if and only
   if all four `required` checks graded `pass`; otherwise `reject`.
2. The `preferred` check (claims-calibrated) is recorded with its grade
   and evidence but never changes the verdict.
3. Any `required` check graded `unclear` counts as a `fail` for the
   verdict.
4. In the output, quote the deciding fact for every check. When the
   verdict is `reject`, the **deciding check is the first failing
   required check in rubric table order** (plan-fidelity, then
   test-evidence, then diff-reviewable, then standards-met); make sure
   that check's evidence line names the exact drift, missing observable,
   debris, or ignored ask that sank it. Other failing checks keep their
   own evidence lines too.
5. The verdict is binary; emit it in the JSON block exactly as SKILL.md
   requires, as the last thing in the output.
