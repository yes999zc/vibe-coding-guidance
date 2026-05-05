# Vibe Coding Level Guide (English)

> Based on Karpathy's original definition + MenuGen battle report + [Karpathy-Inspired Claude Code Guidelines](https://github.com/forrestchang/andrej-karpathy-skills) (4 behavioral principles) + production engineering practice.

## How to Use

When you load this skill, your AI agent will ask you **3 questions**:

1. **What type of project?** (Prototype / Internal tool / Production SaaS / Infrastructure)
2. **Your technical background?** (Non-developer / Can read code / Senior engineer)
3. **Verification rigor?** (Does it run / Manual QA / Automated tests + CI)

Based on your answers, the agent routes you to the appropriate level.

---

## Routing Logic

```
Q1: What are you building?
├── One-off demo / Prototype → continue to Q2
├── Internal tool / Personal product → L2-L3 (depends on Q2)
├── Production SaaS / User data → L3-L4
└── Infrastructure / Security-critical → Don't vibe code this

Q2: Your technical background?
├── Non-developer / Beginner → L1-L2 (warn about deployment pitfalls)
├── Can read and tweak code → L2-L3
└── Senior engineer → L3-L4 (you can steer)

Q3: Verification rigor?
├── "Does it run?" is enough → L1-L2
├── Manual test checklist → L2-L3
└── Automated tests + CI → L3-L4
```

---

## Level Details

### L1: Pure Vibe

**When to use:** Single-file demos, one-off prototypes, frontend-only proofs-of-concept, personal experiments.

**How it works:**
- Describe what you want in one sentence, let AI generate everything
- Don't review code — evaluate only by running and observing
- Unsatisfied? Rewrite the prompt and regenerate
- No git, no testing, no deployment

**Karpathy principles applied:** Mostly skipped. Speed > quality.
- Think Before Coding → Skip
- Simplicity First → Light — simplify only if it doesn't run
- Surgical Changes → N/A (everything is new)
- Goal-Driven Execution → "I can see it working"

**Limitations:**
- Code is unmaintainable
- Falls apart on deployment (see Karpathy's MenuGen local→deploy gap)
- Never use for user-facing or data-sensitive projects

**Karpathy's words:** *"fully give in to the vibes, embrace exponentials, forget that the code even exists"*

---

### L2: Guided Vibe

**When to use:** Personal tools, internal scripts, single-page apps, light third-party API integrations.

**How it works:**
- Per feature: describe → AI writes → run → iterate prompt
- You can spot obvious errors but don't dive into root cause
- Basic git commits
- You register and configure third-party services; AI writes the calling code

**Karpathy principles applied:**
- **Think Before Coding** — Ask AI to surface assumptions; on key decisions, make it explain
- **Simplicity First** — Watch for: unnecessary features, over-abstraction
- **Surgical Changes** — When editing existing code, tell AI "change only what's needed, don't touch anything else"
- **Goal-Driven Execution** — Give steps: "First do A, verify B works, then do C"

**Gotchas (from Karpathy's MenuGen):**
- API keys go in environment variables, never hardcoded
- Running locally ≠ deploying successfully (Vercel build ≠ local dev)
- Third-party APIs have rate limits — first failure might be throttling, not bugs
- AI may use deprecated SDKs — check official docs when errors appear

**Reference case:** Karpathy's `llm-council` — a 99% vibe-coded Saturday hack.

---

### L3: Hybrid Engineering

**When to use:** Multi-module apps with APIs/DBs/third-party services, team-internal tools, projects needing long-term maintenance.

**How it works:**
- **Human controls architecture, AI fills in implementation**
- Write the technical spec first: architecture diagram, interface definitions, data models, routing
- Break into granular tasks: one task at a time, verify before handing off the next
- Review critical paths yourself (especially security-related)
- Every task has clear input/output definitions + success criteria

**Karpathy principles applied (full):**
- **Think Before Coding** — Force AI to state assumptions + present alternatives before implementation; push back on unreasonable demands
- **Simplicity First** — Scrutinize: any unnecessary abstractions? Any "for future use" code?
- **Surgical Changes** — When reviewing diffs, verify every changed line traces back to the requirement. **If AI touched unrelated code, correct it immediately**
- **Goal-Driven Execution** — Convert every task into a verifiable goal ("write test → pass → refactor → tests still pass")

**The Karpathy trap:** Third-party integration is the single biggest pain point — OpenAI → Replicate → Clerk → Stripe → Vercel KV. Each service has its own account, keys, config, docs. List all services upfront and register them before asking AI to write integration code.

---

### L4: Orchestrated

**When to use:** Production SaaS, multi-team projects, user-data-handling systems, CI/CD-mandatory environments.

**How it works:**
- Multiple AI agents with role specialization (frontend agent, backend agent, testing agent)
- Tasks broken to TDD granularity with automated tests covering critical paths
- CI verification + type checking + linting
- Canary releases + reversible database migrations
- Architecture abstraction layer for vendor independence (don't lock into a single API or service)

**Karpathy principles applied (strict):**
- All 4 principles enforced on every PR
- Each PR undergoes a Karpathy principles review
- **Goal-Driven Execution is the core** — every task MUST have verifiable success criteria; AI loops until they pass

**Karpathy's vision:**
> *"Some app development platform could come with all the batteries included... Something opinionated, concrete, preconfigured with all the basics: domain, hosting, authentication, payments, database, server functions."*

This is what L4 builds toward.

---

## Cross-Level Pitfall Checklist (applies to all levels)

Common AI behaviors that don't discriminate by level:

- [ ] **Side-effect edits** — AI "improves" unrelated code while implementing your feature. Always review the diff.
- [ ] **Fake competence** — AI pretends to know APIs it doesn't. Verify critical paths.
- [ ] **Over-abstraction** — Base class + interface + factory for something used once. Call it out.
- [ ] **Premature progression** — AI writes the next step before verifying the previous one. Enforce validation.
- [ ] **Security negligence** — Hardcoded keys, passwords in logs, email-based user matching. Human-review all security-related logic.

---

## Trade-off Notes

These 4 principles bias toward **caution over speed**:
- L1-L2: Relax enforcement. Optimize for velocity.
- L3-L4: Enforce strictly. Optimize for maintainability.

**You know the guidelines are working when:**
- Diffs contain fewer unnecessary changes
- Reworks from over-engineering decrease
- Clarifying questions come before implementation, not after bugs
