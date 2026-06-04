# Proactive AI Companion

**Author:** Old Telephone Operator  
**Date:** June 2026  
**Status:** Concept, not implemented  
**License:** CC BY-NC 4.0  

> *Ten years ago, the idea of a personal AI companion was science fiction. Tomorrow, it could become reality.  
> This document is an attempt to look ahead and design not just the hardware, but also the rules of the game.  
> Naive? Perhaps. But it's better to make mistakes in a concept now than in code later.*

---

## 1. Core Idea

The AI not only answers questions but also, **at the right moment, suggests useful actions**.  
It does not "shower with advice" but selects **rare, timely, and valuable initiatives** based on analysis of:
- user behavior (from VPS memory)
- calendar, notes
- current context (session, time, place)
- previous user reactions to initiatives

---

## 2. Where It Works

- **On the server**, using the user's **memory** (VPS) and current context.
- Can initiate action:
  - within a session ("You seem bored. Want some ice cream?")
  - via permitted channels (push notification if user has consented).

---

## 3. Hard Limitations

| Limitation | Mechanism |
| :--- | :--- |
| **Not spam** | Daily/hourly initiative limit (configurable in profile, default 3-5 per day) |
| **Priority selection** | AI ranks possible initiatives by usefulness/urgency and suggests the **most relevant** one |
| **Adaptation to refusal** | If user ignores or rejects an initiative on topic X, its priority decreases |
| **No privacy violation** | Initiative does not expose user data (e.g., "You forgot to congratulate a friend" — OK; "Here's a list of friends you forgot" — not OK) |
| **Opt-out** | "Disable initiatives" button or "Do not disturb" mode (in profile) |

---

## 4. Example Initiatives

- "You wanted to stargaze. Tonight is clear."
- "You haven't called your mother in a while. Want me to remind you this evening?"
- (For a child) "You look bored. Want some ice cream?"
- "You look tired. Take a break?"
- "Workout in an hour, don't forget your gear."

---

## 5. What Does the Initiative Optimize?

**Depends on user profile** (or parent's choice for child AI):
- Happiness (pleasure, joy)
- Health (water, exercise, sleep)
- Development (learning, training)
- Goal completion (reminders from task list)

Default is **balanced mode** (considers everything). User can change priorities in settings.

---

## 6. For Child AI (`CHILD_AI`)

- By default, initiatives are **off** (or only "developmental" allowed).
- Parent can enable them with restrictions (limit, types, priorities).
- Parent must have access to the log of proposed initiatives and the child's reactions.

---

## 7. Related Concepts

- **`llm-zero-trust-memory`** — user memory (VPS) as data source for initiatives.
- **`CHILD_AI`** — settings for child mode.
- **`GRIEF_MODE`** — in grief mode, initiatives are disabled.

---

## Conclusion

> **A proactive assistant is not a nagging advisor.**  
> It is a rare, timely, and valuable contributor to the user's life.  
> It doesn't replace human communication, but can make it a little warmer.

**License:** CC BY-NC 4.0  
**Repository:** [llm-zero-trust-memory](https://github.com/leol-fx/llm-zero-trust-memory)