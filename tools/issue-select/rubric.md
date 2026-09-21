# Rubric: is this a good first issue?

All date thresholds below are measured against the bundle's stated
capture date in eval mode, and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| repo-alive | The repo line (`archived:`) and the "last 5 default-branch commits" list in the repo-facts block. Live mode: the archived banner, and the newest commit date above the file list. | `archived` is not `yes`, AND the newest of the last 5 default-branch commits is dated within 90 days of the capture date. Bot-authored commits count: a bot merging a human's pull request is maintainer activity. Absent or unreadable commit dates grade `unclear`. | required |
| unclaimed | The `this issue:` line of the repo-facts block (`assignees:` and `linked PRs:` with state per PR), plus every comment in the thread. Live mode: the Assignees box, the Development box, and the thread. | No assignee is set, AND no linked PR is in the `open` state, AND the thread holds no live claim. A claim comment (`/assign`, "I'll take this", "can I work on this", "working on this") or a PR named as in progress in the thread is **live** when it is dated within 180 days of the capture date; older than that with no open PR behind it, it is a stale claim and does not block. Linked PRs in `closed` or `merged` state never block. | required |
| bounded-scope | The issue title and body, its labels, its `author_association`, and the full comment thread. | One pull request, by one newcomer, must be able to finish this issue and leave it closable. Fails if ANY of: (a) it cannot close in one PR — it is an umbrella, tracking, or "mega" issue listing separable items (often other issue numbers) meant to be split, or it invites an open-ended series of PRs with no defined end state; (b) the thread runs 10 or more comments without a maintainer having named the solution to build — an unsettled design debate; (c) it asks for a feature or behavior change that no maintainer has endorsed (not filed by an OWNER / MEMBER / COLLABORATOR, no maintainer comment backing it, and no maintainer-applied triage label), so the product decision is still open; (d) a maintainer states the fix reaches core internals, or the issue is a usage/support question rather than a change. Otherwise passes. The question is whether the issue has ONE closing condition; if it does, it is bounded. Three shapes that are NOT scope failures: a terse body, a missing repro, or an unpolished bug report — grade the size of the work asked for, not the quality of the writeup; a single coherent change that edits several files or spells out several numbered steps; and a bug report naming one symptom that then lists several contributing causes or suggested approaches, which is a diagnosis and a menu of options, not a list of separable deliverables. | required |
| ai-policy-allows | The `contribution policy` line of the repo-facts block. Live mode: `CONTRIBUTING.md` in the root or `.github/`, the docs it links to, and any `AI_POLICY.md` / `AI_USAGE_POLICY.md` / `AGENTS.md`. | Passes unless the stated policy bars AI-assisted contributions outright with no compliant path. These all **pass**: no CONTRIBUTING.md at all; a policy silent on AI; a policy allowing AI under conditions (disclose, understand, test, human-review); a policy refusing only *fully* AI-generated work while permitting assistive use. Silence is a pass, never `unclear`. Fails only on a flat prohibition, e.g. "we do not accept AI-generated code or documentation". | required |
| maintainer-answers | The "maintainer first-response sample" in the repo-facts block. Live mode: the Issues tab sorted by recently updated, reading first replies from Owner / Member / Collaborator badges. | At least one issue in the sample drew a first maintainer response within 30 days. | preferred |
| shipping | The `latest release` line of the repo-facts block. Live mode: the Releases box on the repo front page. | A release published within 365 days of the capture date. A repo with no releases published cannot pass this check, which is why it is preferred and not required: some living repos ship from the default branch only. | preferred |
| newcomer-labeled | The issue's labels. Live mode: the labels on the issue page. | Carries a label inviting newcomers — `good first issue`, `help wanted`, `easy`, or the project's equivalent. | preferred |

## Verdict rule

**accept** when every `required` check grades `pass`. Any single required
`fail` rejects the issue.

`preferred` checks never change the verdict. Report their grades; on an
accepted issue they are the reasons to rank it above other accepted
candidates.

`unclear` on a required check counts as `fail`: a first issue whose
evidence you cannot verify is not a first issue you should take. One
exception is written into `ai-policy-allows` above — an absent or
AI-silent contribution policy is a **pass**, not `unclear`, because most
repos state nothing and silence is not a restriction.
