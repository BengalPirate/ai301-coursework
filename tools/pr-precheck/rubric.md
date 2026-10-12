# Rubric: is this pull request ready to submit?

<!--
The checks below are what procedure.md executes, check by check,
against the evidence references/evidence-guide.md maps. They cover the
four failure families the lecture named and the eval set was built
around, under the harness's category names:

- plan-fidelity  -> silent-drift   (the diff does more or less than the
                                     plan with no deviation note, or the
                                     description claims a fidelity the
                                     diff contradicts)
- test-evidence  -> not-tested     (the evidence proves nothing
                                     observable, exercises the unchanged
                                     path, or the repo's own checks were
                                     never run)
- diff-reviewable -> unreviewable  (debris or unrelated hunks bury the
                                     change)
- standards-met  -> standards-wall (the repo's stated template sections
                                     or AI-disclosure policy are ignored)

A PR that clears all four required checks is a clear-accept. Every pass
condition judges the OUTCOME the PR delivers (does the diff fall inside
the plan? is the claimed behavior observable in the evidence? is the
diff free of debris? are the repo's stated asks met?), never the
write-up's shape (how long the description is, how many commits there
are). Honest-outcome rule, fourth week running: a PR that openly
discloses a shortfall — fewer changes than planned with a deviation
note, or a check that failed reported honestly with its reason — is
ready; a diff that quietly does more or less than the plan, or hides a
failing check, is not. The two orthogonality rules that keep two
graders agreeing: plan-fidelity reads only FUNCTIONAL/behavioral
changes against the plan's scope, while diff-reviewable reads only
NON-FUNCTIONAL debris (debug lines, dead code, commented-out blocks,
formatting churn); and whether the description's claims match the diff
is plan-fidelity, while whether the repo's template/policy asks are met
is standards-met.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-fidelity | The diff's functional changes (changed files and the behavior each hunk introduces), read against the plan's scope pair (in-scope / not-in-scope), its named Files, and any `## Deviations` note; AND the description's fidelity claims, read against those same diff contents. | Every functional change in the diff falls inside the plan's stated scope and named files, or is covered by a deviation note; the plan's whole change is present (nothing silently dropped); and the description describes what the diff actually does. Fails when the diff does MORE than the plan (a change the plan scoped out or never named, with no deviation note), does LESS with no note, or the description claims a fidelity the diff contradicts ("implements the plan exactly" / "no other changes" over a diff that carries an extra refactor or new option). An honest deviation note, or a description that states the gap plainly, re-ties the match. | required |
| test-evidence | The PR's test-evidence section, read against the plan's test plan and the reproduction's own steps, plus the stated outcome of the repo's own checks or suite. | The evidence shows the reproduced behavior's observable before→after — the specific artifact the plan's test plan named (the exact output, value, exit code, or symptom that flips when fixed) — exercised on the path the fix changed, AND the repo's required checks/suite outcome is visible. Fails when the evidence names no observable before/after ("tested locally, works now", "tests pass" with nothing named), proves only a path other than the reproduced one, or the repo's required checks were never run or shown. A check honestly reported as failing, with its reason, still counts as evidence; a hidden or vaguely-waved one does not. | required |
| diff-reviewable | The unified diff itself (and the commit list as a secondary signal). Reads only non-functional content — not whether the change matches the plan, which is plan-fidelity. | The diff contains only the change the plan describes plus its tests and required changelog/docs entry, with nothing non-functional riding along. Fails when debris buries the change: debug leftovers (`eprintln!`, `print`, `DBG`), commented-out code or kept "first attempt" blocks, dead/unused code (an unreferenced helper), pure formatting churn across untouched lines, or unrelated functional hunks folded in. The test is whether a reviewer can read the change without wading through noise. | required |
| standards-met | The description (and title), read against the repo-facts block's stated PR-template sections and contribution / AI-use policy. Treat every candidate PR as AI-assisted. | Every required template section named in repo-facts is present with real content — the issue link / `Closes #`, the required checklist, and any required changelog/whatsnew entry (present in the diff when the template demands one) — AND, where the repo's stated policy requires AI-use disclosure, the description discloses the tool and the extent of its use. Passes when the repo states no such asks, or states them and the PR meets them. Fails when a required template section is visibly ignored or left as boilerplate, a required entry the template demands is absent from the diff, or a required AI disclosure is missing. | required |
| claims-calibrated | The description's confidence words, read against the diff and the test evidence. | No load-bearing claim is stated with a confidence the evidence does not support ("fully tested", "guaranteed", "100% fixed", "verified on all platforms" without the runs to back it). Strengthens the PR; never changes the verdict. | preferred |

## Verdict rule

Accept if and only if every `required` check (plan-fidelity,
test-evidence, diff-reviewable, standards-met) is graded `pass`.
Any `required` check graded `fail` makes the verdict `reject`. A
`required` check graded `unclear` counts as a `fail`: a PR I cannot
verify from the package is a PR that is not ready to submit. The
`preferred` check (claims-calibrated) is recorded with its grade and
evidence but never changes the verdict. The verdict space is binary —
`accept` (ready to submit) or `reject` (hold).
