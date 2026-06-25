# LOLA — POC Setup & Deployment Guide

This guide walks through deploying the **LOLA (Local Office Lookup Agent)** demo into a **sandbox or developer** Microsoft Copilot Studio environment. It is intended for proof-of-concept exploration, not production use.

For an overview of what the agent does and how it works, see [README.md](README.md).

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1 — Get an OpenCage API Key](#step-1--get-an-opencage-api-key)
- [Step 2 — Import the Agent](#step-2--import-the-agent)
- [Step 3 — Create the OpenCage Connection](#step-3--create-the-opencage-connection)
- [Step 4 — Verify the Power Automate Flow](#step-4--verify-the-power-automate-flow)
- [Step 5 — Configure Channels](#step-5--configure-channels)
- [Step 6 — Publish & Test](#step-6--publish--test)
- [Configuration Reference](#configuration-reference)
- [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before you begin, make sure you have:

- A **Microsoft Copilot Studio** environment (Power Platform) you can deploy into — ideally a **sandbox or developer** environment.
- Permission to **import agents, custom connectors, and cloud flows** in that environment.
- A free **OpenCage Geocoding API** account (used to convert a place name into coordinates).
- Outbound access from your environment to:
  - `https://api.opencagedata.com` (geocoding)
  - `https://services6.arcgis.com` (public SSA Field Office dataset)

---

## Step 1 — Get an OpenCage API Key

The OpenCage key is **not included in this repository**. Each person deploying the demo supplies their own key.

1. Sign up for a free account at [opencagedata.com](https://opencagedata.com/).
2. Open your dashboard and copy your **API key**.
3. Keep it handy for [Step 3](#step-3--create-the-opencage-connection).

> The free tier is more than enough for a POC. No billing setup is required.

---

## Step 2 — Import the Agent

Bring the agent and its components into your environment.

1. Sign in to [Microsoft Copilot Studio](https://copilotstudio.microsoft.com/) and select your target (sandbox/dev) environment in the top-right environment picker.
2. Import the agent solution / package that contains:
   - The agent definition (`agent.mcs.yml`)
   - Topics (including **Field Office Finder**)
   - The **OpenCage** custom connector (`connectors/new_opencage-.../`)
   - The **FO-Automate** Power Automate flow (`workflows/FO-Automate-.../`)
3. Wait for the import to complete and confirm the agent appears in your environment.

> If you are working from the source-controlled `.mcs.yml` files, use your standard Copilot Studio / Power Platform solution import process to bring them into the environment.

---

## Step 3 — Create the OpenCage Connection

This is where your API key lives — entered once, stored securely in the connection, never in source files.

1. In Power Platform, go to **Connections** (or you'll be prompted during agent/flow setup).
2. Create a new connection for the **OpenCage** custom connector.
3. When prompted, paste your OpenCage key into the secured **`api_key`** field.
4. Save the connection.
5. Make sure the agent's and flow's **connection reference** points to this connection.

> The custom connector automatically appends the key to each geocoding request, so no key needs to be entered anywhere else.

---

## Step 4 — Verify the Power Automate Flow

The **FO-Automate** flow does the geocoding and SSA office lookup.

1. Open **Power Automate** in the same environment.
2. Locate the **FO-Automate** cloud flow.
3. Confirm the flow is **turned on**.
4. Confirm its OpenCage connection reference points to the connection you created in [Step 3](#step-3--create-the-opencage-connection).
5. (Optional) Use the flow's **Test** feature with a sample location (e.g., `McKinney, TX`) to confirm it returns office details.

---

## Step 5 — Configure Channels

LOLA ships configured for **Telephony**, **Microsoft Teams**, and **Microsoft 365 Copilot** (defined in `settings.mcs.yml`).

1. Open the agent in Copilot Studio and go to **Channels**.
2. Enable or disable channels to match your demo needs.
3. For a quick test, the built-in **Test** pane (Step 6) requires no channel setup.

---

## Step 6 — Publish & Test

1. In Copilot Studio, open the agent and select **Publish**.
2. Open the **Test** pane.
3. Use the conversation starter: **"I need to find a Social Security Field Office near me"**.
4. When prompted **"Where are you located?"**, enter a city (e.g., `McKinney, TX`).
5. Confirm LOLA returns the nearest SSA field office with address, phone, fax, and hours.

Expected response (example):

> **SSA Field Office Closest to McKinney, TX**
>
> **MCKINNEY TX SSA Field Office**
> **Address:** 3250 CRAIG DR
> **City:** MCKINNEY · **State:** TX · **ZIP Code:** 75070
> **Phone:** (866) 931-2731 · **Fax:** (833) 939-1986
> **Hours:** Monday–Friday 9:00 AM – 4:00 PM Local Time

---

## Configuration Reference

You can tune these without code changes:

| Setting | Where | Default | Notes |
|---------|-------|---------|-------|
| **Search radius** | `workflows/FO-Automate-.../workflow.json` (`distance`) | `20` statute miles | Increase/decrease to widen or narrow the search |
| **Result count** | `workflows/FO-Automate-.../workflow.json` (`resultRecordCount`) | `1` | Number of offices returned (nearest first) |
| **Channels & auth** | `settings.mcs.yml` | Telephony, Teams, M365 Copilot; Integrated auth | Adjust to your demo |
| **Response format** | `agent.mcs.yml` instructions + Field Office Finder topic `SendActivity` | — | Controls how results are presented |

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| "No office found" for a valid city | Geocoding returned no/odd coordinates, or no office within 20 miles | Try a larger city/ZIP; increase the `distance` value in the flow |
| Flow fails on the geocoding step | OpenCage connection missing or key invalid | Recreate the connection (Step 3) with a valid key |
| Flow fails on the SSA lookup (HTTP) step | Outbound access to `services6.arcgis.com` blocked | Allow outbound access from the environment |
| Agent doesn't trigger the lookup | Topic not published or starter phrase not matched | Re-publish; use the conversation starter phrasing |
| Connection reference shows as broken after import | Connection not yet created/linked | Create the OpenCage connection and re-link the reference |

---

*This is a demonstration agent intended for sandbox/developer environments. Teams adapting it for production should apply their organization's standard secret-management, governance, and ALM practices.*

