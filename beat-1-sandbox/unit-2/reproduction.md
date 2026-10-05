# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

BengalPirate

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5986071387

Hi — this is my first contribution to PathReview. I'd like to take the parenthesized US phone-number case described in this issue: `(555) 123-4567` passes through `scrub()` unredacted and `detect()` returns `[]` for it, while the dashed form `555-123-4567` is redacted and detected as expected. Both formats should be caught.

My next step is to reproduce this on current `main` in a clean checkout — the snippet from the issue plus a dashed-format control, and the `tests/unit/test_pii_scrubber.py` tests marked for #53 — and post a report here with the exact commands and output. After that I'll read the `phone_us` pattern in `safety/pii_scrubber.py` (line 16) and trace how it handles the `(` `)` and the space separator, since that is where the dashed and parenthesized forms diverge. I'll keep the investigation scoped to the parenthesized format those tests cover and report back here with what I find before proposing any change.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5986071553

Reproduction report for #53.

**Result:** reproduced. On current `main`, `(555) 123-4567` is not redacted by `scrub()` and `detect()` returns `[]` for it, while the dashed form `555-123-4567` is handled by both. This matches the issue.

**Environment**

- Code: fresh clone of this repo at commit `2f4e82f` (current `main`); the repo's tracked files are unmodified. The only addition is `repro_53.py` below, a new untracked file I placed in the repo root for the snippet.
- OS: macOS 26.5.1 (build 25F80), Apple Silicon (arm64).
- Python: 3.14.7 in a `.venv` created with `python3 -m venv .venv`.
- Installed into that venv only `structlog` 26.1.0 (the sole runtime import in `safety/pii_scrubber.py`) and `pytest` 9.1.1 (for the test run below). This bug is in a pure-Python module; no database, Redis, or Docker is involved, so I did not run `docker compose` or `make setup`.

**Step 1 — the issue's snippet, with a dashed-format control added**

Saved as `repro_53.py` in the repo root:

```python
from safety.pii_scrubber import PIIScrubber
s = PIIScrubber()
print('scrub  :', repr(s.scrub('Call me at (555) 123-4567 or 555-123-4567')))
print('detect par@:', s.detect('Call me at (555) 123-4567'))
print('detect dash:', s.detect('Call me at 555-123-4567'))
```

```
$ .venv/bin/python repro_53.py
scrub  : 'Call me at (555) 123-4567 or [REDACTED]'
detect par@: []
detect dash: [{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
```

(structlog `pii_detected` log lines on stdout trimmed.) The dashed number is redacted and detected; the parenthesized number is left in the clear and `detect()` returns an empty list for it.

**Step 2 — the tests marked for #53**

Those tests carry `@pytest.mark.xfail(strict=True, reason="issue #53: ...")`, so a plain run reports them as xfailed, not failed:

```
$ .venv/bin/pytest tests/unit/test_pii_scrubber.py -q -rxX
..xx.......x.....x....x..                                                [100%]
XFAIL ... test_us_phone_number_redaction - issue #53: ...
XFAIL ... test_us_phone_formats - issue #53: ...
XFAIL ... test_detect_phone_pii - issue #53: ...
XFAIL ... test_phone_at_start_of_text - issue #53: ...
XFAIL ... test_mixed_pii_and_text - issue #53: ...
20 passed, 5 xfailed in 0.04s
```

**Step 3 — the four named tests with the marker disabled, to show the real assertion**

```
$ .venv/bin/pytest tests/unit/test_pii_scrubber.py --runxfail -q --tb=line \
    -k 'test_us_phone_number_redaction or test_us_phone_formats or test_detect_phone_pii or test_phone_at_start_of_text'
tests/unit/test_pii_scrubber.py:43: AssertionError: assert '[REDACTED]' in 'Call me at (555) 123-4567'
tests/unit/test_pii_scrubber.py:62: AssertionError: assert '[REDACTED]' in 'Contact: (555) 123-4567'
tests/unit/test_pii_scrubber.py:140: assert 0 > 0
tests/unit/test_pii_scrubber.py:200: AssertionError: assert '[REDACTED]' in '(555) 123-4567 is my phone number.'
4 failed, 21 deselected in 0.01s
```

All four fail on exactly the parenthesized form: `scrub()` leaves it unchanged and `detect()` returns no match for it.

**Expected vs actual**

- Expected: `(555) 123-4567` comes back as `[REDACTED]` from `scrub()`, and `detect()` returns a `phone_us` entry for it, the same as the dashed form.
- Actual: the parenthesized number passes through unchanged and `detect()` returns `[]` (Step 1); the four named tests fail on that when the xfail marker is disabled (Step 3). The dashed form works, which is the control.

**One note on the fifth marked test**

`test_mixed_pii_and_text` also carries the `reason="issue #53: ..."` marker, but with the marker disabled it fails for a different reason — over-redaction, not a missed phone number:

```
$ .venv/bin/pytest tests/unit/test_pii_scrubber.py --runxfail -q --tb=short -k test_mixed_pii_and_text
tests/unit/test_pii_scrubber.py:254: in test_mixed_pii_and_text
    assert "Python" in scrubbed
E   assert 'Python' in "... I worked at TechCorp for [REDACTED]ications. ..."
```

`detect()` shows the culprit is the `street_address` pattern, not `phone_us`:

