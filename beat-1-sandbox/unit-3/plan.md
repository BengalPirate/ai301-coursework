# Plan — issue #53: PII scrubber fails to redact parenthesized US phone numbers

Builds on my reproduction posted on the issue:
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5986071553

## Diagnosis

The `phone_us` pattern in `safety/pii_scrubber.py` (line 16) is:

```
\b(?:\+?1[-.]?)?\(?([0-9]{3})\)?[-.]?([0-9]{3})[-.]?([0-9]{4})\b
```

It allows an optional `(` and `)` around the area code (`\(?` … `\)?`), but
the separators *between* the three digit groups are `[-.]?` — a hyphen or a
dot, or nothing. In `(555) 123-4567` the separator after the area code is
`)` followed by a **space**: `\)?` consumes the `)`, but the space cannot be
matched by `[-.]?`, so the next group `([0-9]{3})` meets a space instead of a
digit and the whole match fails. The dashed form `555-123-4567` matches
because its separators are hyphens.

This is exactly what the reproduction shows. From the posted repro evidence:

> `scrub  : 'Call me at (555) 123-4567 or [REDACTED]'`
> `detect par@: []`
> `detect dash: [{'type': 'phone_us', 'value': '555-123-4567', ...}]`

The dashed number is redacted and detected (the control); the parenthesized
number passes through and `detect()` returns `[]`. The missing space in the
separator class is why the parenthesized form is not matched at all. I probed
the pattern to confirm, and the probe surfaced a second detail the fix must
handle: the pattern begins with `\b` sitting *before* `\(?`, and a word
boundary cannot fall between a space and `(`, so simply adding `\s` to the
separators makes the match start at `555` and leaves the opening `(` outside
the redacted span (`scrub` would return `([REDACTED]`, an incomplete
redaction). The fix therefore has two parts (see Approach): add `\s` to the
separators, and move the `\b` to just after the optional paren so the whole
`(555) 123-4567` is redacted. With both, the parenthesized form is fully
redacted, every format that already worked (`555-123-4567`, `555.123.4567`,
`5551234567`, `(555)123-4567`, `+1 555-123-4567`) still matches, and the
pattern still refuses to match inside a longer digit run (`5551234567890`,
`x5551234567` are left untouched).

## Scope

**In scope:** one change to the `phone_us` pattern — widen its separators so a
single space is accepted (the `(AAA) ` form) and relocate the leading `\b` to
just after the optional paren so the opening `(` is redacted with the number —
and remove the `@pytest.mark.xfail` markers on the four phone tests the fix
makes pass.

**Not in scope:** the `street_address` over-redaction that makes
`test_mixed_pii_and_text` fail. As I noted in the repro, that test carries the
`issue #53` marker but fails for a different reason — the `street_address`
pattern matches `"5 years developing Python appl"` and redacts "Python", which
has nothing to do with phone numbers. It needs a separate fix, so I leave its
marker in place. Also out of scope: any other PII pattern and any refactor of
`PIIScrubber`.

## Files to touch

- `safety/pii_scrubber.py` — the `phone_us` pattern (line 16).
- `tests/unit/test_pii_scrubber.py` — remove the `xfail` marker from the four
  phone tests (`test_us_phone_number_redaction`, `test_us_phone_formats`,
  `test_detect_phone_pii`, `test_phone_at_start_of_text`). Leave
  `test_mixed_pii_and_text`'s marker in place (out of scope, above).

## Approach

1. In `safety/pii_scrubber.py`, change the `phone_us` pattern so its three
   separator classes accept a space (`[-.]?` → `[-.\s]?`, including the
   optional-`1` separator for the `+1 555-...` form), and move the leading
   `\b` from before `\(?` to just after it (`\(?\b`), so the opening paren is
   included in the match. Net change, line 16:
   - from `\b(?:\+?1[-.]?)?\(?([0-9]{3})\)?[-.]?([0-9]{3})[-.]?([0-9]{4})\b`
   - to   `(?:\+?1[-.\s]?)?\(?\b([0-9]{3})\)?[-.\s]?([0-9]{3})[-.\s]?([0-9]{4})\b`
