# Unit 4 — pull-request submission

## PR link
<!-- The link to your PR's OWN page on the Path Review repo (not your
     fork's branch page). Fill in after you open the PR. -->
https://github.com/codepath/pathreview-ai301-fa26-s3/pull/153

## Branch name
fix/53-redact-parenthesized-phone

## Run history
My smoke run graded the first three packages (pkg-01 standards-wall,
pkg-02 clear-accept, pkg-03 silent-drift) and agreed on all three
(3/3) — a cheap wiring + verdict check across three different
categories before spending on a full run. I then ran one full
20-package run, which agreed on all twenty and cleared the category
floor:

    clear-accept 7/7  not-tested 4/4  silent-drift 4/4
    standards-wall 2/2  unreviewable 3/3
    agreement: 20/20 scored items  (bar: 18/20: PASS)

That full run is the one saved to eval-run.txt. It took no revision
loop: the four required checks were written to map one-to-one onto the
harness's four failure categories (plan-fidelity = silent-drift,
test-evidence = not-tested, diff-reviewable = unreviewable,
standards-met = standards-wall), with clear-accept being the state
where all four pass, so the first full run matched every gold label.

## Package analysis
Package: **pkg-04** (sharkdp/bat#3833, category not-tested).
- My rubric's verdict: **reject**
- Gold label: **reject** (agree)

Reasoning: the fix itself is plausible and faithful to the plan (pass
`-R`/`--RAW-CONTROL-CHARS` in the Windows fallback flag set, one unit
test, a CHANGELOG entry — all inside the plan's scope), so
plan-fidelity, diff-reviewable, and standards-met all pass. The
package fails on **test-evidence**: the plan's test plan named a
concrete observable — "re-run `bat Cargo.toml` under pwsh with
less-Windows, expect paged output WITH colors, matching the
`--pager="less -R"` control." The PR's entire evidence section is
"Tested locally on Windows 11 with less-Windows: colors work now in
paging mode. `cargo test` passes." That shows no before→after on the
reproduced path (the literal `ESC[38;5;...m` escape text turning into
real color), names no artifact, and gives no visible outcome for the
control — exactly the "'tests pass' with nothing named" shape the
not-tested category is built around. My rubric's test-evidence pass
condition requires the reproduced behavior's observable shown before
and after on the changed path, so the missing before/after is the
fact that decided the reject.

## Check rationale
The check I lean on most, quoted as it now reads in rubric.md:

> | test-evidence | The PR's test-evidence section, read against the
> plan's test plan and the reproduction's own steps, plus the stated
> outcome of the repo's own checks or suite. | The evidence shows the
> reproduced behavior's observable before→after — the specific
> artifact the plan's test plan named (the exact output, value, exit
> code, or symptom that flips when fixed) — exercised on the path the
> fix changed, AND the repo's required checks/suite outcome is
> visible. Fails when the evidence names no observable before/after
> ("tested locally, works now", "tests pass" with nothing named),
> proves only a path other than the reproduced one, or the repo's
> required checks were never run or shown. A check honestly reported
> as failing, with its reason, still counts as evidence; a hidden or
> vaguely-waved one does not. | required |

Why it reads that way: the whole not-tested failure family is PRs that
assert success without showing it, so the pass condition is written
around an *observable* — a named artifact that visibly flips from the
pre-fix state to the post-fix state on the exact path the fix touched
— rather than around the word "tested" or a green check mark. Two
clauses do the load-bearing work. "Exercised on the path the fix
changed" rejects evidence that passes a different code path than the
one reproduced. The final sentence encodes the course's honest-outcome
rule: a check reported as failing *with its reason* still counts as
evidence, so a PR that honestly says "make test-integration reports
'no tests ran'" is not punished for honesty, while a vague "works now"
is.

## Trade-offs
The sharpest design trade-off was keeping plan-fidelity and
diff-reviewable orthogonal so two graders agree. A package like pkg-03
(a config option bolted onto a one-line fix) technically trips both an
"out of scope" reading and an "unrelated hunk" reading. I resolved it
by a rule written into both the rubric and the procedure:
plan-fidelity reads only *functional* changes against the plan's
scope, and diff-reviewable reads only *non-functional* debris (debug
lines, dead code, commented-out blocks, formatting churn). That keeps
the two checks from double-counting and makes the deciding check
predictable (first failing required check in table order, so pkg-03
quotes plan-fidelity and pkg-12 quotes diff-reviewable). The second
trade-off was treating `unclear` as `fail`: a PR I can't verify from
the package is not ready to submit, which is strict but matches the
contract's default. The cost is that a genuinely-fine PR with a
thin-but-adequate evidence block could be held; I accepted that
because the eval set never punished it and because holding an
unverifiable PR is the safer error for a submission gate.