```
$ .venv/bin/python -c "from safety.pii_scrubber import PIIScrubber; print(PIIScrubber().detect('I worked at TechCorp for 5 years developing Python applications.'))"
[{'type': 'street_address', 'value': '5 years developing Python appl', 'start': 25, 'end': 55}]
```

So a phone-only fix would make the four named tests pass but not this fifth one; its marker can't be dropped alongside the others unless the `street_address` match is addressed too. I haven't touched any of the repo's code yet — flagging it so it can be confirmed whether that over-redaction belongs to #53 or should be split out.

Next I'll read the `phone_us` pattern on line 16 of `safety/pii_scrubber.py` and post what I find before proposing a fix.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Five full runs, in order: **18/20 → 19/20 → 19/20 → 20/20 → 20/20**, every run above the
18/20 bar.

1. First full run — **18/20**. Two clear-accepts, `pkg-05` and `pkg-12`, were rejected on
   `steps-rerunnable`.
2. Full run after rewriting `steps-rerunnable` — **19/20**. `pkg-05` and `pkg-12` flipped to
   accept; `pkg-03` (a clear-accept) now rejected on `claims-within-evidence`.
3. Full run after rewriting `claims-within-evidence` — **19/20**. `pkg-03` flipped to accept;
   `pkg-09` (an honest cannot-reproduce) now rejected on `steps-rerunnable`.
4. Full run after a third `steps-rerunnable` revision (the non-reproduction clause), then a
   three-run stability sweep — **20/20 on all three**, zero flips, categories
   `clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`.
5. Final committed full run — **20/20**. This is the run saved as `eval-run.txt`; its agreement
   line reads `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

**Package analysis**

`pkg-09` — an honest *cannot-reproduce* package (gold category `clear-accept`). My rubric now
decides **accept**, matching the gold label. It did not at first: run 3 rejected it on
`steps-rerunnable`.

The report tries to reproduce an argument-size flush-ordering bug, shows the exact command and
the resulting `order.log` (`ONE` ×3 then `TWO` ×3), and states plainly "I could NOT reproduce",
noting the padded wrapper string that *would* make the second batch hit the limit first was only
described, not run. The grader read my then-current `steps-rerunnable` wording — which required
that "every input that determines the claimed behavior" be specified — and failed it, because
the triggering input the author could not find was unspecified. But for a report whose honest
conclusion is non-reproduction, that is backwards: the author could not find the trigger — that
is the result. The check should ask whether the attempt *actually made* is re-runnable (it is:
the command and the log are shown), not whether it reached a trigger the author never found. The
gold accepts for exactly that reason, and the rubric's own header already states the intent
("an evidenced cannot-reproduce is a pass"). The miss was in my wording, not the grader's
application of it.

**Check rationale**

The check is `steps-rerunnable`. Its pass condition, quoted exactly as it reads now in
`tools/repro-check/rubric.md`:

> A stranger could re-run the attempt and reach the same artifact from this report plus the
> issue it cites: the triggering command/invocation is shown, and every input that determines
> the claimed behavior is either pasted in the report or fully specified in the referenced issue
> (input strings, offsets, flags, options). No determining input depends on a private or unshared
> repo, config, or file, and no step is "set up the project" hand-waving. An input the report
> leaves unspecified fails this check only when it could change the claimed behavior; an
> unspecified detail that cannot affect the outcome (e.g. the exact contents of a dependency list
> when the bug is message routing) does not. Fails when the trigger is swapped for an adjacent
> one, the commands/inputs are absent, a determining flag or input is omitted, or the input is a
> private artifact a stranger cannot obtain. For a report that honestly concludes it could NOT
> reproduce, this check judges whether the attempt actually made is re-runnable — the command and
> inputs that were run are shown and concrete — not whether that attempt reached the triggering
> input the author could not find; a triggering variant the author describes but did not run is a
> stated limit of the attempt (its genuineness guarded by `artifact-shows-claimed-behavior` and
> `claims-within-evidence`), not an unfollowable step.

It reads this way after two revisions driven by misses. The first draft demanded every input be
*pasted verbatim in the report*; that rejected `pkg-05` and `pkg-12`, where the determining inputs
are supplied by the cited issue (pkg-12 runs "the issue's script verbatim", and the issue gives
both input strings, the offsets and the parser) or are irrelevant to the bug (pkg-05 leaves the
`dependencies:` list unspecified, but the bug is warning-routing to stdout, independent of the dep
list). So I changed it to count inputs the referenced issue provides, and to ignore an unspecified
input that cannot change the outcome. The second revision added the non-reproduction clause from
the `pkg-09` analysis above.

**Trade-offs**

The non-reproduction clause gives something up: it moves the guard on a *genuine* attempt out of
`steps-rerunnable` and onto `artifact-shows-claimed-behavior` (which requires a real attempt whose
artifact backs the non-reproduction) and `claims-within-evidence` (which requires honesty). A lazy
"I couldn't reproduce" with no shown attempt would no longer be caught by `steps-rerunnable` — but
it still fails those two required checks, so the verdict is unchanged. I did not take either
loosening on trust. Before each, I read the recorded `steps-rerunnable` / `claims-within-evidence`
evidence for all nine gold=reject packages: every one fails for a reason the new wording still
catches (a swapped trigger, no commands at all, an omitted determining flag, or a private
artifact) **and** each also fails at least one other required check, so neither loosening can turn
a reject into a false accept. The three-run stability sweep (20/20 each, zero flips) confirms
nothing else moved.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
