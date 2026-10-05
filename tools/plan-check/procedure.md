# Procedure: how this skill grades a plan package

<!--
These are the operating steps the skill executes. They turn the rubric
(what to decide) and the evidence guide (where each fact lives) into a
repeatable grade: two executors following them reach the same verdict
on the same package.
-->

## Read order

Read the package in this order, because a plan can only be graded
against the evidence it is supposed to follow from, so that evidence
must be in hand first.

1. **Repro evidence block first.** This is the ground truth the plan
   must explain. Note down: the behavior reproduced (the failure and
   its artifact), and — critically — any **control run, isolating
   step, or debug output**, because that is what a wrong-cause plan
   contradicts. Write the one-line "what the evidence points at" before
   reading the plan, so the diagnosis is judged against the evidence,
   not the other way around.
2. **Issue + thread highlights second.** Note the reported behavior and
   any **explicit maintainer direction** in the thread: a maintainer's
   stated cause, a chosen approach, or an approach they already
   rejected. This is what the comment check reads against.
3. **Repo-facts block third.** Note the contribution policy and
   especially any **AI-use disclosure requirement** (treat every
   candidate plan as AI-assisted), and the bug-report/PR template asks.
4. **Candidate plan fourth.** Read its cause, scope (in/out), files,
   approach, test plan, and risks.
5. **Candidate plan comment last.** Read it the way a maintainer on the
   thread will: as the words that go public, judged against the thread
   direction and the repo policy already noted in steps 2–3.

In live mode, read `scope.md` before any of the above (refuse an issue
outside the scoped repo; stop if its repo line is an unfilled
placeholder), and read `voice-guide.md` to check the draft comment's
voice. In eval mode, ignore both; the bundle is the whole world and no
fetching is done.

## Evidence gathering

For each check, pull its fact from exactly these places (see
`references/evidence-guide.md` for what good looks like in each):

- **diagnosis-grounded:** the plan's Diagnosis/Cause section, set beside
  the repro block's control/isolating/debug lines noted in read-order
  step 1. Record the cause the plan names and what the control points
  at; the check is whether they agree.
- **scope-bounded:** the plan's in-scope and not-in-scope statements and
  its approach list. Record every distinct change the plan commits to,
  then compare that set to the single reproduced behavior.
- **executable:** the plan's Files/Change/Approach section. Record
  whether a concrete file/area AND a concrete approach or first step are
  named, or whether the language is investigation ("look into",
  "profile", "not sure which layer").
- **test-plan-observable:** the plan's Test section against the repro's
  steps. Record the observable the test names (the specific post-fix
  result) and whether it maps to a repro step.
- **comment-engages-thread-and-policy:** the plan comment against (a) the
  thread direction noted in read-order step 2 and (b) the AI/contribution
  policy noted in step 3. Record whether the comment engages or ignores
  the direction, and whether a required disclosure is present or absent.
- **honest-unknowns:** the plan's Risks/Unknowns against its own
  confident claims. Record any uncertainty stated as fact.

When a check's evidence is genuinely absent from the package, that
absence IS the finding: grade on what the absence means for the pass
condition (a plan with no stated files is not executable; a comment
that says nothing where the policy requires disclosure fails the comment
check). Do not hunt in other files or invent the missing part.

## Check execution

1. Grade the five `required` checks first, in table order:
   diagnosis-grounded, scope-bounded, executable, test-plan-observable,
   comment-engages-thread-and-policy. Then grade the `preferred`
   honest-unknowns check.
2. Grade each check only against the evidence named for it in the
   gathering step above; do not let a weakness found for one check bleed
   into another's grade. A plan can have a sound diagnosis and still
   fail scope, or be perfectly bounded and fail only on the comment.
3. For each check, name the single fact or quote that decides it. If the
   pass condition is met on that fact, grade `pass`; if it is violated,
   grade `fail`; if the package genuinely does not contain enough to
   decide, grade `unclear` (the verdict rule treats that as fail).
4. A check may be graded from the gathered evidence without re-reading
   the whole package, except the comment check, which is always read
   against the thread and policy notes together (both halves) before
   grading.

## Verdict assembly

1. Apply the rubric's verdict rule: the verdict is `accept` if and only
   if every `required` check graded `pass`; otherwise `reject`.
2. `preferred` checks (honest-unknowns) are recorded with their grade
   and evidence but never change the verdict.
3. Any `required` check graded `unclear` counts as a `fail` for the
   verdict.
4. In the output, quote the deciding fact for every check. When the
   verdict is `reject`, the deciding check(s) are the required ones that
   failed; make sure each failed check's evidence line names the exact
   contradiction, missing piece, or ignored direction that sank it.
5. The verdict is binary; emit it in the JSON block exactly as the
   skill's output format requires, as the last thing in the output.
