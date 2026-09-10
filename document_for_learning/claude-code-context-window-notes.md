# Claude Code — Context Window Management (Revision Notes)

> The single most important concept for using Claude Code well. Manage this and you save cost, quality, and headaches.

---

## 1. Context vs Context Window

**Context** = all the information needed to understand something correctly.
In programming, context comes from many places: **codebase, PRD/spec doc, Jira/GitHub issues, Slack discussions, previous AI chats, the git repo.**

**Context window** = how much information (in **tokens**) a model can *see and remember at one time* while generating a response.
→ Think of it as the model's **working memory**.

LLMs can't handle infinite context — there's a hard limit, measured in tokens. That limit *is* the context window.

---

## 2. Key Facts About Claude Code's Context Window

- **~200K tokens** for Sonnet models. *(The newer Opus is reported at ~1M tokens.)*
- **Every new session = a fresh context window.**
- Tokens are consumed by **both** your messages (**input tokens**) *and* Claude's replies (**output tokens**) — including generated code, tool calls, and tool outputs.
- Claude's output is **~6× larger** than what you send → context fills faster than you'd expect.

### The cumulative growth trap (remember this!)
LLMs are **stateless** — they have no memory. So **every new request re-sends the entire prior conversation** as input tokens.

If each message ≈ 100 tokens:
| Turn | Tokens sent that turn |
|---|---|
| 1 | 200 |
| 2 | 400 |
| 3 | 600 |
| … | … |
| 10 | ~2000 (cumulative cost keeps climbing) |

**Takeaway:** the longer a session runs, the faster you hit the limit. Building 4 features in one session ≈ **4× the tokens** of one feature per session.
→ **Give every feature its own session.**

---

## 3. What Actually Fills the Window (you get ~150K usable, not 200K)

Like buying "8GB RAM" but only ~6GB is usable — some space is pre-occupied:

| Component | Approx tokens |
|---|---|
| System prompt (Claude Code's built-in behavior/rules) | ~6K |
| Tool schemas (definitions of each tool) | ~8K |
| `CLAUDE.md` file (project overview, loaded each session) | small |
| Conversation history | grows |
| Tool outputs (verbose) | grows |
| MCP tool schemas (if used) | some |
| Skills (if used) | some |
| **Auto-compaction reserve** | **~33K** |

**Net:** ~50K is reserved up front → you realistically work with **~150K tokens**.

👉 Run **`/context`** to see this live breakdown and your current % usage.

---

## 4. Why It Matters

1. **Cost** — you pay per token. Context-conscious = token-conscious = cost optimized.
2. **Workflow** — 4 features in 1 session = 4× tokens = 4× cost. Structure work around sessions.
3. **Quality** — response quality **degrades as the window fills**. At 120–130K used, answers are noticeably worse than at 20–30K.

---

## 5. Rescue Options When the Window Fills Up

**Auto-compaction (Claude does it automatically)**
- Triggers when you hit **~75–92%** of usable context.
- Summarizes the whole conversation → stores summary in the reserved 33K → frees up history.
- Repeats each time you refill. Eventually the 33K summary space also fills → Claude stops → you must start a **new session**.
- ⚠️ Downside: it can fire **mid-task**, and summaries are **lossy** — important details can get dropped.

**Manual compaction — `/compact`** *(preferred)*
- Does the same thing, but **on your terms**.
- Check `/context` periodically; when you're around **70–75%** and **not mid–important-task**, run `/compact`.
- (Press **Ctrl+O** to view the generated summary.)
- Note: repeated `/compact` also fills the 33K reserve — there's a limit.

**Other exits**
- **Sub-agent** → hand the next task to a sub-agent (fresh 200K window). Works in some cases.
- **`/clear`** → wipes the conversation, back to session start.
- **New session** → cleaner. (`/clear` and new session are similar; `/clear` stays in the same session, new session starts fresh. Preferring a new session pairs well with "one feature per session".)

---

## 6. Sub-Agents (quick preview)

Claude Code is an agent that can spawn **sub-agents** under it.
- Each sub-agent has its **own isolated context window** (separate from the main one).
- Run several in **parallel** for independent tasks.
- They return only a **summary** to the main agent — not all their tokens → very efficient way to manage context.

---

## 7. Best Practices (the checklist)

1. **One feature = one session.** Never mix multiple features in one session.
2. **Use `/compact` proactively**, not reactively — don't rely on auto-compaction.
3. **Write focused, specific prompts.** Vague prompts waste tokens.
4. **Use sub-agents** for isolated/exploratory or parallel work — not the main agent.
5. **Use `.claudeignore`** (like `.gitignore`) to keep big/irrelevant files (build files, `.venv`, etc.) out of context. *(Still improving — not always fully respected yet.)*

---

## 8. Terminal vs GUI (bonus)

Access modes: **terminal, desktop app, web, VS Code plugin.**
- **Terminal is the OG + power-user way** — first to exist, most capable.
- Some features are **terminal-only** (e.g. editing memory, hooks → GUI says *"continue in terminal"*).
- GUI modes exist for **convenience** (e.g. viewing `git diff`), but terminal unlocks full potential.

---

## One-line takeaway
Context window = working memory (~150K usable). It fills cumulatively and fast; keep it lean with **one feature per session + proactive `/compact`**, and quality + cost both stay under control.
