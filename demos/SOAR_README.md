# SOAR — Sortie Optimization & Allocation Resourcing

SOAR is a conversational sortie planning agent for US Air Force training operations. It helps planners build and manage flights end‑to‑end — from creating an operation and assigning crews and aircraft, to checking weather and generating mission paperwork — all through natural conversation.

SOAR acts as an **assistant, not an autonomous planner**: the human planner makes every decision. SOAR gathers data, surfaces options and recommendations, and waits for explicit confirmation before committing changes.

> ⚠️ **Disclaimer:** SOAR is **inspired by**, but is not a faithful representation of, the official US Air Force mission planning process. The workflows, terminology, and rules used here are simplified and in some places adapted to enable demo and training scenarios. SOAR is **not** an authoritative planning tool and should not be used to plan or execute real‑world operations. Always defer to current Air Force doctrine, regulations, and approved planning systems for actual mission planning.

---
## What SOAR Does

- **Plans sorties** using a three‑tier hierarchy: **Operation → Mission (callsign) → Sortie → Flight Positions**
- **Manages crews and aircraft** — checks availability and assigns pilots and jets to flight positions
- **Enforces planning rules** — valid callsigns per mission type, role naming, and status transitions
- **Checks weather** for departure bases during planning
- **Generates documents** — hands off to **SOAR_DocGen** to produce weather briefs, mission briefs, and maps
- **Answers questions** about doctrine, terminology, and planning processes from its knowledge source

### Operating Modes

| Mode | Purpose |
|------|---------|
| **Planning** (default) | Concise, operational sortie planning with full read/write access |
| **Doctrine** | Read‑only Q&A about doctrine, processes, and terminology — no record changes |
| **Training** | Plans sorties *and* narrates the reasoning, rules, and constraints behind each decision |

Modes are mutually exclusive and only change when you explicitly ask.

> **Enabling and disabling modes:** Mode switches are driven entirely by what you say — SOAR never changes modes on its own. Planning Mode is the default, so you "disable" Doctrine or Training simply by switching back to Planning.
>
> | To enable | Say |
> |-----------|-----|
> | Doctrine Mode | "Switch to Doctrine Mode" or "Enter Doctrine Mode" |
> | Training Mode | "Switch to Training Mode" or "Enter Training Mode" |
> | Planning Mode (disable the others) | "Switch to Planning Mode", "Return to normal planning", "Exit Doctrine Mode", or "Exit Training Mode" |
>
> While a mode is active, SOAR tags its responses with **[DOCTRINE MODE]** or **[TRAINING MODE]** so you always know where you are. Planning Mode shows no tag.

---

## Tools SOAR Uses

| Tool | Purpose |
|------|---------|
| **Excel Online (Business)** | Reads and writes mission data — list rows, add rows, update rows across the planning tables |
| **Knowledge (SOAR‑Data workbook)** | Semantic search over the workbook to answer natural‑language questions |
| **Get Realtime Weather** | Current conditions and forecast for departure bases |
| **WorkIQ Word MCP** | Creates Word documents |
| **WorkIQ SharePoint MCP** | Creates folders and files the generated documents in OneDrive |
| **SOAR_DocGen** (connected agent) | Worker agent invoked to generate weather briefs, mission briefs, and maps |

### Data Tables

`Operations`, `Missions`, `Sorties`, `FlightPositions`, `Personnel`, `Aircraft`, `Bases`

---

## Your First Sortie

New to SOAR? This walkthrough takes you from a blank slate to a scheduled sortie with documents generated. Just talk to SOAR naturally — you don't need to memorize commands.

### 1. Say hello

Start the conversation to see what SOAR can do:

> **"Hi"** &nbsp;·&nbsp; **"Help"** &nbsp;·&nbsp; **"What can you do?"**

SOAR responds with a capabilities card to orient you.

### 2. Start planning a sortie

Tell SOAR what you want to fly. You can be broad and let it guide you:

> **"Plan a Close Air Support mission for tomorrow"**
>
> **"I need to schedule an Air Superiority sortie for next week"**

SOAR will walk you through the three levels it needs — **Operation**, **Mission/callsign**, and the **Sortie** itself.

### 3. Pick an operation and callsign

SOAR ensures the sortie has a parent operation and a valid callsign. Callsigns are tied to mission type, so SOAR offers the valid choices:

> **"Use Operation RED FLAG 26‑2"**
>
> **"Let's use the VIPER callsign"** *(Air Superiority)*

> 💡 If you pick a callsign that doesn't match the mission type, SOAR will tell you and list the valid options.

### 4. Set the details

Give SOAR the flight specifics — it will ask for anything missing:

> **"Flight 1, takeoff at 0800 local, 90 minutes, out of Nellis"**

### 5. Assign crew and aircraft

Check who and what is available, then assign:

> **"Who's available to fly?"**
>
> **"Show me mission‑ready jets"**
>
> **"Assign Maverick as flight lead and Goose as wingman"**

> 💡 SOAR never assigns crew or aircraft on its own — it presents options and waits for your go‑ahead.

### 6. Check the weather

> **"What's the weather looking like at Nellis for the sortie?"**

### 7. Commit the sortie

Nothing is locked in until you say so. When you're ready:

> **"Schedule it"** &nbsp;·&nbsp; **"Lock it in"** &nbsp;·&nbsp; **"Confirm"**

SOAR moves the sortie from **Draft → Planned** and triggers document generation.

### 8. Generate documents

SOAR hands off to **SOAR_DocGen** to produce the paperwork:

> **"Generate the weather brief for VIPER 1"**
>
> **"Create all documents for VIPER Flight 1"**

Documents are filed automatically in OneDrive under:

```
SOAR/Missions/<Operation>/<Callsign>_<Flight>_<Date>/
```

### Quick Reference — Handy Prompts

| Goal | Try saying |
|------|-----------|
| See capabilities | "Help" |
| List operations | "What operations are active?" &nbsp;·&nbsp; "Show me all operations" |
| Operation details | "Tell me about Operation RED FLAG 26‑2" &nbsp;·&nbsp; "What missions are under Allied Anvil?" |
| List sorties | "Show me all sorties for RED FLAG 26‑2" &nbsp;·&nbsp; "What's on the schedule this week?" |
| List missions/callsigns | "What callsigns are in use?" &nbsp;·&nbsp; "Show me the VIPER missions" |
| Browse the roster | "List all available pilots" &nbsp;·&nbsp; "Who's qualified on the F‑16?" |
| Browse aircraft | "List all jets and their status" &nbsp;·&nbsp; "Which aircraft are at Nellis?" |
| Browse bases | "What bases are in the system?" &nbsp;·&nbsp; "Show me the coordinates for Nellis" |
| Start a sortie | "Plan a CAS mission for tomorrow" |
| Check crew | "Who's available to fly?" |
| Check aircraft | "Show me mission‑ready jets" |
| Assign crew | "Assign Maverick as flight lead" |
| Check weather | "What's the weather at Nellis?" |
| Commit | "Schedule it" |
| Generate briefs | "Create all documents for VIPER 1" |
| Learn the rules | "Switch to Doctrine Mode" then "What are the crew rest rules?" |

---

*SOAR works together with **SOAR_DocGen**, its document‑generation companion. Both agents must be published in your environment for the full workflow to function — see [POC-SETUP.md](POC-SETUP.md).*
