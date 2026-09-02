# Federal Agents — Demos

This folder contains standalone demonstration agents for U.S. Federal scenarios, built primarily with **Microsoft Copilot Studio**, **Power Automate**, and the Power Platform. Each demo is a self-contained proof-of-concept intended for **sandbox / developer environments** — not production use. Follow the linked README in each folder for capabilities, architecture, and setup details.

## Available Demos

| Demo | Full Name | Description |
| --- | --- | --- |
| [ATLAS](./ATLAS/README.md) | Access, Travel, Logistics & Assignment System | Conversational courier readiness and trip-support agent that helps couriers prepare for assigned runs — itineraries, site access and badging, escort requirements, parking, points of contact, and delivery instructions. |
| [LOLA](./LOLA/README.md) | Local Office Lookup Agent | Finds the nearest Social Security Administration (SSA) field office within 20 miles of a city or address, returning address, phone, fax, and hours via OpenCage geocoding and an SSA ArcGIS spatial query. |
| [MEDA](./MEDA/README.md) | Medical Enterprise Decision Agent | Clinical decision-support agent for licensed providers that surfaces patient-specific info, evidence-based reference, terminology explanations, care coordination, and documentation assistance. Advisory only — not a certified medical device. |
| [SOAR](./SOAR/README.md) | Sortie Optimization & Allocation Resourcing | Conversational sortie-planning agent for USAF training operations — builds operations, missions, and sorties; assigns crews and aircraft; checks weather; and generates mission paperwork via a connected SOAR_DocGen worker agent. |
| [VASN](./VASN/README.md) | VA Site Navigator | Helps Veterans, families, caregivers, and VA staff find a nearby Department of Veterans Affairs (VHA) facility from a city or address, and surfaces Veterans Crisis Line guidance. |

## Demo Details

### ATLAS — Access, Travel, Logistics & Assignment System
A conversational courier readiness and trip-support agent (Copilot Studio + Dataverse/SharePoint). ATLAS acts as an assistant, not an autonomous coordinator — it surfaces logistics and readiness details but leaves every operational decision to the courier. Supports Courier Support, Site Information, and Training modes.
📄 [ATLAS README](./ATLAS/README.md)

### LOLA — Local Office Lookup Agent
A Copilot Studio agent that answers *"Where are you located?" → nearest SSA field office within 20 miles.* Geocodes the location through an OpenCage custom connector and runs a spatial query against the public SSA Field Office ArcGIS feature service via Power Automate. Available on Telephony, Teams, and Microsoft 365 Copilot.
📄 [LOLA README](./LOLA/README.md) · [POC Setup](./LOLA/POC-SETUP.md)

### MEDA — Medical Enterprise Decision Agent
A reference clinical decision-support agent for licensed healthcare professionals, delivered as an unmanaged Copilot Studio solution package with four supporting Power Automate flows (SharePoint and team-calendar integration). Configured for Teams and Microsoft 365 Copilot with high content moderation. All output is advisory and must be validated by a clinician.
📄 [MEDA README](./MEDA/README.md)

### SOAR — Sortie Optimization & Allocation Resourcing
A conversational sortie-planning agent for USAF training operations, built as two connected agents: **SOAR** (the conversational orchestrator) and **SOAR_DocGen** (a background document-generation worker for weather and mission briefs). Uses Excel Online, real-time weather, and WorkIQ Word/SharePoint MCP tools. Inspired by — but not a faithful representation of — the official Air Force planning process.
📄 [SOAR README](./SOAR/README.md) · [DocGen README](./SOAR/DOCGen_README.md) · [Architecture](./SOAR/SOAR_ARCHITECTURE.md) · [POC Setup](./SOAR/POC-SETUP.md)

### VASN — VA Site Navigator
A Copilot Studio agent that answers *"Where are you located?" → a nearby VA facility.* Geocodes the location via OpenCage and queries the public Veterans Health Administration Medical Facilities ArcGIS feature service through a Power Automate flow. Every facility result includes Veterans Crisis Line contact guidance. Configured for Teams and Microsoft 365 Copilot.
📄 [VASN README](./VASN/README.md) · [POC Setup](./VASN/POC-SETUP.md)

---

> ⚠️ **Note:** All demos in this folder are demonstration / proof-of-concept solutions intended for evaluation and training in non-production environments. They are not authoritative operational systems. Validate connections, data sources, and security roles before testing, and always defer to official guidance for real-world operations.
