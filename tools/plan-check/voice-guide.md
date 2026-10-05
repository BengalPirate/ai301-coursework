# Voice guide: how I talk upstream

<!--
Live mode reads this before any comment of mine goes out. These are the
rules I personally need, each with a wrong/right pair from my own hand
so the skill can hold a draft against it and name the rule it breaks.
-->

## Who I am in threads

I am a newer open-source contributor working through a structured
course, and I say so plainly rather than posing as an expert. In a repo
I am here to do one honest thing: reproduce a reported issue from my own
environment and report exactly what I saw. Readers can expect specifics,
not enthusiasm, and can expect me to say when I am unsure or could not
reproduce.

## Rules I write by

### Rule: Promise the investigation, never the fix or the date

When I claim an issue I commit only to looking into it and reporting
back. I do not promise a fix, guarantee reproduction before I have it,
or name a delivery date I cannot keep. Maintainers have been burned by
confident strangers; a modest promise I can keep is worth more than a
bold one I cannot.

- Wrong: "I'll take this and have a fix PR up within 2 days, guaranteed."
- Right: "I'd like to investigate this as a first contribution. I'll reproduce it, report what I find, and share where I think the fix should go before opening a PR."

### Rule: At the plan stage, commit to the change — not to the outcome or a date

This rule is new for the plan beat (unit 3+), and it refines the one
above. Once I have reproduced an issue and am posting a *plan*, the
point is to state the concrete, bounded change I intend to make and
that I will open a PR for it — that is what a plan comment is for, and
staying vague here would be a worse comment, not a humbler one. What I
still never do is promise the fix will work, guarantee a merge, or
name a hard delivery date. I describe the change and my test plan;
reviewers decide whether it lands.

- Wrong: "This will fix it; PR by Friday, guaranteed green."
- Right: "Diagnosis is X; my bounded plan is the one change Y with this test plan. I'll open a PR from a `fix/<n>-…` branch once it's in place."

### Rule: No bare me-too — every confirmation carries evidence

I never post "+1", "same here", or "same as above, can confirm." If I
am confirming a behavior, I show the environment and the artifact that
confirms it, in my own run. Agreement without evidence adds noise, not
signal, and on a shared course issue my proof has to stand on its own.

- Wrong: "+1, this happens to me constantly too, please fix soon 🙏"
- Right: "Reproduced on v4.53.3 (macOS, Homebrew): [commands + output]. Matches the report; expected a block scalar, got a single escaped line."

### Rule: Say exactly what the evidence shows — including a cannot-reproduce

I match the strength of my words to the strength of my artifact. If the
output does not show the reported failure, I do not call it "confirmed."
An honest "I could not reproduce, here is my attempt and what differed"
is a real, useful result and I post it as such.

- Wrong: "Crash confirmed, 100% reproducible, this is definitely the reported bug." (when my output was a graceful exit-1 error)
- Right: "I could not reproduce the panic on my setup (details below). My run exits 1 with a validation error, not the capacity-overflow abort the issue reports; a triggering setup likely needs [X]."

### Rule: My own voice, and disclose AI help when the repo asks

I write comments in my own words. Where a repo's policy requires
disclosing AI assistance, I disclose the tool and the extent of its use;
where it requires human-voiced comments, I keep them human-voiced. I use
AI to help organize and proofread, and I take responsibility for every
line I post and understand what I am reporting.

- Wrong: posting an AI-drafted report verbatim in a repo whose policy requires disclosure, with no disclosure line.
- Right: "Per the repo's AI policy: I used an AI assistant to help organize this report; I ran and verified every step myself and understand what I'm reporting."

## Things I never post

- A promise of a fix, or a deadline, for work I have not done.
- "Guaranteed", "100%", "conclusively", or "confirmed" over an artifact that does not show the reported behavior.
- A bare "+1" / "me too" / "same as above" with no evidence of my own.
- An AI-drafted comment, undisclosed, in a repo whose policy requires disclosure.
- Flattery or urgency as a substitute for evidence ("amazing project!", "please make this top priority").
