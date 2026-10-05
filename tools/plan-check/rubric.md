# Rubric: is this plan ready to post and build from?

<!--
The checks below are what the procedure executes, check by check,
against the evidence the guide in references/evidence-guide.md maps.
They cover the failure families the lecture named and the eval set was
built around: the diagnosis contradicts or ignores the reproduced
evidence (wrong-cause), the change is unbounded (scope-creep), a
stranger could not start executing it or the test plan proves nothing
observable (unbuildable), and the comment ignores explicit maintainer
direction in the thread or omits disclosure the repo's AI policy
requires (thread-convention). A plan that clears all of these is a
clear-accept.

Every pass condition judges the OUTCOME the plan commits to, never the
write-up's shape: a terse complete plan can be ready (calib-01: three
short sections, grounded, one named file, a decisive test) and a long
confident one can be a wrong cause or a redesign (a polished plan that
contradicts its own control run is still a reject).
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis-grounded | The plan's stated cause (its Diagnosis/Cause section), read against what the repro evidence pins down — especially any control run, isolating step, or debug output in the repro-evidence block. | The stated cause is consistent with the behavior the repro evidence shows and explains it; where the repro includes a control/isolating run or debug output, the cause agrees with what that run points to. Fails when the diagnosis contradicts or ignores that evidence — e.g. it blames a component the repro's own control or debug run rules out, or dismisses as a "red herring" the exact signal the repro isolated. | required |
| scope-bounded | The plan's scope statement (in-scope / not-in-scope) and approach, read against the one behavior the issue and repro identify. | The plan is one coherent change that closes the reproduced issue, and what it touches stays confined to that. Fails when it bundles work beyond the issue — a drive-by refactor, a dependency migration, a redesign, or a multi-front campaign — whether or not it is labelled "while I'm here" or wrapped around the real fix. | required |
| executable | The plan's files/areas and approach (its Files / Change / Approach section). | A stranger could start without asking the author anything: the plan names the file(s) or a specific area to change and a concrete approach or first step. Fails when the plan is an intention to investigate rather than a change — no chosen file, layer, or approach (e.g. "profile and optimize", "poke around the editor", "gocui? tcell? not sure", "upstream or vendored, whichever is easier"). | required |
| test-plan-observable | The plan's test plan, read against the repro evidence's steps and artifacts. | The test plan names an observable outcome that distinguishes fixed from not-fixed — typically the repro's own steps re-run, with the specific post-fix result stated. Fails when it names no observable outcome ("make sure it's faster", "verify it works") or cannot be tied back to the reproduced behavior. | required |
| comment-engages-thread-and-policy | The candidate plan comment, read against the thread highlights (explicit maintainer direction) and the repo-facts block's contribution and AI policy. Treat every candidate plan as AI-assisted work. | The comment is consistent with explicit maintainer direction present in the thread — it does not ignore or contradict a maintainer's stated cause, chosen approach, or an approach the maintainer already rejected — AND it discloses AI assistance (naming the tool and the extent) when the repo's stated policy requires disclosure. If the thread carries no explicit maintainer direction, that half passes; if the repo's policy requires no disclosure for issue comments, that half passes. Fails when the comment ignores or contradicts explicit maintainer direction, or omits a disclosure the repo's AI policy requires. | required |
| honest-unknowns | The plan's risks/unknowns, read against its own confidence and the repro evidence. | Genuine uncertainties are stated as open questions or flagged trade-offs rather than asserted away, and no load-bearing claim is stated with a confidence the repro evidence does not support. Strengthens the plan; never changes the verdict. | preferred |

## Verdict rule

Accept if and only if every `required` check passes. `preferred` checks
never change the verdict. A required check graded `unclear` counts as a
fail: a plan I cannot verify from the package is a plan that is not ready
to build from. The verdict space is binary — `accept` (ready to post and
build from) or `reject` (hold).
