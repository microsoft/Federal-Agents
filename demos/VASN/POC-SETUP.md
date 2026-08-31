# VASN - POC Setup & Deployment Guide

This guide walks through deploying the **VASN (VA Site Navigator)** demo into a **sandbox or developer** Microsoft Copilot Studio environment. VASN helps Veterans, their families, caregivers, and VA staff locate nearby U.S. Department of Veterans Affairs facilities. It is intended for proof-of-concept exploration, not production use.

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
  - `https://services2.arcgis.com` (public VHA Medical Facilities dataset)

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
  - Topics (including **VA Office Lookup Topic**)
  - The OpenCage connection reference (`connectionreferences.mcs.yml`)
  - The **VAST** Power Automate flow (`workflows/VAST-5e1638df-b287-f111-ab0f-70a8a59b63dd/`)
3. Wait for the import to complete and confirm the agent appears in your environment.

> If you are working from the source-controlled `.mcs.yml` files, use your standard Copilot Studio / Power Platform solution import process to bring them into the environment.
>
> The custom connector definition is not included in this repository. Confirm that the imported package or target environment already provides the OpenCage custom connector referenced by the flow.

---

## Step 3 — Create the OpenCage Connection

Create and authorize the OpenCage connection required by the VAST flow.

1. In Power Platform, go to **Connections** (or you'll be prompted during agent/flow setup).
2. Create a new connection for the **OpenCage** custom connector.
3. Provide your OpenCage API key as required by the custom connector definition.
4. Save the connection.
5. Make sure the agent's and flow's **connection reference** points to this connection.

> [!CAUTION]
> The exported `workflow.json` currently contains an API key value in the OpenCage action parameters. Rotate that key and remove embedded credentials before sharing or deploying the package. Use the connector's secured authentication configuration or another approved secret-management pattern.

---

## Step 4 — Verify the Power Automate Flow

The **VAST** flow does the geocoding and VA facility lookup.

1. Open **Power Automate** in the same environment.
2. Locate the **VAST** cloud flow.
3. Confirm the flow is **turned on**.
4. Confirm its OpenCage connection reference points to the connection you created in [Step 3](#step-3--create-the-opencage-connection).
5. Use the flow's **Test** feature with a sample location such as `Murphy, Texas` and confirm it returns `site_code`, `address`, `city`, `state`, and `zip`.

---

## Step 5 — Configure Channels

VASN is configured for **Microsoft Teams** and **Microsoft 365 Copilot** in `settings.mcs.yml`.

1. Open the agent in Copilot Studio and go to **Channels**.
2. Enable or disable channels to match your demo needs.
3. For a quick test, the built-in **Test** pane (Step 6) requires no channel setup.

---

## Step 6 — Publish & Test

1. In Copilot Studio, open the agent and select **Publish**.
2. Open the **Test** pane.
3. Ask: **"Find a VA facility near me."**
4. When prompted **"Where are you located?"**, enter a city or address such as `Murphy, Texas`.
5. Confirm VASN returns a VA facility name, address, city, state, ZIP code, and Veterans Crisis Line guidance.

Expected response shape:

> **VA Facility closest to Murphy, Texas**
>
> **[Facility name] Facility**
> **Address:** [Street address]
> **City:** [City] | **State:** [State] | **ZIP Code:** [ZIP code]
>
> In case of an emergency, call the **Veterans Crisis Line** by dialing **988, then press 1**, or text **838255**.

---

## Configuration Reference

You can tune these without code changes:

| Setting | Where | Default | Notes |
|---------|-------|---------|-------|
| **Search radius** | `workflows/VAST-5e1638df-b287-f111-ab0f-70a8a59b63dd/workflow.json` (`distance`) | `6` statute miles | The topic text currently says 7 miles; align both values when changing the radius |
| **Result count** | `workflows/VAST-5e1638df-b287-f111-ab0f-70a8a59b63dd/workflow.json` (`resultRecordCount`) | `1` | Number of features requested from ArcGIS |
| **Channels and auth** | `settings.mcs.yml` | Teams, M365 Copilot; Integrated auth | Adjust to your demo |
| **Response format** | `topics/VAOfficeLookupTopic.mcs.yml` (`SendActivity`) | N/A | Controls how facility results are presented |

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Blank or failed response for a valid location | Geocoding returned no coordinates, or ArcGIS returned no feature within 6 miles | Try a more specific city/address; align and increase the flow distance and topic response text if appropriate |
| Flow fails on the geocoding step | OpenCage connection missing or key invalid | Recreate the connection (Step 3) with a valid key |
| Flow fails on the VA lookup (HTTP) step | Outbound access to `services2.arcgis.com` is blocked | Allow outbound access from the environment |
| Agent doesn't trigger the lookup | Topic is not published or the request was not routed to VA Office Lookup Topic | Re-publish and ask explicitly for a nearby VA facility |
| Connection reference shows as broken after import | Connection not yet created/linked | Create the OpenCage connection and re-link the reference |

---

*This is a demonstration agent intended for sandbox/developer environments. Teams adapting it for production should apply their organization's standard secret-management, governance, and ALM practices.*

