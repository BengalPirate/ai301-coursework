# Rubric: is this reproduction package ready to post?

<!--
The checks below are what I execute, check by check, against the
evidence the guide in references/evidence-guide.md maps. They cover the
five proof families the lecture named: the environment is recorded, the
steps are followable, the behavior shown is the issue's (not an
adjacent one), the outcome is stated honestly (an evidenced
cannot-reproduce is a pass; a confident wrong-target is not), and the
words respect the repo's conventions (including any AI-use policy).

Every pass condition judges the OUTCOME the artifact shows, never the
write-up's shape: a terse complete report can be ready and a long
confident one can be empty. (Worksheet calibration from the activity:
calib-01 is a terse-but-complete accept, so brevity never fails a
check; calib-03 is a long, beautifully formatted report whose artifact
is the WRONG error, so formatting never passes one.)
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| claim-names-issue | The candidate claim comment, read against the issue's title and body. | The claim refers to this issue's specific reported behavior or target (not a generic "+1" / "me too" / "same here") and names a concrete next investigation step the author will take. | required |
| claim-promises-only-investigation | The candidate claim comment. | The claim promises only to investigate and report back. It does NOT assert a completed fix, guarantee a fix, or commit to a delivery date. | required |
| environment-recorded | The repro report's environment record, read against the issue's stated target version/platform and the repo-facts bug-report template. | An environment is recorded with enough detail to place the attempt (at minimum the tool version plus OS/platform, and the install method when the issue's behavior can depend on it), AND the version tested matches the issue's target (or the current release), OR any deviation from the issue's target is explicitly called out in the report. | required |
| steps-rerunnable | The repro report's steps and inputs, read together with the issue it cites. | A stranger could re-run the attempt and reach the same artifact from this report plus the issue it cites: the triggering command/invocation is shown, and every input that determines the claimed behavior is either pasted in the report or fully specified in the referenced issue (input strings, offsets, flags, options). No determining input depends on a private or unshared repo, config, or file, and no step is "set up the project" hand-waving. An input the report leaves unspecified fails this check only when it could change the claimed behavior; an unspecified detail that cannot affect the outcome (e.g. the exact contents of a dependency list when the bug is message routing) does not. Fails when the trigger is swapped for an adjacent one, the commands/inputs are absent, a determining flag or input is omitted, or the input is a private artifact a stranger cannot obtain. For a report that honestly concludes it could NOT reproduce, this check judges whether the attempt actually made is re-runnable — the command and inputs that were run are shown and concrete — not whether that attempt reached the triggering input the author could not find; a triggering variant the author describes but did not run is a stated limit of the attempt (its genuineness guarded by `artifact-shows-claimed-behavior` and `claims-within-evidence`), not an unfollowable step. | required |
| artifact-shows-claimed-behavior | The repro report's artifacts (output excerpts, logs, screenshots) read against the issue's described behavior AND the report's own stated outcome. | The shown artifact is real evidence for the outcome the report claims. A report claiming reproduction shows the issue's specific behavior — the same failure mode, error text, and/or exit status, not an adjacent one. A report claiming it cannot reproduce shows a genuine attempt whose artifact backs the non-reproduction. Fails when the artifact shows a different or adjacent behavior narrated as the issue's, or shows nothing of the claimed behavior. | required |
| claims-within-evidence | The assertions in the report and claim comment, read against what the artifacts actually show. | Every assertion the verdict rests on — the reproduction (or non-reproduction) outcome and the primary artifact behind it — is backed by shown evidence: no "confirmed" / "guaranteed reproducible" / "conclusively" / "I verified" about that core outcome that the artifacts do not support. An inability to reproduce stated honestly and with its attempt shown passes; a confident claim resting on a wrong or absent primary artifact fails. An unsupported remark about a preferred-only contrast/control run (e.g. asserting the expected-correct case without showing its output) does NOT by itself fail this check — that gap is registered, without penalty, by `control-run-isolates-trigger` — as long as the load-bearing reproduction claim is itself shown and honest. | required |
| respects-repo-conventions | The candidate comments read against the repo-facts contribution and AI policy. Treat every candidate package as AI-assisted work. | The comments comply with the repo's stated policy. If the policy requires disclosing AI assistance in issues or comments, the comment discloses the tool and the extent of its use. If the policy requires comments in the contributor's own voice, the comment reads as human-written. If the policy states no such requirement for issue comments, this check passes. | required |
| control-run-isolates-trigger | The repro report's artifacts. | A contrast or control run is present that isolates the trigger (the failing case next to a passing one, or the behavior with vs. without the triggering flag). Strengthens the proof but is never required. | preferred |

## Verdict rule

Accept if and only if every `required` check passes. `preferred` checks
never change the verdict. A required check graded `unclear` counts as a
fail: proof I cannot verify from the package is proof that is not ready
to post. The verdict space is binary — `accept` (ready to post) or
`reject` (hold) — with no third outcome.

In a claim-only draft (no repro report yet), the repro-report checks
(`environment-recorded`, `steps-rerunnable`, `artifact-shows-claimed-behavior`,
`claims-within-evidence`, `control-run-isolates-trigger`) are reported
`unclear` with evidence "not yet applicable: claim-only draft" and are
left out of the verdict. The verdict then rests only on the claim-comment
and conventions checks, answering: is this claim ready to post?
