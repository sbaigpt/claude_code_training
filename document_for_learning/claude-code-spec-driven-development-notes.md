# Claude Code — Spec-Driven Development (Revision Notes)

> A core pillar of **agentic coding**. Understanding it is exactly what separates *vibe coding* from *agentic coding*.

---

## 1. Vibe Coding (the problem it solves)

**What it is:** a modern style where — instead of planning up front — you build software by chatting with an AI assistant in a fast, conversational, experimental way.
- Tools: Cursor, Lovable, Replit. You describe in plain English (*"build me a to-do app"*), AI codes it, you check, give feedback, iterate.
- Great for **non-technical users** and for **starting/learning** programming.

**The core problem: you lose control.** The AI makes crucial decisions for you.
> *Example:* "Build me a user authentication system" forces the AI to silently decide — which framework? JWT or sessions? password rules? lockout after 3 failed attempts? — and its choices may not match what you wanted.

**Result:** you get code **fast**, but maybe not the **right** code → you end up in a **loop of corrections and patches**. Frustrating on big apps.

---

## 2. Spec-Driven Development (SDD)

**The idea:** you keep most of the control. Every core decision that should be settled *before* coding is answered by **you** and handed to the AI as a **spec document**. The AI just studies it and codes — no guessing.

> **Definition:** a development approach where a detailed **spec document is written before any code**. The spec is the **single source of truth** for what the system should do, and all development flows from it.

**Philosophically opposite to vibe coding:**

| | Vibe Coding | Spec-Driven Development |
|---|---|---|
| Trade-off | Lose control → faster code | Slower → but lots of control |

---

## 3. What a Spec Document Contains

Format varies, but ~6 things are always there:

1. **Problem statement** — *why* you're building this (the WHY).
2. **Functional requirements** — the exact things the feature does (the WHAT).
3. **Input / output behavior** — how it takes input and what it returns.
4. **Constraints** — limits on the system (performance, screen sizes…).
5. **Edge cases** — where it can fail and how to handle each.
6. **Acceptance criteria** — the task is "done" only if all these are met.

<details>
<summary><b>Worked example — Chat History Sidebar</b></summary>

- **Problem:** users create many conversations but have no easy way to revisit/continue them → add a chat-history sidebar.
- **Functional reqs:** show a sidebar list of past chats; short readable title per chat; auto-generate title from the user's first message; click any chat to open it.
- **Input/output:** input = user clicks a past chat → output = that chat loads in the main area.
- **Constraints:** loads within ~1s; works on standard laptop screens; titles short/readable; handles a reasonable number of chats smoothly.
- **Edge cases:** no chats yet → *"No chat history yet"*; chat can't load → error message; very long first message → use only the first part for the title.
- **Acceptance criteria:** user sees their past chats; correct conversation shown every time; new chat appears automatically after first use.
</details>

---

## 4. The SDD Workflow (end-to-end)

1. **Create a spec document** (non-technical — WHY & WHAT).
2. **Review** it (catch anything missed before moving on).
3. **Create a technical design plan** (the HOW — how to turn the spec into code).
4. **Review** the technical design plan.
5. **Extract tasks** from the design plan (what exactly to build).
6. **Build** (code) — a single agent, or multiple **sub-agents in parallel**.
7. **Validate** against the spec's acceptance criteria.

---

## 5. Technical Design Plan (the "HOW" doc)

Converts the spec into an implementation plan. Typically contains:
- **Objective**
- **Tech stack decision** (*e.g. React for UI, FastAPI for a high-concurrency chat system, relational DB to store chats*)
- **High-level architecture** (frontend → backend → database)
- **Data model** (how the DB is organized)
- **Example / boilerplate code** (so coders aren't confused)
- **Core design decisions**
- **Functional flows** (e.g. how "load sidebar" works, which API it hits)
- **Development plan**

### Spec doc vs Technical design plan
| | Spec document | Technical design plan |
|---|---|---|
| Level | High-level, WHY & WHAT | HOW — tech stack, implementation |
| Technical? | No | Yes |
| Written by | Product managers (with eng) | Engineering team, for the coders |

**Why two separate docs?** So you can switch tech stacks without rewriting the spec. One tech-free spec → **many** technical design plans for different stacks.

### Tasks example (from the plan)
Database & models → Backend (endpoints) → Frontend (UI components) → Integration.
→ These get uploaded to **Jira** and split among developers — mirroring how real dev teams work.

---

## 6. Vibe Coding vs SDD — Full Comparison

| Aspect | Vibe Coding | Spec-Driven Development |
|---|---|---|
| **Starting point** | Rough idea via a prompt | Written spec, everything defined |
| **Who decides requirements** | AI, based on your ask | You, the programmer |
| **Control** | Little (AI has more) | You lead the whole process |
| **Speed** | Very fast | Slower (spec first) |
| **Code quality** | Unpredictable | Consistent & traceable |
| **Best for** | Prototypes, exploration, side projects | Serious production systems |
| **Failure mode** | You can't understand the generated code | AI over-engineers |
| **Debugging** | Spot error → ask AI to fix | Refer to spec → check output matches |
| **Need to know code?** | No | **Yes** — you must understand the language to write/guide the spec |

**Agentic coding** = a paradigm that uses SDD to build features, *plus* other tools (sub-agents, etc.). SDD is **one part** of it.

---

## 7. How This Playlist Uses SDD (with Claude)

Normally teams write all three docs manually. Here, **Claude does the heavy lifting**, you stay the reviewer:

1. **Spec document** → generated by Claude (via a custom slash command, future video). *You review.*
2. **Technical design plan** → generated by Claude using **Plan Mode** (next video). *You review.*
3. **Tasks** → Claude auto-creates them from the design plan.
4. **Coding** → single agent *or* parallel sub-agents.
5. **Validate** against the spec (and optionally write tests).

### Tight Git / GitHub integration (per feature)
1. **Pull** the latest code from git.
2. **Create a new feature branch** and switch to it.
3. Run the whole SDD flow **inside that branch** (spec → review → design plan → review → code → validate).
4. **Commit** the changes.
5. **Push** to git.
6. Open a **PR**, **merge** into the main codebase.
7. **Delete** the feature branch.
8. **Switch back** to main.

*Every feature from the next video onward follows this exact flow.*

---

## One-line takeaway
Vibe coding = hand control to the AI for speed; **Spec-Driven Development = write a detailed spec (single source of truth) first, so the AI implements your decisions instead of guessing** — slower, but consistent, traceable, production-grade. It's the backbone of agentic coding.
