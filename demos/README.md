# Federal Agents — Demos

This folder contains standalone demonstration agents for U.S. Federal scenarios, built primarily with **Microsoft Copilot Studio**, **Power Automate**, and the Power Platform. Each demo is a self-contained proof-of-concept intended for **sandbox / developer environments** — not production use. Follow the linked README in each folder for capabilities, architecture, and setup details.

## Available Demos

| Demo | Full Name | Description |
| --- | --- | --- |
| [AEGIS](./AEGIS/README.md) | Acquisition Evidence & Governance Investigation System | Acquisition intelligence and evidence-review agent that researches vendors and public federal contract activity using SAM.gov and USAspending.gov, producing source-backed briefings for human review without scoring vendors or making acquisition decisions. |
| [ATLAS](./ATLAS/README.md) | Access, Travel, Logistics & Assignment System | Conversational courier readiness and trip-support agent that helps couriers prepare for assigned runs — itineraries, site access and badging, escort requirements, parking, points of contact, and delivery instructions. |
| [CropSure](./CropSure/README.md) | Crop Validation & Case Review Agent | Copilot Studio agent that combines USDA NASS Quick Stats and CropScape/CDL cropland data with a structured, human-reviewed case workflow to assemble validation packages, record findings, and route Approve / Review Required / Escalate / Unable To Determine recommendations to an authorized reviewer. |
| [Fed Workforce Assistant](./FedWorkforceAssistant/README.md) | Federal Workforce Assistant | Knowledge agent for federal HR specialists and benefits administrators that explains workforce policies, classification standards, benefits, retirement guidance, and travel policy using configured official and agency sources. |
| [IAA Navigator](./IAANavigator/README.md) | Interagency Agreement Navigator | Helps authorized personnel prepare, look up, update, and track Interagency Agreement request packages and related line items in SharePoint. Workflow transitions do not constitute approvals, signatures, or authority to obligate funds. |
| [LOLA](./LOLA/README.md) | Local Office Lookup Agent | Finds the nearest Social Security Administration (SSA) field office within 20 miles of a city or address, returning address, phone, fax, and hours via OpenCage geocoding and an SSA ArcGIS spatial query. |
| [MEDA](./MEDA/README.md) | Medical Enterprise Decision Agent | Clinical decision-support agent for licensed providers that surfaces patient-specific info, evidence-based reference, terminology explanations, care coordination, and documentation assistance. Advisory only — not a certified medical device. |
| [NSFPIA](./NSFPIA/README.md) | NSF Proposal Intake Assistant | POC agent for NSF administrative proposal intake — runs compliance checks, program routing, reviewer conflict-of-interest screening, draft communications, and attributed panel-summary drafting on synthetic demo data. Not approved for production NSF data or decisions. |
| [Oversight Agent](./OversightAgent/README.md) | Congressional Oversight Response Agent | Drafts neutral, citation-backed responses to Congressional oversight inquiries, RFIs, QFRs, and hearing-preparation questions using approved agency knowledge sources and mandatory human review. |
| [SOAR](./SOAR/README.md) | Sortie Optimization & Allocation Resourcing | Conversational sortie-planning agent for USAF training operations — builds operations, missions, and sorties; assigns crews and aircraft; checks weather; and generates mission paperwork via a connected SOAR_DocGen worker agent. |
| [VASN](./VASN/README.md) | VA Site Navigator | Helps Veterans, families, caregivers, and VA staff find a nearby Department of Veterans Affairs (VHA) facility from a city or address, and surfaces Veterans Crisis Line guidance. |
| [WATCHTOWER](./WatchTower/README.md) | WATCHTOWER | Executive cybersecurity intelligence and decision-support agent that uses NIST NVD vulnerability records and the CISA KEV catalog for evidence-supported briefings and leadership recommendations. Does not assess customer assets, establish exposure, or perform remediation. |

## Demo Details

### AEGIS — Acquisition Evidence & Governance Investigation System
A Microsoft Copilot Studio agent for federal acquisition, procurement, program, and oversight teams. It uses SAM.gov and USAspending.gov to research vendors, review bounded prime-contract award sets, compare obligations across the last two completed fiscal years, and produce acquisition intelligence briefs with source links, retrieval timestamps, and scope limitations. It separates facts from inferences and evidence gaps; authorized personnel retain all investigative and acquisition decisions.
📄 [AEGIS README](./AEGIS/README.md)

### ATLAS — Access, Travel, Logistics & Assignment System
A conversational courier readiness and trip-support agent (Copilot Studio + Dataverse/SharePoint). ATLAS acts as an assistant, not an autonomous coordinator — it surfaces logistics and readiness details but leaves every operational decision to the courier. Supports Courier Support, Site Information, and Training modes.
📄 [ATLAS README](./ATLAS/README.md)