2. Probe the pattern against the formats above (and against digit-run
   non-examples) to confirm the parenthesized form is now fully matched, no
   previously-matching form regressed, and no 10-digit slice of a longer run
   is matched.
3. Remove the `@pytest.mark.xfail(strict=True, reason="issue #53: ...")`
   decorator from the four phone tests. `strict=True` turns a now-passing
   xfail into `XPASS(strict)` (a failure), so the markers must come off as
   part of the fix.

## Test plan

Re-run the repro steps from Unit 2 against the change.

**Before (posted repro, current `main` `2f4e82f`):**
- `scrub('Call me at (555) 123-4567 or 555-123-4567')` → `'Call me at (555) 123-4567 or [REDACTED]'`
- `detect('Call me at (555) 123-4567')` → `[]`
- `pytest tests/unit/test_pii_scrubber.py -q` → `20 passed, 5 xfailed`

**After (expected):**
- `scrub('Call me at (555) 123-4567 or 555-123-4567')` → `'Call me at [REDACTED] or [REDACTED]'`
- `detect('Call me at (555) 123-4567')` → one `phone_us` entry for `(555) 123-4567`
- `pytest tests/unit/test_pii_scrubber.py -q` → the four phone tests pass with
  their markers removed and no `XPASS(strict)`; `test_mixed_pii_and_text`
  remains the one intentional `xfail`. Expected tally: `24 passed, 1 xfailed`.

The observable that distinguishes fixed from not-fixed: the parenthesized
number comes back `[REDACTED]` and `detect()` returns a match for it, and the
four formerly-xfailed tests pass without XPASS.

## Risks and unknowns

- Adding `\s` lets `555 123 4567` (space-separated) match too. That is itself a
  valid US phone format, and the terminal `\b` plus the fixed 3-3-4 group shape
  bound it: I probed `5551234567890`, `x5551234567`, and `call12345678901234`
  and none are redacted, because moving the boundary to `\(?\b` keeps the match
  from starting inside a longer digit run and the trailing `\b` keeps it from
  ending inside one. I will re-confirm the suite's non-phone / non-redaction
  assertions still hold after the change.
- I use `[-.\s]?` (one optional separator char), matching the pattern's
  existing single-optional structure, rather than `\s*`. A mixed form like
  `(555) 123 4567` still works because each gap is a single separator.
- Moving `\b` to `\(?\b` is slightly more than a one-character edit, but it is
  required to fix the stated bug correctly: without it the `(` is left behind
  and the redaction is incomplete. It stays within the one `phone_us` pattern,
  so the change is still bounded to the reported defect.
- The `street_address` over-redaction (the fifth marked test) is a real,
  separate defect; whether the maintainer wants it folded into #53 or split out
  is an open question I raised in the repro. My plan assumes split-out.

## Deviations

Nothing changed. I built the fix exactly as planned: the one `phone_us`
pattern edit (separators `[-.]?` → `[-.\s]?` and the `\b` moved to `\(?\b`)
and the removal of the four phone tests' `xfail` markers, with
`test_mixed_pii_and_text`'s marker left in place. The build produced the
output the test plan predicted — `scrub` returns `'Call me at [REDACTED] or
[REDACTED]'`, `detect` returns a `phone_us` match with value `(555)
123-4567`, and the scrubber suite goes to `24 passed, 1 xfailed` with no
`XPASS(strict)` — so there was nothing to change in the approach.

One note that is not a deviation from the plan: running the entire
`tests/unit` suite in my local venv surfaces 9 collection errors in unrelated
modules (review service, security, the chunkers). Those are `ImportError`s
from dev dependencies my minimal `structlog`+`pytest` venv does not install;
they are present on the unchanged base commit as well, so they are an
artifact of my local setup, not a regression from this change. The
`test_pii_scrubber.py` module, which is what this fix touches, passes in full.
