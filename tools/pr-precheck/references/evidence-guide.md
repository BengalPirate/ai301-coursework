# Evidence guide: where evidence lives in a PR package

<!--
The tool's map: for every evidence family a rubric check names, where
to find it in a PR package (eval bundle sections, or live GitHub + the
working copy) and what good looks like when you do. Observable
conditions, not adjectives. The four families are the harness's failure
categories: plan fidelity = silent-drift, test evidence = not-tested,
diff quality = unreviewable, standards and comms = standards-wall. A
package that fails none of them is a clear-accept.
-->

## Plan fidelity (harness category: silent-drift)

**Where it lives.** The plan's scope is in its **Scope** line
(in-scope) and **Not in scope** line, plus the **Files** it names and
any **`## Deviations`** note; in an eval bundle these sit in the
plan-context block, live they are in `plan.md`. What actually changed
is the **unified diff** (the candidate PR's `Diff` section, or
`git diff main...HEAD` on the branch). The description's fidelity
claims are in the PR **Description** ("implements the plan exactly",
"no other changes", "per the posted plan").

**What good looks like.** Every functional hunk in the diff falls
inside the plan's in-scope boundary and named files, the plan's whole
change is present, and the description says what the diff does. Silent
drift reads two ways: **more than the plan** — a hunk the plan put out
of scope or never named riding along with no deviation note (a
one-line fallback fix arriving with an unrequested helper rewrite and a
brand-new config option the plan explicitly scoped out, while the
description says "implements the posted plan exactly; no other
changes"); or **less than the plan** — part of the planned change
dropped with no note. A description claiming a fidelity the diff
contradicts is silent drift, not a comms problem. An honest deviation
re-ties the match: a diff that does less, with a `## Deviations` note
or a description line saying exactly what was left and why, is bounded
and honest, not drift.

## Test evidence (harness category: not-tested)

**Where it lives.** The PR's **Test evidence** section (its
before/after and the stated outcome of the repo's checks or suite),
read against the plan's **Test plan** and the reproduction's own
**Steps / observable**; in an eval bundle the repro observable is in
the plan-context block's repro evidence, live it is in the student's
`test_evidence.md` read against `plan.md` and the issue's repro.

**What good looks like.** Decisive reads as the reproduced behavior's
observable shown **before and after on the path the fix changed**: the
exact artifact the plan named flips (the field length goes 99999 →
100000, the paged output's literal `ESC[...m` becomes real color, the
query returns `?997;1n` instead of `?997;2n`, exit 101 → exit 0), and
the repo's required checks/suite outcome is visible ("pytest ... 2841
passed"). Not-tested reads as proof of nothing observable — "tested
locally, colors work now", "`cargo test` passes" with the reproduced
symptom never shown before/after — or evidence that exercises a path
other than the one the issue reproduced, or the repo's required checks
never run. A check honestly reported as failing, with its reason, is
still evidence; a vague "works now" is not.

## Diff quality (harness category: unreviewable)

**Where it lives.** The change itself: the **unified diff** and the
**commit list**. This family reads only the diff's non-functional
content — whether it matches the plan is plan fidelity, above.

**What good looks like.** A reviewable change is the fix plus its tests
and any required changelog/docs entry, and nothing else: a reviewer can
read it top to bottom without wading through noise. Unreviewable reads
as debris burying the change — debug leftovers (`eprintln!("DBG ...")`,
stray `print`), commented-out code or a kept "first attempt" block
(`// let mut ... ^ first attempt: keeping for reference`), dead or
unused code (an `_unused_buffer_probe` helper nothing calls),
formatting churn rewriting untouched lines, or unrelated hunks folded
in. Commit messages like `wip` / `fix` / `fmt + cleanup` are a
secondary tell, but the diff content is what decides it.

## Standards and comms (harness category: standards-wall)

**Where it lives.** The repo's stated asks are in the **repo-facts
block** — its PR-template sections, contribution instructions, and any
**AI-use policy** (live: the repo's `.github/PULL_REQUEST_TEMPLATE.md`
and `docs/CONTRIBUTING.md` / `AI_POLICY.md`). Where the PR honors them
is the **Description**, the **Title**, and the **diff** (for a required
changelog/whatsnew entry). Treat every candidate PR as AI-assisted.

**What good looks like.** Compliant reads as every required template
section filled with real content — the `Closes #<issue>` link, the
required checklist actually completed, the required changelog/whatsnew
entry present in the diff — and, where the repo's policy requires it, a
real AI-use disclosure naming the tool and the extent of its help. The
standards wall reads as required sections visibly ignored (a solid fix
with decisive evidence but no `Closes #`, no checklist, and the
template's required whatsnew entry absent from the diff) or a stated
AI-disclosure policy left unmet (a clean, faithful, well-tested PR into
a repo whose policy demands disclosure, with no disclosure line
anywhere). Where the repo states no template and no AI policy, silence
is a pass: there is no wall to clear. (Whether the description's claims
match the diff is plan fidelity, not this family.)
