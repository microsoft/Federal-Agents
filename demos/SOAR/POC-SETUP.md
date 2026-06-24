# SOARLite POC Setup Guide

This document provides setup instructions for deploying SOARLite in a demo tenant.

## Prerequisites

- Microsoft 365 tenant with OneDrive for Business
- Copilot Studio license
- Excel Online connector access
- A free **WeatherAPI.com** account and API key — sign up at https://www.weatherapi.com (free tier is sufficient for the POC)

---

## 1. OneDrive Folder Structure

Create the following folder structure in the **root** of the POC user's OneDrive:

```
OneDrive/
└── SOAR/
    ├── Missions/          ← Generated Word documents (weather briefs, mission docs)
    └── SOARData/          ← Excel workbook and reference data
```

### Steps:

1. Open OneDrive (https://onedrive.com or via Microsoft 365)
2. In the root folder, create a new folder named `SOAR`
3. Inside `SOAR`, create two subfolders:
   - `Missions`
   - `SOARData`

> ℹ️ **You only need to create these three folders.** The agent automatically creates a per-Operation folder and a per-Sortie folder inside `Missions/` as documents are generated, so the structure ends up like:
> ```
> SOAR/
> └── Missions/
>     └── <Operation>/              ← created automatically (e.g., Red_Fury)
>         └── <Callsign>_<Flight>_<Date>/   ← created automatically (e.g., VIPER_1_15JUN2026)
>             ├── Weather_Brief_VIPER_1_15JUN2026.docx
>             └── Mission_Brief_VIPER_1_15JUN2026.docx
> ```

---

## 2. Excel Workbook Setup

Download `SOARLite-Data.xlsx` from the [`assets/`](assets/) folder of this repo and copy it to the `SOAR/SOARData/` folder in your OneDrive.

### Workbook Contents:

| Sheet | Purpose | Sample Rows |
|-------|---------|-------------|
| Personnel | Pilot roster with qualifications | 20 |
| Aircraft | Tail numbers, types, status | 10 |
| Bases | USAF installations with coordinates | 10 |
| Operations | Exercise definitions (RED FLAG, etc.) | 3 |
| Missions | Mission packages within operations | 5 |
| Sorties | Individual flights within missions | 8 |
| FlightPositions | Pilot/aircraft assignments per sortie | 16 |
| Units | Squadron/Wing definitions | 4 |

### Format Each Sheet as a Table

The Excel connector and knowledge indexer both require each sheet to be a named **Table**:

1. Open `SOARLite-Data.xlsx` in Excel Online
2. For each sheet:
   - Select all data (including headers)
   - **Insert** → **Table**
   - Ensure "My table has headers" is checked
   - Name the table to **match the sheet name** (e.g., `Personnel`, `Aircraft`, `Sorties`)

### Important:
- The workbook must remain in `SOARData/` for the knowledge source to function
- Do not rename the file after adding it as a knowledge source
- Table names must match exactly — the rebind step in section 5 depends on the table names being correct

---

## 3. Import the Solution

Import the SOARLite managed solution into your Power Platform environment.

1. Go to **https://make.powerapps.com**
2. Confirm the environment selector (top right) shows your **POC environment**
3. Left nav → **Solutions** → **Import solution**
4. Browse to the `SOARLite_managed.zip` package from the [`assets/`](assets/) folder of this repo and click **Next**
5. Click **Next** again on the solution details page

### Connection references

The import wizard will prompt you to bind each connection reference to a connection in your tenant:

| Connection reference | What to provide |
|----------------------|-----------------|
| Excel Online (Business) | Create a new connection using the POC user's account |
| WorkIQ Word MCP | Create a new connection using the POC user's account |
| WorkIQ SharePoint MCP | Create a new connection using the POC user's account |
| GetRealtimeWeather (HTTP) | No connection needed |

### Environment variables

The wizard will also prompt for environment variable values:

| Variable | What to provide |
|----------|-----------------|
| **Weather API Key** (`soar_WeatherAPIKey`) | Paste your WeatherAPI.com key from the prerequisites step |

6. Click **Import** and wait for the import to complete (a few minutes).

> ⚠️ If the import fails on a connection reference, cancel, create the missing connection from **Connections** in the left nav, then restart the import.

---

## 4. Re-add the Excel Workbook as a Knowledge Source

Knowledge sources are tenant-specific and do **not** travel with the solution import. You must re-add the workbook in your tenant after import.

1. Open the **SOARLite** agent in Copilot Studio
2. Navigate to **Knowledge** → **Add knowledge**
3. Select **Files** → **OneDrive for Business**
4. Browse to `SOAR/SOARData/SOARLite-Data.xlsx` (the copy in **your** OneDrive)
5. Click **Add**

The agent will index the workbook (this takes a few minutes). Once indexed, the agent can query it via natural language.

> ℹ️ Knowledge is only added to **SOARLite** (the orchestrator). SOARLite_DocGen does not need its own knowledge source.

---

## 5. Rebind the Excel Actions to Your Workbook

The imported Excel Online actions reference the source tenant's OneDrive file ID, which does not exist in your tenant. You must rebind each action to **your** copy of the workbook.

There are **4 Excel actions** to rebind, across both agents:

| Agent | Action |
|-------|--------|
| SOARLite | List rows present in a table |
| SOARLite | Add a row into a table |
| SOARLite | Update a row |
| SOARLite_DocGen | List rows present in a table |

### Steps (repeat for each of the 4 actions):

1. Open the agent in Copilot Studio
2. Navigate to **Actions** → click the Excel action to open it
3. In the **Inputs** panel, click the **Location** dropdown → select **OneDrive for Business**
4. In the **Document Library** dropdown → select **OneDrive**
5. In the **File** picker → browse to `SOAR/SOARData/SOARLite-Data.xlsx`
6. In the **Table** dropdown → the correct table will populate automatically (it must match the original table name — e.g. `Sorties`, `Personnel`)
7. **Save** the action

> ⚠️ Each Excel sheet must already be formatted as a **Table** (see section 2) before this picker will resolve table names correctly.

---

## 6. Output Folder Configuration

Configure Word MCP to save generated documents to `SOAR/Missions/`:

- Weather briefs → `SOAR/Missions/Weather_Brief_[CALLSIGN]_[DATE].docx`
- Mission briefs → `SOAR/Missions/Mission_Brief_[CALLSIGN]_[DATE].docx`

*(Word MCP action configuration details TBD)*

---

## 7. Publish All Agents

> ⚠️ **REQUIRED:** Both agents must be **published** before the POC will function. An unpublished agent cannot be invoked, and the orchestrator cannot call an unpublished child agent.

The POC consists of two agents that work together:

| Agent | Role | Must Publish |
|-------|------|--------------|
| **SOARLite** (`crb00_SOARLite`) | Orchestrator / main conversational agent | ✅ Yes |
| **SOARLite_DocGen** (`crb00_SOARLiteDocGen`) | Connected document-generation worker agent | ✅ Yes |

### Steps:

1. Open **SOARLite_DocGen** in Copilot Studio and click **Publish** (publish the child agent first)
2. Open **SOARLite** in Copilot Studio and click **Publish**
3. Re-publish **both** agents after any change to instructions, topics, actions, or connections

### Notes:
- Always publish the connected worker agent (**SOARLite_DocGen**) before or alongside the orchestrator (**SOARLite**).
- If document generation fails with an invocation error, confirm **SOARLite_DocGen** is published in the same environment.

---

## Verification Checklist

**Pre-Import:**
- [ ] OneDrive folders created (`SOAR/Missions/`, `SOAR/SOARData/`)
- [ ] `SOARLite-Data.xlsx` copied to `SOARData/`
- [ ] Excel sheets converted to Tables
- [ ] WeatherAPI.com account created and API key obtained

**Import:**
- [ ] Solution imported successfully
- [ ] All connection references bound to POC user's account
- [ ] WeatherAPI key supplied to environment variable

**Post-Import:**
- [ ] Knowledge source (`SOARLite-Data.xlsx`) added to SOARLite and indexed
- [ ] All 4 Excel actions rebound to POC user's workbook (3 in SOARLite, 1 in SOARLite_DocGen)
- [ ] **Both agents published (SOARLite AND SOARLite_DocGen)**

**Smoke test:**
- [ ] Test query: "Who are the available F-16 pilots?"
- [ ] Test write: "Assign pilot Viper to RAPTOR Flight 2"
- [ ] Test weather: "What's the weather at Eglin AFB?"
- [ ] Test doc gen: "Generate a weather brief for VIPER Flight 1"
---

*Document Version: 1.1*  
*Last Updated: 2026-06-24*
