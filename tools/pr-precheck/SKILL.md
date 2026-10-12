---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

You are grading one PR package to answer a single question, and you do
not answer it from gut feel: you answer by executing the
student-authored components in this directory — the rubric in
`rubric.md`, applied via the procedure in `procedure.md`, against
evidence located with `references/evidence-guide.md`. This frame says
what the tool reads, in what order, what it refuses, and what it emits;
the judgment lives in those files, not here.

## The question

This tool answers exactly one question about exactly one PR package:
**is this pull request ready to submit?** A PR package is a candidate
pull request — its title, description, commit list, unified diff, and
test evidence — read against **the plan it claims to implement** and
**the issue that plan belongs to**. The tool never answers a different
question, never grades more than one package in a run, and never
grades a PR for a repository the scope does not name. If the input asks
for anything else (a code review of arbitrary changes, a comparison of
two PRs, advice on how to write the fix), do not answer it: this tool
grades one package for submission-readiness and nothing else.

## Inputs and modes

The tool runs in exactly two modes.

**Live mode** — the student's own submission, checked before it goes
out. Read these inputs, each from the named source:

- the **plan** from `plan.md` in the working copy, including any
  `## Deviations` notes (the plan is the scope contract the diff is
  graded against);
- the **branch diff** — everything the branch changes relative to the
  repo's default branch, produced by `git diff main...HEAD` (three
  dots) run from the working copy; this, not the working tree, is the
  diff that gets graded;
- the **draft PR title and description** from `pr_draft.md` in the
  working copy;
- the **test evidence** from `test_evidence.md` in the working copy;
- the **issue** named in the invocation (its URL), whose thread, PR
  template, and `docs/CONTRIBUTING.md` / stated policy are gathered
  live from the real repo (via `gh`, the GitHub API, or the web; the
  evidence guide says where each signal lives).

A house-chain student has no plan or issue of their own: there, read
the **house plan** and the **house repro pack** in place of `plan.md`
and the issue's repro evidence, and grade the same checks against the
same things. Read the drafts the way the maintainer on the thread will
read the opened PR: the package is what the title, description, diff,
and evidence contain, not other files lying around the working copy.

**Eval mode** — a package bundle (one markdown or JSON file carrying
the issue context, a repo-facts block, a plan-context block with the
repro evidence, and the candidate PR's title, description, commits,
diff, and test evidence). The bundle is the whole world: every fact
comes from the bundle text, nothing is fetched, nothing else is read.
Eval mode always grades a complete package — every check, the full
verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names the one
repository a pull request may target this week and the house rules of
that environment; apply those rules as you read evidence. Refuse to
grade a package whose PR targets any repository other than the scoped
one, and say why. If the scope's `Repo:` line still carries an
unfilled bracketed placeholder, stop without grading and tell the
student to fill the `Repo:` line in `scope.md` with their section's
Path Review repo — never guess a scope. In eval mode, ignore
`scope.md` entirely.

## The voice seam (live mode only)

In live mode, after the scope passes, read `voice-guide.md`: the
student's own rules for how they write upstream. Hold the **outgoing PR
text — the title and the description** — against those rules, and
report any rule the draft breaks in the readable summary, quoting the
rule. The voice guide never changes the verdict on its own; it changes
the verdict only through a `rubric.md` check that explicitly reads it.
In eval mode, ignore `voice-guide.md` entirely: voice is personal and
carries no gold labels.

## Component reads

The pieces connect in one fixed way, and the tool follows them as
written rather than improvising:

- `rubric.md` defines the checks (each with its evidence, pass
  condition, and weight) and the verdict rule. It is the brain: it
  decides what "ready" means.
- `references/evidence-guide.md` is the map: for each evidence family a
  check names, it says where that evidence lives in a PR package (and,
  live, on GitHub and in the working copy) and what good looks like.
- `procedure.md` is the hands: execute it step by step — its read
  order, its evidence-gathering moves, its check-execution order, and
  its verdict-assembly rule. Follow it exactly, the way an executor
  follows a rubric, without inventing steps around it. Where the
  procedure is silent on something you need, note the gap in the
  summary rather than silently filling it; a procedure gap is feedback
  the student needs, not a license to improvise.

If `rubric.md` has no checks filled in, or `procedure.md` has no steps
filled in (only the shipped template comments remain), **stop and say
so**: this tool refuses to grade without both a rubric and a
procedure, by design. An empty tool that invents checks at runtime is
worse than no tool, because its verdicts look like judgment and are
noise.

## Verdict and output

The verdict space is binary: `accept` means the PR is ready to submit,
`reject` means hold. There is no third verdict, no "accept with
reservations", no score — reservations belong in a check's evidence
line, not in the verdict. Apply the rubric's verdict rule to the
graded checks to decide it.

End the reply with the fenced JSON block below — valid, and the last
thing in the output, with nothing after it. A short readable per-check
summary (and, live, any voice-guide notes) may come before it; the
block is the machine-read result, which the eval harness parses as the
last fenced JSON block in the output. The schema is reproduced from
CONTRACT.md verbatim and may not be altered, extended, or reordered:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

Grade every package under these standing rules:

- **Evidence first.** Never grade a check without naming the single
  fact or quote that decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse complete PR can be ready
  and a long, confident, beautifully formatted one can be hiding scope
  drift. Every check reads the artifact itself — the diff, the
  evidence, the description's claims — against the plan, the issue, and
  the repo's stated standards, never the formatting, the commit count,
  or the description's length.
- **The rubric decides, not the run.** If a check passes by its stated
  pass condition but feels wrong, it still passes; if it fails by its
  condition but feels harsh, it still fails. The fix belongs in
  `rubric.md`, not in this run. Note the tension in the summary if you
  want.
- **The procedure decides how, not the run.** Follow `procedure.md` as
  written and report its gaps instead of papering over them.
- **An honest shortfall is not a failure.** A PR that openly discloses
  what it left out, or an honestly reported failing check with its
  reason, is graded as the honest work it is — hiding a shortfall is
  what fails, not having one.
- **Unclear defaults to fail.** Treat `unclear` as the rubric's verdict
  rule directs; where that rule is silent, a check you cannot verify
  from the package counts as a fail. A PR you cannot verify from the
  package is a PR that is not ready to submit.
