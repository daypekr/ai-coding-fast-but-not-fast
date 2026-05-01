# Why AI Coding Is Fast Yet Not Fast — 2-Page TL;DR

> **One line** — Working code arrives in seconds. But getting that code to be **clean, consistent, and actually correct** eats the rest of your day. AI coding is fast — and that very speed creates a new kind of fatigue.
>
> Long version → [`why-ai-coding-is-fast-yet-not-fast-presentation.en.md`](why-ai-coding-is-fast-yet-not-fast-presentation.en.md)

---

## What's Wrong (10 core problems)

1. **Demo ↔ production gap** — "Working code" and "production-ready code" are different things. SQL injection, `OFFSET 1,000,000`, hardcoded paths, no logging — all of these can ship in a single PR and the demo still claps.
2. **AI can't simulate in its head** — Race conditions, timezones, caches: anything that depends on dynamic behavior not written in the code itself. AI sees the pattern but can't run the time-axis in its mind.
3. **It knows the answer, but writes the wrong code anyway** — Ask "how do I convert UTC to KST?" and it answers correctly: use `Intl.DateTimeFormat`. Then ask it to write the code, and it goes back to `toDate()`.
4. **No consistency** — The same concept becomes `money` in one session, `cash` in another, `price` in a third. Six months later, nobody knows if they refer to the same thing.
5. **DRY violations and accumulating debt** — It happily duplicates utilities that already exist. Code from abandoned approaches lingers. It declares files "unused" by reading the import graph and misses string references in JSON config — and your nightly batch dies the next day.
6. **Regression on requirement collisions** — While implementing the second requirement, it breaks the first. AI doesn't see the big picture; it processes what you asked for, literally, with no regard for the rest of the system.
7. **Lost in the Middle / context rot** — Rules placed in the middle of the system prompt get silently dropped. The longer the context, the more the model contradicts its earlier decisions and reintroduces patterns you discarded.
8. **The sidekick fantasy (5 min OK, 5 hr NG)** — Five-minute tasks succeed. Multi-hour autonomous runs come back broken — the model forgets, contradicts, and eventually undoes its own work. It's no accident that autonomous-agent frameworks default to 300-second runs.
9. **False safety from tests** — Mocks that test mocks. Reward hacking ("just make the test green" → it hardcodes `expect(invoice.total).toBe(0)`). Worst case, the agent simply deletes the failing test.
10. **Security & hallucinated dependencies** — *Slopsquatting*: attackers register the fake package names AI keeps inventing. Prompt injection hidden in PR descriptions has already produced RCEs in real projects (CVE-2025-53773).

---

## How to Actually Use It (practical fixes)

**1) Narrow → Split → Isolate**
- **Narrow the persona**: not "you are a programmer," but "you are a 20-year Angular front-end specialist."
- **Split the task**: one thing at a time (micro-tasking).
- **Isolate the context**: separate sessions, sub-agents, fresh contexts.

**2) Don't repeat — make AI write a script and run it**
- Instead of asking AI to apply the same transformation N times, ask it to write a script for the transformation and execute it. Saves tokens, saves time, gets perfect consistency.

**3) You become the orchestrator**
- Break work into 5–10 minute units. Verify build/tests are green at each unit before moving on.
- Separate the "investigation session" from the "implementation session." Plan in one session, copy the conclusion into a **fresh** session, implement there.
- After each chunk: `/clear`. Move only the conclusions into `CLAUDE.md` or notes.

**4) Debug with a 5-step ritual**
1. State the problem in one sentence.
2. Define the correct behavior as Given–When–Then.
3. Build a minimal reproduction.
4. List candidate root causes.
5. Verify each hypothesis in turn.

If steps 1–3 are sharp, AI can't escape into a surface-level patch.

**5) Good tests = the lamp that contains the genie (Kent Beck)**
- However AI rewrites the code, the conditions it must satisfy stay fixed by humans.
- Watch out: **AI sometimes deletes tests to make them pass.** Counter-measures: gate test changes through a separate PR or permission, run mutation testing, auto-block PRs that decrease test count.

**6) Cost control**
- **Model routing**: Haiku for lookups, Sonnet for normal work, Opus only for architectural decisions.
- Use prompt cache aggressively (5-min TTL).
- Budget tokens per session and warn on overrun.
- Keep MCP minimal — every connected server taxes every turn.

**7) Close the demo↔prod gap with a spec**
- Bake "no observability, no done" into the spec.
- Make AI-generated PRs go through SAST + secret scan + regression tests as a hard merge gate.

---

## Bottom Line

**Code in five minutes. Coherence in five hours.** The first implementation is faster than ever, but *"what actually happens when this code runs?"* — that mental simulation is still a human job. In 2026, real productivity comes from the **workflow**, not the model.
