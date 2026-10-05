# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

BengalPirate

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5986510110

Following up on my reproduction above (#issuecomment-5986071553) with a plan.

**Diagnosis.** The `phone_us` pattern in `safety/pii_scrubber.py` (line 16) separates its digit groups with `[-.]?` — a hyphen or dot, but not a space. In `(555) 123-4567` the area-code `)` is followed by a space, so after `\)?` consumes the paren the space can't be matched and the whole pattern fails; `555-123-4567` matches because its separators are hyphens. That's the control in my repro: dashed redacted, parenthesized passed through with `detect()` returning `[]`.

**Plan — one bounded change to `phone_us`.** Two parts, both in that one pattern: (1) let its separators accept a space (`[-.]?` → `[-.\s]?`), and (2) move the leading `\b` to just after the optional paren (`\(?\b`) so the opening `(` is included in the redaction — otherwise adding the space alone leaves `(` behind and `scrub` returns `([REDACTED]`. Then remove the `xfail` markers on the four phone tests the fix makes pass (`strict=True` makes them `XPASS` once they pass). I checked that every format that already matched — `555-123-4567`, `555.123.4567`, `5551234567`, `(555)123-4567`, `+1 555-123-4567` — still matches, and that the pattern still won't match inside a longer digit run (`5551234567890` is left alone).

**Out of scope.** `test_mixed_pii_and_text` also carries the `#53` marker, but as I noted in the repro its failure is `street_address` over-redaction (it eats "Python"), not a phone miss, so I'm leaving that marker in place and keeping this change to the phone pattern. Happy to raise the `street_address` over-redaction as its own issue.

**Test plan.** Re-run the repro snippet (expecting both numbers `[REDACTED]` and `detect()` returning a `phone_us` match for `(555) 123-4567`) plus the scrubber unit tests, with the four formerly-xfailed tests passing and no `XPASS(strict)`. I'll open a PR from a `fix/53-…` branch on my fork once the change is in place.

---

## Your branch

**Branch**

fix/53-redact-parenthesized-phone

**Evidence**

Re-run of my Unit 2 reproduction against the built change. Same `repro_53.py` in the
repo root both times:

```python
from safety.pii_scrubber import PIIScrubber
s = PIIScrubber()
print('scrub :', repr(s.scrub('Call me at (555) 123-4567 or 555-123-4567')))
print('detect:', s.detect('Call me at (555) 123-4567'))
```

**Before** (current `main`, commit `2f4e82f`, unmodified):

```
$ .venv/bin/python repro_53.py
scrub : 'Call me at (555) 123-4567 or [REDACTED]'
detect: []

$ .venv/bin/python -m pytest tests/unit/test_pii_scrubber.py -q
..xx.......x.....x....x..                                                [100%]
20 passed, 5 xfailed in 0.04s
```

The parenthesized number passes through and `detect()` returns `[]`; the dashed number is
the control.

**After** (branch `fix/53-redact-parenthesized-phone`, commit `07abb22`):

```
$ .venv/bin/python repro_53.py
scrub : 'Call me at [REDACTED] or [REDACTED]'
detect: [{'type': 'phone_us', 'value': '(555) 123-4567', 'start': 11, 'end': 25}]

$ .venv/bin/python -m pytest tests/unit/test_pii_scrubber.py -q -rxX
......................x..                                                [100%]
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_mixed_pii_and_text - issue #53: ...
24 passed, 1 xfailed in 0.03s
```

The parenthesized number is now fully redacted (the opening `(` included) and `detect()`
returns a `phone_us` match for the whole `(555) 123-4567`. The four phone tests pass with
their markers removed and no `XPASS(strict)`; `test_mixed_pii_and_text` stays the one
intentional xfail (the separate `street_address` over-redaction, left out of scope).

## Eval iterations

**Run history**

One full run — **20/20**, bar PASS, categories `clear-accept 7/7  scope-creep 4/4
thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`. This is the run saved as
`eval-run.txt`; its agreement line reads `agreement: 20/20 scored items  (bar: 18/20:
PASS)`. The first full run agreed on all twenty with every category matched, so there was
no revise loop to run — this is the only run.

**Package analysis**

`pkg-20` — source `ghostty-org/ghostty#11261`, category `thread-convention`. My rubric
decided **reject**; the gold label is **reject** (agree). This is the package that proves
why a comms check has to be its own required check rather than folded into "is the plan
good". The plan itself is excellent: the diagnosis follows the repro's control (the
no-hyperlink run isolates growth-during-print), the scope is one bounded change following
the direction the maintainer already gave in the thread, the approach names files and a
concrete mechanism, and the test plan re-runs both fuzz cases. Every plan-quality check
passes. What sinks it is the **candidate plan comment**: ghostty's `CONTRIBUTING.md` +
`AI_POLICY.md` require that *all* AI usage be disclosed, naming the tool and the extent,
and the comment discloses nothing. My `comment-engages-thread-and-policy` check reads the
comment against the repo-facts AI policy, treats every candidate plan as AI-assisted, and
fails it on the missing disclosure — so the package is a reject despite a flawless plan.

**Check rationale**

The check is `comment-engages-thread-and-policy`. Quoted exactly as it reads now in
`tools/plan-check/rubric.md`:

> The comment is consistent with explicit maintainer direction present in the thread — it
> does not ignore or contradict a maintainer's stated cause, chosen approach, or an
> approach the maintainer already rejected — AND it discloses AI assistance (naming the
> tool and the extent) when the repo's stated policy requires disclosure. If the thread
> carries no explicit maintainer direction, that half passes; if the repo's policy
> requires no disclosure for issue comments, that half passes. Fails when the comment
> ignores or contradicts explicit maintainer direction, or omits a disclosure the repo's
> AI policy requires.

It is deliberately one compound check with two fail conditions rather than two separate
checks. The `thread-convention` category has exactly two scored packages, and they fail on
*different* halves: `pkg-04` is a docs-only plan whose comment ignores the owner's explicit
direction toward a code fix, and `pkg-20` is a strong plan whose comment omits the AI
disclosure its repo requires. With only two packages holding up that category's floor, I
needed a single check that reliably catches both halves; splitting them risked a check that
grades one case well and leaves the other to chance. The "treat every candidate plan as
AI-assisted" clause is carried over from my Unit 2 conventions check, because that is what
makes the disclosure half fire on `pkg-20` instead of assuming the comment was human-written.

**Trade-offs**

The cost of that check is a false-positive risk on the rare genuinely-human comment. Because
it treats every candidate plan as AI-assisted, in a repo whose policy requires AI disclosure
it will fail a comment that carries no disclosure even if the author wrote every word
themselves — the check cannot tell a human-written comment from an AI-written one, so it
errs toward requiring the disclosure wherever the policy demands it. I accept that miss: the
assignment's own framing is to treat every candidate as AI-assisted, and the downside it
guards against (shipping an undisclosed comment into a repo like ghostty that explicitly
forbids it, which is exactly `pkg-20`) is worse than asking a fully-human comment to add one
honest disclosure line. Nothing else moved to pay for it: the same check passes all seven
clear-accepts and both halves of `thread-convention`, and the run held the category floor at
one full pass, so the line sits in the right place for this set.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
