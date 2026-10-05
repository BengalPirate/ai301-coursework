# Evidence guide: where proof lives in a reproduction package

<!--
This is the rubric's map. For every kind of proof a check names, it
says WHERE to look (in an eval bundle, and live on GitHub) and WHAT
GOOD LOOKS LIKE there. Each "what good looks like" is an observable
condition someone else could apply and reach my answer, not an
adjective.
-->

## Environment

**Where it lives.** In an eval bundle: the repro report's "Environment:"
line or environment block, read against the issue's own version/OS line
and the repo-facts "bug reports" template (which names the environment
fields that repo asks for). Live: the environment block in the draft
repro comment, read against the issue body's stated version/platform.

**What good looks like.** The tool version and the OS/platform are both
named, plus the install method when the issue's behavior can depend on
it (e.g. a build-profile-sensitive crash, or a package that differs by
install channel). The version tested either matches the issue's target
(or the current release), or the report explicitly calls out the
difference. A silent deviation is the failure: a report that tests an
old version against an issue the reporter confirmed on latest/main,
without saying so, records an environment that is not evidence about the
reported bug. An issue that targets a specific platform (a Windows-only
bug, a specific driver) needs that platform recorded, not an unrelated
one left implicit.

## Steps

**Where it lives.** In an eval bundle: the repro report's "Steps" block
and any inline commands or inputs. Live: the steps block of the draft
repro comment.

**What good looks like.** A stranger with only this report can re-run it
end to end: the commands, inputs, and config are concrete and
self-contained, and the sequence includes the exact action that triggers
the reported behavior. Watch for the trigger specifically — steps that
set everything up but run a different command, or a different flag/syntax
than the issue names, do not reproduce it. The failure cases are steps
that live in a private or unshared place (a private monorepo, a config
file never pasted), steps that say "set up the project" without the
commands, and steps that quietly swap the triggering syntax for an
adjacent one.

## Behavior shown

**Where it lives.** In an eval bundle: the fenced output excerpts, logs,
or described screenshots in the repro report, read against the issue's
"Actual behavior" / described failure. Live: the pasted artifacts in the
draft repro comment, read against the issue body.

**What good looks like.** The artifact is evidence for the SAME behavior
the issue describes — the same error text, the same failure mode, the
same exit status — not an adjacent one that merely also looks like a
problem. Concretely: a graceful argument-validation error (exit 1) is
NOT the reported capacity-overflow panic (exit 101); a compile/"not
defined" error is NOT the reported runtime "invalid path expression"; a
garbled-output-with-the-process-still-alive is NOT the reported crash;
output that only shows the tool launching (a version banner, a session
list) shows nothing of the reported failure. For a cannot-reproduce
report, the artifact instead shows a genuine attempt and its (non-failing
or different) result — that is the right kind of evidence for that
outcome.

## Honesty

**Where it lives.** The report's and claim comment's assertion words
("confirmed", "guaranteed reproducible", "conclusively", "I verified",
"fully reproduced") read against what the artifacts above actually show.

**What good looks like.** The strength of the words matches the strength
of the shown evidence. A report that says exactly what happened — "I ran
X, got Y, which matches the issue" — is honest; so is "I could not
reproduce; here is my attempt and what differed," which is a PASS, not a
failure, when the attempt is shown. The failure is a confident verdict
resting on a wrong or absent artifact: "crash confirmed" over an exit-1
validation error, "I verified this race condition" with no transcript,
"guaranteed reproducible" backed by nothing. Grade the gap between claim
and evidence, not the confidence itself.

## Comms

**Where it lives.** The candidate claim comment and repro comment, read
against the issue (for the claim) and against the repo-facts contribution
policy and AI policy (for both). Live: the issue thread, the repo's
CONTRIBUTING.md / AI policy, and the draft comments.

**What good looks like.** The claim names this issue's specifics and
promises only investigation and a report-back — never a fix, a guarantee,
or a date; a bare "+1 / assign me / I'll fix it in N days" is the failure.
For conventions: **treat every candidate package as AI-assisted work
(these are course submissions).** So when the repo's stated policy
requires disclosing AI assistance in issues or comments, the comment must
contain an explicit disclosure (the tool and the extent of its use) — a
flawless repro with no disclosure still fails this axis where the policy
demands one. When the policy instead requires comments in the
contributor's own voice, a human-voiced comment satisfies it. When the
policy is permissive or silent on issue-comment disclosure, this axis
passes. Specific-and-honest beats boilerplate: an interchangeable
assign-me template fails even when the attached report is fine.
