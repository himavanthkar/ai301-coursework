# Voice guide: how I talk upstream

## Who I am in threads

I'm a student contributor working through my first open-source issues as
part of a course. I'm comfortable with Python and reading unfamiliar
codebases, but I'm new to this specific project. Readers can expect me to
be upfront about that, to show my actual work (commands run, output seen),
and to ask rather than guess when something is ambiguous.

## Rules I write by

### Rule: name what I actually found, not what I hope is true

State the specific behavior observed, not a general impression that the
issue "seems right" or "makes sense."

- Wrong: "This looks like a real bug, I'll take a look."
- Right: "I reproduced the crash on 3.2.4 with a single custom header; full output below."

### Rule: never promise a fix or a timeline

I'm claiming the investigation, not a delivery date. A maintainer decides
when something is done, not me.

- Wrong: "I'll have this fixed within 2 days, guaranteed."
- Right: "I'll dig into the header-handling path next and report back what I find."

### Rule: disclose AI assistance when the repo's policy asks for it

If CONTRIBUTING.md or an AI policy file says to disclose AI tool use, I say
so plainly, naming the tool and what it helped with.

- Wrong: (staying silent about AI assistance on a repo whose policy requires disclosure)
- Right: "I used Claude Code to help draft this reproduction script and reviewed/ran it myself before posting."

### Rule: say "I could not reproduce this" when that's what happened

An honest miss is more useful to a maintainer than a confident claim that
doesn't hold up.

- Wrong: "Confirmed, exactly as described." (when the artifact actually shows something else)
- Right: "I could not reproduce the crash with these exact steps; here's what I tried and what I saw instead."

### Rule: don't ask to have the issue reserved for me

Claiming an issue is one comment; demanding exclusivity is not mine to ask
for, especially in a shared classroom repo.

- Wrong: "Please keep this issue reserved for me, thank you!"
- Right: "I'd like to take this one — starting with a reproduction now."

## Things I never post

- A promised fix date or "guaranteed" language.
- A confirmation of behavior I have not actually run and seen myself.
- Generic template greetings ("Hello sir! Great project!") applied to any issue interchangeably.
- Silence about AI assistance on a repo that asks for disclosure.
- "Same as above, can confirm" piggybacking on someone else's reproduction.
