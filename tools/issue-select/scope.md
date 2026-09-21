# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s3`

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

I am most comfortable in Python and JavaScript/TypeScript, and I also
have real experience in Java, C++, Swift, and Kotlin. I have done mobile
app development on both sides — Swift/iOS and Kotlin and Java on Android
— so a native mobile target or a C/C++ codebase does not put me off. I
can read my way around an unfamiliar codebase if the issue names the
files to touch. I have used git and the fork-branch-PR workflow before, but
this is my first contribution to a repository I do not own, so I would
rather land a small, well-specified change than a clever one.

I want to get better at reading a project's existing conventions and
matching them, and at writing tests that a maintainer trusts. Rank
documentation fixes, small bug fixes with a stated reproduction, and
issues whose body names the files or functions involved above
open-ended feature work.

Prefer issues I can finish and verify locally without standing up heavy
infrastructure. Deprioritize anything centered on Rust or on Kubernetes
operators and cluster tooling — those are the two I have little
experience with and would have to learn before I could even reproduce
the bug.
