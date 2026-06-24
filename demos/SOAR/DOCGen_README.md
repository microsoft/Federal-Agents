# SOAR_DocGen — Document Generation Agent

SOAR_DocGen is the document‑generation companion to **[SOAR](README.md)**. It is a **worker agent**, not a conversational one — it receives a structured request, gathers the data, renders the document, and files it in the correct sortie folder. It returns a terse success or error message and nothing else.

SOAR determines *when* documents are needed and invokes SOAR_DocGen with a request like:

```
Generate WeatherBrief for VIPER Flight 1
Generate MissionBrief for THUNDER Flight 2
Generate all documents for HAWG Flight 1
```

---

## Why a Two‑Agent Design?

Everything SOAR_DocGen does — querying the workbook, calling the weather service, generating Word documents, filing them in OneDrive — could technically live inside a single, larger SOAR agent. We deliberately split it into two agents for a different reason: **to demonstrate how multiple Copilot Studio agents can work together as a system.**

### Benefits of the multi‑agent approach

- **Keeps the planner conversational and responsive.** Document generation is the slowest part of the workflow — it can involve multiple Excel reads, a weather call, an HTML render, a Word MCP create, and a SharePoint move. By handing that work off to SOAR_DocGen, the goal is for **SOAR to return control to the planner quickly** ("Generating the weather brief now — anything else you want to work on?") rather than freezing the conversation while a document is being built. The planner stays in flow; the document arrives when it's ready.
- **Separation of concerns.** SOAR is conversational, opinionated, and decision‑oriented — it talks with the planner, enforces rules, and decides *what* needs to happen. SOAR_DocGen is silent, deterministic, and execution‑oriented — it takes a structured request and *does* the work. Each agent stays focused on a single responsibility.
- **Connected‑agent invocation.** SOAR_DocGen is registered as a connected child of SOAR. SOAR calls it the same way it would call any other tool, passing a short structured prompt and getting back a terse result.
- **Smaller, sharper instructions.** Each agent's system prompt only has to describe its own job, which keeps the instruction set focused and easier to reason about. Conversational rules don't pollute the document generator, and HTML/template details don't bloat the planner.
- **Independent lifecycles.** Document templates, MCP integrations, and filename/folder logic can evolve inside SOAR_DocGen without touching the conversational core. Likewise, planning rules and modes in SOAR can change without rewriting the document generator.
- **Reusability.** A second consumer (a different orchestrator, an automation, or a future agent) could invoke SOAR_DocGen without inheriting any of SOAR's conversational logic.
- **Easier to debug and improve.** Issues with document content, formatting, or filing are isolated to SOAR_DocGen. Issues with planning logic, callsign rules, or mode behavior are isolated to SOAR. Each agent can be tested, tuned, and rolled out independently.

If you only need a quick demo, a single‑agent build would be perfectly viable. The two‑agent layout exists specifically to model the orchestrator + worker pattern that real multi‑agent systems use — and to keep the human planner unblocked while the back‑end work happens.

---

## What SOAR_DocGen Does

- **Parses** the incoming request for a sortie identifier (`[CALLSIGN] Flight [NUMBER]`) and a document type
- **Queries** the SOAR data workbook for the sortie, crew, aircraft, base, and operation details
- **Generates** formatted Word documents from inline‑styled HTML templates
- **Files** each document in a per‑sortie folder, creating the folder structure as needed
- **Returns** the result path or a specific error

### Document Types

| Type | Contents | Prerequisites |
|------|----------|---------------|
| **WeatherBrief** | Mission info, current conditions, forecast, flight‑risk assessment | Base assigned |
| **MissionBrief** | Crew assignments, comms plan, ordnance, mission data | Crew + aircraft assigned, status Planned+ |

When asked for **"all documents"**, it generates WeatherBrief → MissionBrief in that order.

---

## Tools SOAR_DocGen Uses

| Tool | Purpose |
|------|---------|
| **Excel Online (Business)** | Reads sortie, mission, crew, aircraft, base, and operation data (list rows) |
| **Get Realtime Weather** | Current conditions and forecast for the departure base |
| **WorkIQ Word MCP** | Creates the Word document from HTML (`CreateDocument`) |
| **WorkIQ SharePoint MCP** | Creates Operation/Sortie folders and moves the document into place |

### Data Tables

`Sorties`, `Missions`, `Operations`, `Personnel`, `Aircraft`, `FlightPositions`, `Bases`

---

## File Naming & Placement

**Filename convention (strict):**

```
<DocType>_<Callsign>_<FlightNum>_<DDMMMYYYY>.docx
```

Examples: `Weather_Brief_VIPER_1_15JUN2026.docx`, `Mission_Brief_THUNDER_2_16JUN2026.docx`

**Folder structure** — documents are filed in a per‑sortie folder nested under their operation. SOAR_DocGen creates these folders automatically if they don't already exist:

```
SOAR/
└── Missions/
    └── <Operation>/                    ← created as needed (e.g., Red_Fury)
        └── <Callsign>_<Flight>_<Date>/ ← created as needed (e.g., VIPER_1_15JUN2026)
            ├── Weather_Brief_VIPER_1_15JUN2026.docx
            └── Mission_Brief_VIPER_1_15JUN2026.docx
```

### How a Document Is Saved (Two‑Step Process)

Word MCP creates the file in the OneDrive root, then SharePoint MCP relocates it:

1. **Create** — Word MCP `CreateDocument` renders the HTML and drops the file in the OneDrive root
2. **File it** — SharePoint MCP resolves the Operation/Sortie path, creates any missing folders, and moves the file into `SOAR/Missions/<Operation>/<Sortie>/`

> ℹ️ **Why two steps?** At this time, the **WorkIQ Word MCP server can only create content in the user's OneDrive root folder** — it does not accept a target subfolder when generating a document. SOAR_DocGen works around this by letting Word MCP drop the file in the root, then immediately handing it off to the SharePoint MCP server to move it into the correct `Missions/<Operation>/<Sortie>/` folder. This is a limitation of the current Word MCP service and may change in future versions.

---

*SOAR_DocGen is invoked by **SOAR** and is not used directly by end users. Both agents must be published in your environment for document generation to work — see the SOAR [POC-SETUP.md](POC-SETUP.md).*