### CropSure — Crop Validation & Case Review Agent
A Microsoft Copilot Studio solution that combines agricultural reference data with a structured, human-reviewed case workflow. It uses USDA NASS Quick Stats and CropScape Cropland Data Layer (CDL) services to assemble validation packages, auto-generate Case IDs, and consolidate findings (evidence, discrepancies, confidence, limitations, missing information) before routing a recommendation via Power Automate to a SharePoint-backed Case Review list. CropSure supports validation and review only — an authorized reviewer makes and records every final decision. Available on Microsoft Teams and Microsoft 365 Copilot.
📄 [CropSure README](./CropSure/README.md)

### Fed Workforce Assistant
A Microsoft Copilot Studio knowledge agent for federal HR specialists and benefits administrators. It searches configured OPM, GSA, and agency SharePoint sources to explain workforce policy, position classification, FEHB, retirement, travel, and onboarding guidance in plain language with source citations.
📄 [Fed Workforce Assistant README](./FedWorkforceAssistant/README.md)

### IAA Navigator
A Microsoft Copilot Studio agent for preparing and tracking Interagency Agreement request packages using a conversational intake card, SharePoint request and line-item records, and two Power Automate flows. Authorized users can look up requests, add cost lines, make supported updates, record selected completed requirements, and advance workflow stages. It does not execute agreements, obtain signatures, process payments, or confer funding authority. The demo currently provides exported solution source, not a qualified customer installation ZIP; follow its documented limitations and acceptance requirements before deployment.
📄 [IAA Navigator README](./IAANavigator/README.md)

### LOLA — Local Office Lookup Agent
A Copilot Studio agent that answers *"Where are you located?" → nearest SSA field office within 20 miles.* Geocodes the location through an OpenCage custom connector and runs a spatial query against the public SSA Field Office ArcGIS feature service via Power Automate. Available on Telephony, Teams, and Microsoft 365 Copilot.
📄 [LOLA README](./LOLA/README.md) · [POC Setup](./LOLA/POC-SETUP.md)

### MEDA — Medical Enterprise Decision Agent
A reference clinical decision-support agent for licensed healthcare professionals, delivered as an unmanaged Copilot Studio solution package with four supporting Power Automate flows (SharePoint and team-calendar integration). Configured for Teams and Microsoft 365 Copilot with high content moderation. All output is advisory and must be validated by a clinician.
📄 [MEDA README](./MEDA/README.md)

### NSFPIA — NSF Proposal Intake Assistant
A Microsoft Copilot Studio proof of concept that demonstrates administrative proposal intake, compliance checks, program routing, reviewer conflict screening, draft communications, and attributed panel-summary drafting. Built on Dataverse, AI Builder, SharePoint, Word Online, Outlook, and Teams, it ships with deterministic synthetic proposals and seed data. All content is fictional demo data — the POC is not approved for production NSF data or decisions.
📄 [NSFPIA README](./NSFPIA/README.md)

### Oversight Agent — Congressional Oversight Response Agent
A Microsoft Copilot Studio agent that helps authorized agency staff draft neutral, citation-backed responses to Congressional oversight inquiries, RFIs, QFRs, and hearing-preparation questions. It is grounded only in approved agency sources, uses Azure AI Search for retrieval, and requires Legislative Affairs, General Counsel, or Policy review before use.
📄 [Oversight Agent README](./OversightAgent/README.md)

### SOAR — Sortie Optimization & Allocation Resourcing
A conversational sortie-planning agent for USAF training operations, built as two connected agents: **SOAR** (the conversational orchestrator) and **SOAR_DocGen** (a background document-generation worker for weather and mission briefs). Uses Excel Online, real-time weather, and WorkIQ Word/SharePoint MCP tools. Inspired by — but not a faithful representation of — the official Air Force planning process.
📄 [SOAR README](./SOAR/README.md) · [DocGen README](./SOAR/DOCGen_README.md) · [Architecture](./SOAR/SOAR_ARCHITECTURE.md) · [POC Setup](./SOAR/POC-SETUP.md)

### VASN — VA Site Navigator
A Copilot Studio agent that answers *"Where are you located?" → a nearby VA facility.* Geocodes the location via OpenCage and queries the public Veterans Health Administration Medical Facilities ArcGIS feature service through a Power Automate flow. Every facility result includes Veterans Crisis Line contact guidance. Configured for Teams and Microsoft 365 Copilot.
📄 [VASN README](./VASN/README.md) · [POC Setup](./VASN/POC-SETUP.md)

### WATCHTOWER
A Microsoft Copilot Studio executive cybersecurity intelligence agent that turns public NIST National Vulnerability Database records and the CISA Known Exploited Vulnerabilities catalog into evidence-supported briefings. It supports CVE research, exploitation-list checks, recent KEV additions, catalog-wide priorities, and leadership recommendations aligned to the NIST Cybersecurity Framework. It distinguishes published facts from conditional mission impact and evidence gaps; it does not inspect customer assets, establish exposure or compromise, perform remediation, or make operational decisions.
📄 [WATCHTOWER README](./WatchTower/README.md)

---

> ⚠️ **Note:** All demos in this folder are demonstration / proof-of-concept solutions intended for evaluation and training in non-production environments. They are not authoritative operational systems. Validate connections, data sources, and security roles before testing, and always defer to official guidance for real-world operations.
