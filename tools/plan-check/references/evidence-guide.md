# Evidence guide: where evidence lives in a plan package

<!--
The skill's map: for every evidence family a rubric check names, where
to find it in a package (eval bundle sections, or live GitHub + drafts)
and what good looks like when you do. Observable conditions, not
adjectives.
-->

## Diagnosis and grounding

**Where it lives.** The plan states its cause in its `Diagnosis` or
`Cause` section. The behavior that cause must explain is in the
**Repro evidence** block — its Steps, Actual, and especially any
**Control run**, isolating step, or `--debug` output (the lines that
pin down *which* component the failure comes from). Live: the plan's
cause in `plan.md`, the behavior in the student's posted repro comment.

**What good looks like.** The stated cause names behavior the repro
evidence actually shows, and agrees with what the control/isolating run
points at. A diagnosis is wrong-cause when it blames a component the
repro's own control ruled out (e.g. the items parse fine without the
flag, and `--debug` shows argparse failing before the tokenizer is
reached — yet the plan blames the tokenizer), or when it waves away as a
"red herring" the exact signal the repro isolated.

## Scope

**Where it lives.** The plan's `Scope` section: its in-scope line, its
`Not in scope` line, and the files/areas it names. Cross-read with the
`Approach` list, since scope creep often hides as extra approach steps.

**What good looks like.** One coherent change that closes the reproduced
issue, with a not-in-scope line that explicitly fences off the
tempting adjacent work. A drive-by rewrite reads as the opposite: a
one-line fix bundled with a dependency migration, a refactor of
surrounding code, or a "five-front" list where only one front is the
issue. A plan that defers a related-but-separate concern to a follow-up
(and says so) is bounded, not deficient.

## Executability

**Where it lives.** The plan's `Files`, `Change`, and `Approach`
sections — what will actually be edited and how.

**What good looks like.** A named file or specific area plus a concrete
approach or first step, enough that a stranger could open the file and
start. Unbuildable reads as an intention to investigate rather than a
change: no chosen file, no chosen layer ("gocui? tcell? not sure"), no
chosen approach ("profile and optimize", "poke around the editor",
"recover() somewhere", "upstream or vendored, whichever is easier").

## Test plan

**Where it lives.** The plan's `Test` / `Test plan` section, read
against the **Repro evidence** block's Steps and Artifact.

**What good looks like.** It re-runs the repro's own steps (or a check
that drives the same code path) and states the specific observable that
would flip when fixed — the color changes without leaving the view, the
command prints the request with exit 0, the two fuzz cases pass and the
control is unchanged. A vague test plan names no observable ("make sure
it's faster", "verify it works") or cannot be tied to the reproduced
behavior.

## Honesty

**Where it lives.** The plan's `Risks`, `Unknowns`, or `Deviations`
sections, read against the confident claims elsewhere in the plan and
against the repro evidence.

**What good looks like.** Real uncertainties are named as open questions
or flagged trade-offs ("I have not measured the per-print cost; if it
shows in the benchmark I will move the check and flag it for review").
False confidence is the opposite: a load-bearing claim stated as settled
that the repro evidence does not support. A mid-build deviation is
recorded honestly in the plan's Deviations section, not left to live
only in the diff.

## Comms

**Where it lives.** The **Candidate plan comment**, read against two
things: the **Thread highlights** (explicit maintainer direction — a
maintainer's stated cause, a chosen approach, or an approach they
already rejected) and the **Repo facts** block's contribution policy,
PR/bug template asks, and any **AI-use policy**.

**What good looks like.** The comment engages the thread: it follows or
explicitly addresses a maintainer's stated direction rather than
ignoring it (a docs-only plan posted where the owner has already found
the code culprit and is pursuing a code fix is ignoring direction), and
it does not re-propose an approach the maintainer already rejected as
too expensive. On policy: where the repo's stated AI policy requires
disclosure, the comment names the tool and the extent of the
assistance; where the policy is silent, no disclosure is needed
(silence is a pass). Treat every candidate plan as AI-assisted, so a
required disclosure that is simply absent is a fail, not a non-issue.
Thread-aware reads as "I reproduced it, your direction in the thread is
X, here is how my bounded plan follows it"; boilerplate reads as a
generic plan that could have been written without reading the thread.
