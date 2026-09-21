# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53

**Verdict output**

Live-mode output of `issue-select` on the issue above, run with the same
`rubric.md` uploaded to `tools/issue-select/`:

````
**codepath/pathreview-ai301-fa26-s3#53** — "PII scrubber fails to redact parenthesized US phone numbers" (in scope: it's the course's Path Review repo).

**Required checks**
- **repo-alive — pass.** `archived: false`; newest default-branch commit 2026-09-16 (Andrew Burke, "chore: track five more manifest entries against the tracker"), 4 days before today.
- **unclaimed — pass.** `assignees: []`, 0 comments, timeline holds only four `labeled` events — no linked PR, no claim. (Path Review's house rule would have made classmate claims non-blocking anyway; there are none.)
- **bounded-scope — pass.** One closing condition: extend the phone regex in `safety/pii_scrubber.py` to match `(555) 123-4567`. Filed by `Aburke225` with `author_association: COLLABORATOR` and triage-labeled `bug` / `tier-1`, so the product decision is settled; no design thread (0 comments); body names the file, a runnable repro, and the four failing tests in `tests/unit/test_pii_scrubber.py`.
- **ai-policy-allows — pass.** `docs/CONTRIBUTING.md` (linked from README; no root or `.github/` copy, no `AI_POLICY.md`) covers branches, commits, CI and xfail markers, and says nothing about AI-assisted work — silence is a pass.

**Preferred checks**
- **maintainer-answers — pass.** Issues #52 and #43 (opened 2026-09-10) each drew a first reply from `Aburke225` (COLLABORATOR) on 2026-09-16 — ~6 days.
- **shipping — fail.** The releases endpoint returns none; this repo ships from `main` only. Preferred, so it doesn't touch the verdict.
- **newcomer-labeled — pass.** Labeled `good first issue` and `tier-1` ("Starter difficulty").

**Verdict: accept.** Every required check passes. On fit it's close to ideal for you: pure Python, one named file, a copy-pasteable repro, and named tests to verify against — no infrastructure to stand up. One thing `docs/CONTRIBUTING.md` demands that the issue doesn't mention: those four tests are marked `@pytest.mark.xfail(strict=True)`, so your fix makes CI fail with `XPASS(strict)` until you delete the markers. Removing them is part of the PR.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53",
  "checks": [
    {"name": "repo-alive", "grade": "pass",
     "evidence": "archived: false; newest default-branch commit 2026-09-16 (Andrew Burke), 4 days before today 2026-09-20"},
    {"name": "unclaimed", "grade": "pass",
     "evidence": "assignees: [], 0 comments, timeline shows only 4 'labeled' events — no linked PR and no claim"},
    {"name": "bounded-scope", "grade": "pass",
     "evidence": "One regex fix in safety/pii_scrubber.py, filed by a COLLABORATOR and labeled bug/tier-1, with repro and 4 named failing tests"},
    {"name": "ai-policy-allows", "grade": "pass",
     "evidence": "docs/CONTRIBUTING.md is the only contribution policy and is silent on AI; no AI_POLICY.md/AGENTS.md exists"},
    {"name": "maintainer-answers", "grade": "pass",
     "evidence": "Issues #52 and #43 (opened 2026-09-10) got first COLLABORATOR replies from Aburke225 on 2026-09-16, ~6 days"},
    {"name": "shipping", "grade": "fail",
     "evidence": "GitHub releases endpoint returns an empty list — no published releases"},
    {"name": "newcomer-labeled", "grade": "pass",
     "evidence": "Labels include 'good first issue' and 'tier-1' (Starter difficulty)"}
  ],
  "verdict": "accept"
}
```
````

---

## Eval iterations

**Run history**

Four runs, in order:

1. `--only issue-01,issue-12,issue-14` — **3/3**. A deliberate smoke run on the
   three bundles I expected my thresholds to get wrong, before spending on a
   full pass.
2. Full run — **19/20**, bar PASS, categories `claimed 4/4  clear-accept 7/8
   dead-repo 3/3  policy 1/1  scope 4/4`. One disagreement: `issue-19`, which my
   rubric rejected and the gold label accepts.
3. `--only issue-01,issue-05,issue-10,issue-14,issue-19` — **5/5**. The canary
   re-run after rewriting `bounded-scope`: `issue-19` to confirm the fix, and
   `issue-05`, `issue-10`, `issue-01`, `issue-14` to confirm I had not broken
   the umbrella rejects or the multi-file accepts in the process.
4. Full run — **20/20**, bar PASS, categories `claimed 4/4  clear-accept 8/8
   dead-repo 3/3  policy 1/1  scope 4/4`. This is the run committed as
   `eval-run.txt`; its agreement line reads `20/20 scored items`.

**Issue analysis**

`issue-19` — zxlive#517, "Selecting large subgraphs in proof mode freezes the UI".

My rubric decided **reject**; the gold label is **accept**. The harness named
the cause: `failed: bounded-scope, newcomer-labeled (preferred)`.

`bounded-scope` clause (a) was written to catch umbrella issues, and at the time
it read "an umbrella, tracking, or 'mega' issue listing separable items ... or a
single coherent change that edits several files". `issue-19`'s body is a
maintainer listing **two potential causes** ("The matchers are slow for certain
rewrites (quadratic instead of linear)", "UI update is waiting for the matching
thread to finish") followed by **three additional suggestions** (multi-processing,
matching only expanded categories, threading the rewrite application). The grader
read that five-item list exactly as my wording told it to — as separable items
that cannot close in one PR — and wrote so in its evidence line.

The gold label reads the same text as one bug: the UI freezes, and a maintainer
has pre-diagnosed why. The five bullets are a diagnosis plus a menu of possible
approaches, not five deliverables. The issue has one closing condition —
selecting a large subgraph no longer freezes the UI — and any of the listed
approaches closes it.

So the miss was in my wording, not in the grader's application of it. My clause
was counting list items when it should have been counting closing conditions.
The fix was to say that directly: "The question is whether the issue has ONE
closing condition; if it does, it is bounded", plus a third named exception for
"a bug report naming one symptom that then lists several contributing causes or
suggested approaches". Run 3 confirmed `issue-19` flipped to accept, and run 4
put the full set at 20/20.

**Check rationale**

The check is `bounded-scope`. Its pass condition, quoted as currently written in
`tools/issue-select/rubric.md`:

> One pull request, by one newcomer, must be able to finish this issue and leave
> it closable. Fails if ANY of: (a) it cannot close in one PR — it is an
> umbrella, tracking, or "mega" issue listing separable items (often other issue
> numbers) meant to be split, or it invites an open-ended series of PRs with no
> defined end state; (b) the thread runs 10 or more comments without a
> maintainer having named the solution to build — an unsettled design debate;
> (c) it asks for a feature or behavior change that no maintainer has endorsed
> (not filed by an OWNER / MEMBER / COLLABORATOR, no maintainer comment backing
> it, and no maintainer-applied triage label), so the product decision is still
> open; (d) a maintainer states the fix reaches core internals, or the issue is
> a usage/support question rather than a change. Otherwise passes. The question
> is whether the issue has ONE closing condition; if it does, it is bounded.
> Three shapes that are NOT scope failures: a terse body, a missing repro, or an
> unpolished bug report — grade the size of the work asked for, not the quality
> of the writeup; a single coherent change that edits several files or spells
> out several numbered steps; and a bug report naming one symptom that then
> lists several contributing causes or suggested approaches, which is a
> diagnosis and a menu of options, not a list of separable deliverables.

Why it is shaped this way. The first draft of this check was a single sentence
about "one bounded piece of work", which is an adjective, not a threshold — two
people reading it get two answers. Each clause replaced a judgment call with
something a grader can point at:

- **(a)** exists because `issue-10` is literally titled "Documentation request
  megaissue" and its body is a list of ~38 other issue numbers, and `issue-05`
  says "PRs are welcome both big and small". Neither has an end state.
- **(b)** is a number instead of "long discussion" because I needed to separate
  `issue-15` (97 comments, two abandoned PRs) and `calib-04` (23 comments over
  five years) from `issue-09`, which is from 2018 and still accept. 10 is above
  every accepted issue in the set (the highest is 4) and far below every
  scope reject, so the threshold does real work without sitting on a boundary.
- **(c)** is what separates `issue-20` from the accepts. Its body is
  well-formatted and even states "Success looks like: logo tool in the shapes
  toolbar", so polish cannot be the discriminator. What it lacks is anyone with
  authority wanting it: filed by `cursor[bot]` with `author_association: NONE`
  and **no labels at all**. Every accepted issue in the set is either filed by a
  maintainer or carries a maintainer-applied triage label.
- The three named exceptions are each scar tissue from a specific miss. The
  "several files" one protects `issue-01` (five docs files) and `issue-14` (six
  files, a numbered walkthrough); the "diagnosis and a menu" one is the
  `issue-19` fix described above.

**Trade-offs**

The (b) threshold is the check's real cost, and it is a genuine one: 10 comments
is a proxy for "unsettled design", and a proxy fails on the cases where a bounded
issue simply attracts a friendly crowd. A one-line typo fix with fifteen
"+1, I hit this too" comments and no maintainer verdict would be rejected by this
check for a reason that has nothing to do with its scope. I am accepting that
miss: in this eval set no accepted issue exceeds 4 comments, so the threshold
costs nothing measurable here, and a long thread with no maintainer having named
the fix is more often a warning than a coincidence. If it started rejecting good
issues, the fix is to demote (b)'s weight rather than to move the number.

The `issue-19` rewrite also carried a risk of the opposite failure — loosening
(a) enough to let real umbrellas through — so I did not take it on trust. Run 3
re-graded `issue-05` and `issue-10`, the two umbrella rejects most exposed to the
change, alongside `issue-01` and `issue-14`, the two multi-file accepts. All four
held their previous verdicts, and `issue-19` flipped to accept: 5/5. The full run
that followed confirmed it across the set at 20/20, with `scope 4/4` intact.

---

## Selection rationale

**Selection rationale**

**1. Fit to my interests and the time available.** #53 is pure Python in one
file, `safety/pii_scrubber.py`, and the fix is widening a phone-number regex to
match the parenthesized `(555) 123-4567` form. The issue ships a runnable
reproduction and names the four tests in `tests/unit/test_pii_scrubber.py` that
currently fail, so I can verify the change locally with pytest and nothing else
— no database, no services to stand up. That matters with Unit 2 starting
immediately: I can reproduce this in minutes rather than spending the first
session fighting an environment. It also lines up with what I said I wanted to
get better at in `scope.md` — writing tests a maintainer trusts — because the
tests already exist and define exactly what "fixed" means.

**2. What the verdict identified correctly, and what I weighed that the rubric
could not.** The rubric got the mechanical facts right and I would not second-
guess any of them: the repo is alive (newest commit 4 days old), nothing is
claimed (no assignee, no comments, no linked PR), the scope is one closing
condition, and `docs/CONTRIBUTING.md` is silent on AI so the policy check passes.
What the rubric could not weigh is the detail the live run turned up by reading
the contributing guide rather than the issue: the four failing tests are marked
`@pytest.mark.xfail(strict=True)`, so the moment my fix works, CI fails with
`XPASS(strict)` until I delete the markers. No check in my rubric looks for
that, and nothing in the issue body mentions it — a first-timer following only
the issue would push a correct fix and watch CI go red. I also weighed something
my rubric explicitly refuses to: `shipping` failed for every candidate because
this repo has never published a release. That is the correct call for a
classroom repo and it is exactly why I kept `shipping` preferred rather than
required, but it took a human to know the difference.

**3. Anticipated difficulty in claiming it.** Low, and mostly procedural rather
than competitive. #53 had zero comments and no assignee when I graded it, and
Path Review's house rule in `scope.md` means classmates' claims would not block
me even if some appear before Unit 2 — course credit attaches to the PR I open,
not to whether it merges. The real difficulties are the two things above the
claim itself: remembering that removing the `xfail(strict=True)` markers is part
of the PR, and widening the regex without over-matching things that are not
phone numbers, since the existing tests will catch a regex that gets greedy. I
have not commented on the issue — choosing is not claiming, and the claim
comment belongs in Unit 2.

---

Related paths: `eval-run.txt` in this directory; the skill's files in
`tools/issue-select/`.
