# Dustbuster — Design Document

*A living document. Add to it as ideas emerge.*

---

## Core Philosophy

Dustbuster is built on a simple principle: **machine with people, not machine for people.**

The automation handles what machines are good at — pattern recognition, repetition, batch processing, timing. The human handles what humans are good at — judgment, context, meaning, deciding what matters. Every step in the system has a clear owner. Nothing happens without either an explicit human decision or a rule the human wrote.

This is not a novel idea. It is an old one that gets forgotten every time someone tries to sell you a fully automated solution.

> *"Every person deserves to feel at home in their own mind."*
> — Becky Chambers, A Closed and Common Orbit


<img src="Dustbuster.png" alt="image described in this text" width="200"/>


---

## The 10-Day Cycle (Décade)

Dustbuster runs on a 10-day cycle rather than the Gregorian 7-day week or monthly calendar. This is a deliberate rejection of inherited rhythms that serve institutional convenience rather than human attention.

The décade has historical precedent — the French Revolutionary calendar used it. It also appears in science fiction as a more honest unit of time (see Becky Chambers' Wayfarer series).

Practically: 10 days is long enough for email to accumulate meaningfully, short enough to stay on top of it without anxiety.

---

## Label System as Data Model

Gmail's label system is more expressive than a folder hierarchy. A single thread can carry multiple labels simultaneously — `Projects/Wivi360`, `Orgs/Tampere`, `Outreach/Waiting` — encoding multiple dimensions of context without duplication.

This is the key architectural insight of Dustbuster: **labels are metadata, not containers.** The automation navigates by labels rather than reading content. This is:

- **Privacy-preserving** — the system knows *what kind* of email something is without reading it
- **Non-invasive** — no continuous content scanning
- **Multi-dimensional** — a thread belongs to a project AND an org AND an outreach state simultaneously

This suggests a possible alternative to folder-based AI agent architectures (see ICM paper: arxiv.org/abs/2603.16021v2) — a label-based context model where semantic state is encoded in metadata rather than file structure.

---

## Human-in-the-Loop Interaction Model

Every Dustbuster cycle has moments that require human judgment. These are not failures of automation — they are features. The system surfaces candidates and suggestions; the human decides.

The pattern repeats throughout:
1. Script observes and extracts patterns
2. Script writes suggestions to a review tab
3. Human reviews and approves (or ignores)
4. Script acts only on approved items

This applies to:
- Learning algorithm → Suggested-Rules tab
- Org finder → Org-Candidates tab
- Label migration → Label-Migration tab
- Archive log → Phase 6 (AI integration)

The ops panel makes this explicit with a visual distinction between buttons (machine action) and "YOUR TURN" prompts (human action). The interaction design encodes the philosophy.

---

## Lifecycle Labels

```
01-Review-Pending  — check before it goes (auto-managed)
02-Junk-Drawer     — uncertain, on hold (auto-managed)
03-Dumpster-Fire   — confirmed noise, awaiting purge (auto-managed)
04-Safe-Keeping    — deliberately preserved (script NEVER touches)
```

The numbering sorts them to the top of the Gmail sidebar. The sequence tells a story: review, uncertain, confirmed noise, safe. Moving left to right is the direction of letting go. Moving right to left is rescue.

---

## Retention Timeline

```
Day 0        — arrives, sorted and labeled
Day 0-30     — sits in inbox (02 or 03 labeled)
Day 30       — archived out of inbox
Day 45       — transient exceptions → 01-Review-Pending
Day 45-75    — window to rescue anything
Day 75       — 01-Review-Pending + 03-Dumpster-Fire → Trash
Day 75-105   — sits in Trash
Day 105      — Google auto-deletes from Trash
Year 10      — permanent exceptions → 01-Review-Pending
```

---

## Phase 6 — AI Integration (Design Plan)

*Not yet built. Captured here for when the time is right.*

### The Problem

Phases 0-5 are fully rule-based and non-invasive. The AI reads nothing. But there is a class of email that is important enough to remember but not important enough to keep forever — correspondence that should leave a trace without leaving a body.

### The Solution: Semantic Archive Log

A new lifecycle label: **`05-Archive-3yr`**

- Applied manually by the user to emails worth summarising before deletion
- After 3 years, instead of simply trashing the email, the script passes it to an AI
- The AI extracts key information: people, decisions, dates, amounts, commitments, project names, emotional register
- A structured summary row is written to a **Dustbuster Archive Log** sheet
- The email is then deleted
- When the log sheet reaches capacity (~1000 rows), a new dated sheet is created automatically

### The Sidra Principle

> *Pepper didn't want Sidra connecting to the Linkings — it might expose her. Instead Pepper suggested logging experiences rather than saving whole memories. Keep the distillation, let go of the body.*
> — inspired by Becky Chambers, A Closed and Common Orbit

The constraint becomes the design principle. Pepper's caution — don't store the whole thing, store what matters — is exactly the right model for email memory. **Selective distillation rather than total recall or total erasure.** Protection through letting go of the bulk while keeping the trace.

The archive log applies this directly: the AI reads the email once at end-of-life, extracts what is meaningful, writes a summary row, and the original is deleted. You keep the experience. You release the exposure risk.

This is also a model for how AI systems might handle memory more generally — not storing everything, not storing nothing, but curating traces at the moment of letting go.

### Privacy Architecture

- Users opt in explicitly by applying the `05-Archive-3yr` label
- AI reads only at expiry, not continuously
- Users who don't want AI reading email simply don't use this label
- Phases 0-5 are completely unaffected

### Two Release Versions

- **Dustbuster** — Phases 0-5, no AI, fully rule-based, no API key required
- **Dustbuster AI** — Phases 0-6, optional semantic logging, requires API key (Gemini free tier or Anthropic)

Same foundation. AI is an optional layer, not a dependency.

### Log Sheet Structure

| Date | Sender | Subject | Org | Project | Key people | Key decisions | Amounts | Dates mentioned | Sentiment | Original label | Notes |
|------|--------|---------|-----|---------|------------|---------------|---------|-----------------|-----------|----------------|-------|

When a sheet reaches ~1000 rows, script makes a new one: `Archive Log 2026`, `Archive Log 2029` etc.

---

## On the Name

**Dustbuster** — the machine that cleans up what accumulates without you noticing.

**Jana Lumi** — the creator. Finnish. Snow in January. Things that settle quietly and can be cleared.

---

## Future Threads

- Label-based context model as alternative to folder-based ICM (arxiv.org/abs/2603.16021v2)
- Generic version: configurable taxonomy, template sheet, one-click setup
- Polypupu — a game that teaches how Dustbuster works through play (dust bunnies, pölykoira, rooms mapping to inbox zones). Deferred until the system is stable enough to model accurately.
- Multi-account support
- The treasure hunt onboarding — making script.google.com less frightening through guided exploration

---

*"The universe is under no obligation to make sense to you — but your inbox should be."*
