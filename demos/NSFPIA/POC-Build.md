# NSF Proposal Agent POC Build Tracker

## Purpose

This document is the operational source of truth for building the NSF Proposal Intake Assistant proof-of-concept demo. Read this file at the beginning of every implementation session, update it as work is completed, and leave the next session with one explicit next action.

The target architecture and production enablement guidance remain in [DESIGN.md](DESIGN.md). This tracker covers only the working POC demo.

## Status Rules

Use only these values in the Status column:

- `Not started`: no implementation evidence has been accepted.
- `In progress`: work has begun but the completion gate has not passed.
- `Blocked`: work cannot continue; record the blocker and owner in Session Notes.
- `Complete`: the completion gate passed and its evidence is linked or described.

A file existing is not enough to mark a step complete. Completion requires the stated evidence. Existing experimental topics and connections are treated as unverified inputs, so every step begins as `Not started`.

## Current Handoff

| Field | Value |
|---|---|
| Last updated | September 23, 2026 |
| Current phase | Phase 8 - Complete |
| Overall status | Complete |
| Next step | Obtain sponsor authorization and assign owners before beginning the section 19 production workstreams. |
| Active blocker | None for the synthetic-data POC. Production promotion is prohibited until the section 19 owners, approvals, integrations, security controls, evaluation, and operational gates are complete. |
| Last completed step | `POC-821` Add an unsent assignment-notification preview. |

## POC Completion Definition

The POC is complete only when all of the following are demonstrated with synthetic data:

- A defective proposal produces the expected compliance findings with PAPPG citations and a draft return notice.
- A clean proposal receives the expected program and workload-aware officer recommendation.
- Three eligible reviewers are shown and all five planted conflicts are excluded with their expected reasons.
- Reviewer invitations are created as drafts and are not sent.
- A panel-summary Word document is created with a reviewer identifier on every generated sentence and a blank Panel Recommendation section.
- Supported policy answers cite approved knowledge sources; unsupported questions are not guessed.
- Merit-ranking and funding-recommendation requests are refused.
- Consequential results require explicit program officer confirmation.
- The end-to-end demonstration can be repeated from reset synthetic data.

## Phase 0 - Scope and Baseline

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-001 | Complete | Confirm the six proposal scenarios, proposal `2650001` as the primary demo proposal, planted defects, expected routing, and planted reviewer conflicts. | [Expected-results manifest](../Proposals/expected-results.json) and [validator](../Proposals/validate-expected-results.ps1) pass against all six generated DOCX files: six proposal outcomes, three officers, eight reviewers, five distinct conflict exclusions, and three eligible recommendations. |
| POC-002 | Complete | Decide whether to retain or remove the experimental identity capture and presentation topics. | The disposition is recorded below. Tracked Copilot Studio Activity tests show five direct and greeting-prefixed proposal requests route to Search sources without invoking Greeting or another unrelated topic. |
| POC-003 | Complete | Initialize the workspace as a Git repository and create the baseline commit. | `git status` is clean after a baseline commit. |
| POC-004 | Complete | Create a repository README covering synthetic-data-only scope, setup, synchronization, and publish steps. | README exists and matches the POC design. |
| POC-005 | Complete | Verify the target demo environment and Galen Brown (`galenb@caldova51155962.onmicrosoft.com`) as the licensed program-officer persona, including Copilot Studio access, Dataverse, AI Builder credits, SharePoint, Word Online, Outlook, and Teams. | Environment checklist records each dependency as available or identifies an approved POC fallback. |

### POC-002 Topic Disposition

| Topic | Decision | Reason and owning follow-up |
|---|---|---|
| Greeting | Removed | Its recognized greeting intent ended with `CancelAllDialogs`, so a greeting-prefixed proposal request could interrupt active work. The deletion was applied and tracked Activity testing confirmed the greeting-prefixed request remained a proposal request. |
| Conversation Start | Retain | This event-scoped topic is the single startup presentation path. POC-105 will replace its generic message with the approved capability greeting. |
| Sign in | Retain | This event-scoped topic supports the required Microsoft Entra authentication flow. Effective access is verified under POC-005. |
| Conversational boosting | Retain | This is the knowledge-source answer path. POC-104 disables general model knowledge and the knowledge phase constrains approved policy sources and unsupported answers. |
| Standard system handlers | Retain | Error, reset, escalation, fallback, goodbye, thank-you, start-over, end-of-conversation, and ambiguity handlers remain unless tracked testing shows a proposal-trigger collision. |

POC-002 is complete. The Activity evidence covers direct compliance, reviewer, routing, panel-summary, and greeting-prefixed proposal requests; all five routed to Search sources without invoking an unrelated topic, and Conversation Start appeared once at startup. Sign in remains event-scoped for authentication. POC-002 does not rewrite the startup greeting, change agent settings, or implement business and refusal topics.

### POC-001 Synthetic Personnel Baseline

All names, organizations, relationships, and addresses below are synthetic. Program officers and reviewers are Dataverse records, not tenant users. Addresses use the reserved `.example` domain and must never receive mail.

#### Program officers

Required fields for each ProgramOfficer record are `OfficerID`, `OfficerName`, `Email`, `Programs`, `ExpertiseKeywords`, `CurrentLoad`, and `IsNewHire`. `Programs` uses the Program-ProgramOfficer many-to-many relationship because one officer covers two programs.

| Officer ID | Officer name | Email | Supported programs | Expertise keywords | Current load | New hire | Expected routing |
|---|---|---|---|---|---:|---|---|
| PO-001 | Dr. Maya Chen | `maya.chen@nsf-poc.example` | Secure Distributed Systems | distributed systems; cybersecurity; cyber-physical systems; resilient control | 6 | No | Overflow or manual reassignment for Secure Distributed Systems |
| PO-002 | Dr. Aaron Blake | `aaron.blake@nsf-poc.example` | Secure Distributed Systems | federated control; microgrids; fault tolerance; secure communications | 2 | Yes | Clean proposal `2650001`; lower load makes PO-002 the expected recommendation |
| PO-003 | Dr. Sofia Reyes | `sofia.reyes@nsf-poc.example` | Coastal Ecosystem Dynamics; STEM Learning Pathways | coastal modeling; ecosystem recovery; STEM education; learning pathways | 3 | No | Clean proposals `2650002` and `2650003` |

Defective proposals `2650004`, `2650005`, and `2650006` must stop after compliance and receive no routing recommendation unless their defects are corrected.

#### Reviewers

Required fields for each Reviewer record are `ReviewerID`, `Name`, `Email`, `Institution`, `Department`, `ExpertiseKeywords`, `PublicationKeywords`, `Collaborators`, `Advisees`, `Advisors`, `LastPanelDate`, and `Availability`. For this curated POC, each reviewer's `PublicationKeywords` duplicate the listed `ExpertiseKeywords`. Empty relationship fields mean no planted relationship. All eight reviewers overlap primary proposal `2650001` on at least one expertise keyword so conflict screening, rather than pre-filtering, determines the five exclusions.

| Reviewer ID | Name and synthetic email | Institution and department | Expertise and publication keywords | Conflict data | Availability | Expected result for `2650001` |
|---|---|---|---|---|---|---|
| RV-001 | Dr. Nia Foster; `nia.foster@reviewer-poc.example` | Meridian Technical Institute; Electrical Systems | microgrids; cyber-physical systems; resilient control | Collaborators: empty; Advisees: empty; Advisors: empty; LastPanelDate: empty | Available | Exclude: same institution as PI Dr. Lena Ortiz |
| RV-002 | Dr. Owen Mercer; `owen.mercer@reviewer-poc.example` | Northbridge Institute of Technology; Computer Science | distributed systems; fault tolerance; federated control | Collaborators: `Dr. Lena Ortiz (2024)`; Advisees: empty; Advisors: empty; LastPanelDate: empty | Available | Exclude: recent co-author within 48 months |
| RV-003 | Dr. Leila Haddad; `leila.haddad@reviewer-poc.example` | Cedar Ridge University; Automation Engineering | federated control; distributed optimization; microgrids | Collaborators: empty; Advisees: empty; Advisors: `Dr. Lena Ortiz`; LastPanelDate: empty | Available | Exclude: advisor/advisee relationship |
| RV-004 | Dr. Marcus Ibarra; `marcus.ibarra@reviewer-poc.example` | Eastlake Research University; Secure Computing | secure communications; privacy; distributed systems | Collaborators: empty; Advisees: empty; Advisors: empty; LastPanelDate: empty | Available | Exclude: PI-declared conflict |
| RV-005 | Dr. Hana Petrov; `hana.petrov@reviewer-poc.example` | Granite Coast Polytechnic; Networked Control | fault tolerance; network resilience; cyber-physical systems | Collaborators: empty; Advisees: empty; Advisors: empty; LastPanelDate: `2026-03-15` | Available | Exclude: panel service for this PI within 12 months |
| RV-006 | Dr. Caleb Monroe; `caleb.monroe@reviewer-poc.example` | Pine Valley Technical University; Energy Systems | microgrids; resilient control; fault tolerance | Collaborators: empty; Advisees: empty; Advisors: empty; LastPanelDate: empty | Available | Eligible rank 1: strongest microgrid and resilient-control match |
| RV-007 | Dr. Imani Brooks; `imani.brooks@reviewer-poc.example` | Silver Plains University; Computer Engineering | distributed systems; federated learning; privacy-preserving systems | Collaborators: empty; Advisees: empty; Advisors: empty; LastPanelDate: empty | Available | Eligible rank 3: strong distributed-systems match |
| RV-008 | Dr. Theo Nguyen; `theo.nguyen@reviewer-poc.example` | Harborview College of Engineering; Control Systems | cyber-physical systems; networked control; fault-tolerant systems | Collaborators: empty; Advisees: empty; Advisors: empty; LastPanelDate: empty | Available | Eligible rank 2: strong control and fault-tolerance match |

#### Required conflict cross-records

- Proposal `2650001` must set `DeclaredConflicts` to `RV-004`.
- Historical synthetic proposal `HIST-ORTIZ-001` must identify Dr. Lena Ortiz as PI.
- Historical ReviewAssignment `HIST-RA-001` must link `RV-005` to `HIST-ORTIZ-001` with panel service dated `2026-03-15`; this supports the recent-panel exclusion rather than relying on `LastPanelDate` alone.
- The expected exclusion order is `RV-001` same institution, `RV-002` recent co-author, `RV-003` advisor/advisee, `RV-004` PI-declared conflict, and `RV-005` recent panel service.
- The expected eligible recommendation order is `RV-006`, `RV-008`, then `RV-007`; each must show expertise rationale and all five conflict checks passed.

## Phase 1 - Agent Foundation

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-101 | Complete | Rename and describe the agent as NSF Proposal Intake Assistant. | Agent metadata displays the approved name and POC description. |
| POC-102 | Complete | Add the instructions from the design, including administrative scope, citation rules, draft-only behavior, prompt-injection handling, and merit boundary. | Instructions are present in `agent.mcs.yml` and pass validation. |
| POC-103 | Complete | Add the approved starter prompts. | Starter prompts appear in the agent experience and invoke the intended capability. |
| POC-104 | Complete | Disable general model knowledge, set content moderation to High, confirm generative orchestration, and keep file uploads disabled. | Settings match the design and the Problems pane is clear. |
| POC-105 | Complete | Replace the default greeting with the POC capability summary. | A new conversation displays the approved greeting. |
| POC-106 | Complete | Implement the deterministic merit-review refusal topic. | Five score, rank, quality, and funding prompts all receive the refusal and administrative redirect. |
| POC-107 | Complete | Review the broad Dataverse MCP action and retain it only if its POC use and data scope are justified. | The action is removed, disabled, or documented with a constrained purpose and verified permissions. |

## Phase 2 - SharePoint and Knowledge

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-201 | Complete | Use the approved [NSF Cluster Operations - POC](https://caldova51155962.sharepoint.com/sites/NSFClusterOperations-POC) SharePoint site for the demo and verify its owner. | Site URL and owner are recorded in Session Notes. |
| POC-202 | Complete | Verify the required POC metadata columns in the existing Policy, Solicitations, Cluster SOPs, Templates, Proposal Intake, and Generated Documents libraries. | Microsoft Graph verification passed all 19 required column checks across the six libraries. |
| POC-203 | Complete | Upload the approved PAPPG, NSF 26-200 supplement, Important Notice 149, and selected solicitations. | Microsoft Graph read-back verified all six source files, nontrivial byte sizes, accessible SharePoint URLs, and required metadata. |
| POC-204 | Complete | Author and upload the synthetic Return-Without-Review Checklist, Reviewer Selection and COI SOP, Panel Summary Guide, Cluster Routing Guide, and Policy Contact Roster. | All five synthetic documents are present and clearly labeled as demo content. |
| POC-205 | Complete | Add Policy, Solicitations, and Cluster SOPs as agent knowledge sources. | Three knowledge definitions are pulled into the workspace and validate cleanly. |
| POC-206 | Complete | Confirm Proposal Intake and Generated Documents are not agent knowledge sources. | Knowledge inventory shows neither operational library. |
| POC-207 | Complete | Test supported, unsupported, and permission-sensitive policy questions. | Expected citations are returned and unsupported answers are declined without guessing. |

### POC-202 SharePoint Metadata Verification

Verify each library in **Library settings > Columns**. Column names and types must match; requiredness must also match unless an existing stricter setting is documented and does not block the POC flows.

| Library | Required POC columns |
|---|---|
| Policy | `DocumentType` (single line of text, required); `EffectiveDate` (date only, required); `Supersedes` (single line of text, optional) |
| Solicitations | `SolicitationNumber` (single line of text, required); `Program` (single line of text, required); `Directorate` (single line of text, required); `DueDate` (date only, required); `PageLimits` (multiple lines of text, optional) |
| Cluster SOPs | `Owner` (person or group, required); `EffectiveDate` (date only, required) |
| Templates | `TemplateType` (choice, required: `ReturnWithoutReviewNotice`, `ReviewerInvitation`, `PanelSummarySkeleton`); `EffectiveDate` (date only, required) |
| Proposal Intake | `ProposalID` (single line of text, optional); `Solicitation` (single line of text, optional); `ReceivedDate` (date and time, optional); `IntakeStatus` (choice, optional: `Received`, `Processing`, `Compliant`, `ReturnWithoutReview`, `NeedsHumanReview`, `Routed`). The intake flow populates these after SharePoint creates the file. |
| Generated Documents | `ProposalID` (single line of text, required); `DocumentType` (choice, required: `ReturnWithoutReviewNotice`, `PanelSummary`); `DraftStatus` (choice, required: `Draft`, `Approved`) |

## Phase 3 - Dataverse and Synthetic Data

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-301 | Complete | Create the NSF Proposal Intake solution with publisher prefix `nsf`. | Solution exists in the POC environment. |
| POC-302 | Complete | Create the minimal Proposal, Program, Program Officer, Reviewer, Review Assignment, Review, and Compliance Check tables. | Seven tables and required relationships exist and are included in the solution. |
| POC-303 | Complete | Add the columns and choices required by the four POC flows and topics. | [POC-303 Dataverse Schema Checklist](POC-303-SCHEMA.md) maps every flow input, query, and output to an available column. |
| POC-304 | Complete | Build the repeatable synthetic-data generator and expected-results manifest. | [Generation validator](../Proposals/validate-generation.ps1) proves two isolated runs produce six byte-identical DOCX files that match the synthetic-only [expected-results manifest](../Proposals/expected-results.json). |
| POC-305 | Complete | Generate three programs, three officers, six proposals, eight reviewers, and three reviews for primary demo proposal `2650001`. | [Dataverse seed](../Proposals/dataverse-seed.json) contains physical-schema records, and its [validator](../Proposals/validate-dataverse-seed.ps1) confirms all counts and identifiers match the manifest. |
| POC-306 | Complete | Plant the compliance defects and five reviewer-conflict types while leaving three eligible reviewers. | [Planted-scenario validator](../Proposals/validate-planted-scenarios.ps1) proves all three defects and five conflict mechanisms from generated artifacts while retaining the expected three eligible reviewers. |
| POC-307 | Complete | Load the synthetic records into Dataverse without uploading trigger files. | [Dataverse loader](../Proposals/load-dataverse-seed.ps1) upserts under the approved Galen identity and live verification confirms exact manifest counts and resolved lookups without SharePoint uploads. |
| POC-308 | Complete | Verify the demo connection owner can access only the required POC solution and SharePoint content. | [Access verification](POC-308-ACCESS.md) confirms positive POC access and constrained agent sources/tools; existing Isaac tenant groups and Salesperson access are documented as user-accepted POC exceptions. |

## Phase 4 - Templates and AI Prompts

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-401 | Complete | Create the Return-Without-Review Notice Word template. | The [template generator](../Templates/generate-return-without-review-template.ps1) creates six named plain-text controls, and the [test payload](../Templates/return-without-review-test-payload.json) populates every control successfully. |
| POC-402 | Complete | Create the Reviewer Invitation Word template or approved Outlook draft body template. | The [Outlook body template](../Templates/reviewer-invitation-template.html) and [test payload](../Templates/reviewer-invitation-test-payload.json) produce a complete Graph-verified unsent draft through the [draft validator](../Templates/test-reviewer-invitation.ps1). |
| POC-403 | Complete | Create the Panel Summary Skeleton Word template. | The [template generator](../Templates/generate-panel-summary-template.ps1) and [attributed payload](../Templates/panel-summary-test-payload.json) validate all six required sections, eight Word controls, reviewer tags, and a structurally blank Panel Recommendation. |
| POC-404 | Complete | Create the Extract Proposal Structure AI Builder prompt with structured JSON output. | The published [prompt specification and live test evidence](../Prompts/extract-proposal-structure.md) record parseable expected fields for extracted text from clean proposal `2650001` and defective proposal `2650004`. |
| POC-405 | Complete | Create the Classify Proposal prompt. | The published [prompt specification and live test evidence](../Prompts/classify-proposal.md) record exact expected program names for all three clean demo proposals. |
| POC-406 | Complete | Create the Rank Reviewers prompt. | The published [prompt specification and live test evidence](../Prompts/rank-reviewers.md) record a machine-validated ordered result with all eight reviewer IDs, bounded integer fit scores, rationales, and the expected survivor order after deterministic conflict filtering. |
| POC-407 | Complete | Create the Draft Panel Summary prompt. | The published [prompt specification and live test evidence](../Prompts/draft-panel-summary.md) record machine-validated per-sentence reviewer attribution, section-source separation, and an exactly empty recommendation. |

## Phase 5 - Agent Flows

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-501 | Complete | Build CheckProposalCompliance with extraction, deterministic rules, Dataverse writes, and integrated notice generation. | Clean and defective scenarios match the manifest and produce the expected Dataverse rows and draft notice. |
| POC-502 | Complete | Build ClassifyAndRouteProposal with program selection and lowest-load officer recommendation. | Three clean proposals receive their expected program and officer recommendations. |
| POC-503 | Complete | Build SuggestReviewers with ranking, all five COI rules, assignment creation, and optional invitation drafts. | Active flow `25b3b702-9f59-4e0b-91fe-fd35942afb16` returns `RV-006`, `RV-008`, and `RV-007`; excludes `RV-001` through `RV-005` in rule order; persists three assignments and optional Outlook draft IDs; and contains `DraftEmail` with no send action. |
| POC-504 | Complete | Build DraftPanelSummary with review-count validation, attribution validation, Word generation, and proposal-stage update. | Active flow `982482e6-46f3-47ab-ba15-bcb4674aafeb` returns a linked DOCX for three submitted reviews with `statementsWithoutSource=0`; the template keeps Panel Recommendation structurally blank. |
| POC-505 | Complete | Pull generated workflow definitions and connection references into VS Code. | Copilot Studio Get changes generated four workflow folders with metadata and parseable workflow JSON, four action bindings, and `connectionreferences.mcs.yml`; the Problems pane is clear. |

## Phase 6 - Topics and Human Confirmation

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-601 | Complete | Build the proposal-status topic against Dataverse. | Live test for proposal `2650001` returned its ID, title, Summary drafted stage, Compliant status, and recommended officer Dr. Aaron Blake; an unknown ID returned the not-found response. |
| POC-602 | Complete | Build the on-demand compliance topic and bind CheckProposalCompliance. | Live clean and return-without-review tests displayed the expected findings, the defective path cited PAPPG 24-1 Chapter II.D.2.i(ii) and linked its draft notice, and both paths required explicit confirmation. |
| POC-603 | Complete | Build the routing presentation and confirmation interaction. | Live recommendation card displayed proposal, program, officer, prior workload, confidence, rationale, merit boundary, and a working Accept action. |
| POC-604 | Complete | Build the suggest-reviewers topic and bind SuggestReviewers. | Card or formatted response shows three candidates and all five exclusion reasons. |
| POC-605 | Complete | Build the draft-invitations path. | User confirmation creates Outlook drafts and the agent states that nothing was sent. |
| POC-606 | Complete | Build the draft-panel-summary topic and bind DraftPanelSummary. | Topic handles insufficient reviews and returns the generated document when eligible. |
| POC-607 | Complete | Verify topic descriptions, trigger phrases, and generative-selection settings. | Unrelated questions do not activate business or utility topics, and intended prompts route correctly. |

## Phase 7 - Trigger and Channels

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-701 | Complete | Create the SharePoint new-file autonomous trigger for Proposal Intake. | Trigger definition is pulled into the workspace and validates cleanly. |
| POC-702 | Complete | Pass ProposalID, Solicitation, file identifier, and file URL into intake processing. | A trigger run shows all four values and updates the matching Proposal row. |
| POC-703 | Complete | Test the trigger with one defective proposal. | It produces cited findings and a draft notice but does not route the proposal. |
| POC-704 | Complete | Test the trigger with one clean proposal. | It completes compliance and produces the expected routing recommendation. |
| POC-705 | Complete | Publish to the approved POC Teams and Microsoft 365 Copilot channels. | The program officer persona completed an authenticated proposal-status interaction in Microsoft 365 Copilot through the published agent. |

## Phase 8 - Acceptance and Demo Readiness

| ID | Status | Task | Completion gate and evidence |
|---|---|---|---|
| POC-801 | Complete | Run compliance test cases and record expected versus actual results. | [TC1 results](../Proposals/poc-801-results.json) pass for all three compliance scenarios with exact governing citations. |
| POC-802 | Complete | Run routing test cases. | [TC2 results](../Proposals/poc-802-results.json) pass for all three clean proposals with expected programs and workload-aware officers. |
| POC-803 | Complete | Run reviewer conflict-screening tests. | [TC3 results](../Proposals/poc-803-results.json) return the three expected eligible reviewers, surface zero planted conflicts, and report all five expected exclusion reasons. |
| POC-804 | Complete | Run panel-summary attribution tests. | [TC4 results](../Proposals/poc-804-results.json) pass with all 12 generated sentences attributed and the Panel Recommendation body blank. |
| POC-805 | Complete | Run policy grounding tests. | [TC5 results](../Knowledge/poc-805-results.json) pass all seven supported, unsupported, and permission-sensitive cases. |
| POC-806 | Complete | Run merit-boundary adversarial tests. | [TC6 results](../Knowledge/poc-806-results.json) pass all five prompts with an exact deterministic-refusal match. |
| POC-807 | Complete | Verify draft-only and confirmation behavior. | [TC7 results](../Proposals/poc-807-results.json) pass for notices, invitations, routing, and summaries after correcting invitation assignment traceability. |
| POC-808 | Complete | Create a repeatable demo reset procedure. | The [reset procedure](../Proposals/reset-demo-state.ps1) restores and validates exact synthetic Dataverse rows, canonical intake files, generated-document state, and Outlook draft state; [live evidence](../Proposals/poc-808-results.json) records a passing reset and idempotence run. |
| POC-809 | Complete | Rehearse the 20-minute demonstration three times. | [Three consecutive rehearsals](../Proposals/poc-809-results.json) passed in 4.69, 5.14, and 6.32 minutes with zero in-run repairs and a passing reset after each run. |
| POC-810 | Complete | Capture final screenshots, links, known limitations, and production recommendations. | The [final demo evidence package](../Evidence/POC-810/DEMO-EVIDENCE.md) includes six representative screenshots, all acceptance links, live demo links, nine known limitations, ten production workstreams, and an explicit owner/sign-off framework; [machine-readable evidence](../Proposals/poc-810-results.json) passes. |
| POC-811 | Complete | Create and publish a dedicated Program Officer Workloads Dataverse view without altering the default Active Program Officers view. | The public view `2e8ef4bb-c2b2-f111-aaac-70a8a5114222` is active, solution-contained, and published with Officer ID, Officer Name, Current Load, and Is New Hire sorted by Current Load ascending; the [deployment validator](../Proposals/deploy-program-officer-workloads-view.ps1) passes in mutation and read-only modes. |
| POC-812 | Complete | Correct the demo reset to restore a true pre-processing intake baseline. | All six current proposals reset to Pending and Received with zero current compliance checks and empty recommendation and assignment lookups; historical COI and review evidence remain intact. The active intake flow now clears stale routing lookups before processing, and [live evidence](../Proposals/poc-812-results.json) records passing apply, idempotence, and deployment-readback checks. |
| POC-813 | Complete | Source-control and validate the POC Demo Proposals view and presenter shortcut. | The published view `ca971c7f-ccb2-f111-aaac-70a8a5114222` is active and solution-contained, filters active proposal numbers beginning with `265`, and shows the seven approved presenter columns in Proposal ID order. The [deployment validator](../Proposals/deploy-poc-demo-proposals-view.ps1) passes, and the external demo script links directly to the view; [live evidence](../Proposals/poc-813-results.json) records the result. |
| POC-814 | Complete | Automate preparation of uniquely named demo upload files and link the presenter script to the automation. | The [preparation script](../Proposals/prep-for-demo.ps1) validates the two canonical source files, creates byte-identical timestamped upload copies under `%TEMP%`, and opens their folder. The external Word demo script links to the repository script with a relative hyperlink; [validation evidence](../Proposals/poc-814-results.json) records passing PowerShell, copy-integrity, DOCX-package, hyperlink-resolution, and Word-open checks. |
| POC-815 | Complete | Create and publish a Reviewer COI Screening Dataverse view. | The public view `11016a4e-abb6-f111-aaae-70a8a5114222` is active, solution-contained, and published with all twelve reviewer business columns in Reviewer ID order. The [deployment validator](../Proposals/deploy-reviewer-coi-screening-view.ps1) passes in mutation and read-only modes and explicitly verifies Institution, Collaborators, Advisees, Advisors, and Last Panel Date. |
| POC-816 | Complete | Correct the Review compliance failures gallery prompt to return a current-week multi-proposal report. | The published agent contains an exact-match topic and a zero-input Dataverse action that returns failed checks with Proposal ID, title, stored reason, and citation for proposals received since Monday. The exact gallery starter passed in both Copilot Studio and Microsoft 365 Copilot without requesting a proposal ID. |
| POC-817 | Complete | Validate all six gallery starters and replace the fixed-proposal panel-summary starter with an eligibility-based workflow. | All six published starters passed in Microsoft 365 Copilot without conflicting results or unintended follow-ons. The panel-summary starter now lists only active proposals that passed compliance, have an assigned program officer, are in Reviewers Suggested or In Review, and have at least two submitted reviews; selection stops at explicit confirmation before document creation. |
| POC-818 | Complete | Preserve proposal context when continuing from confirmed reviewer suggestions to invitation drafting. | The Suggest Reviewers topic now asks a local Yes/No question that names the original proposal. Yes creates three Outlook drafts through the existing action; No creates nothing and ends the workflow. The non-mutating No-path test retained proposal `2650001` and did not start Draft Invitations or ask for the proposal ID again. |
| POC-819 | Complete | Replace opaque invitation draft IDs with reviewer-labeled Outlook links. | The flow retains each raw message ID in Dataverse, retrieves the supported Microsoft Graph `webLink`, and returns reviewer name, reviewer ID, and link to the agent. A confirmed Copilot Studio run displayed three labeled links, and the RV-006 link opened the matching Outlook item marked `[Draft]` and `This message hasn't been sent.` |
| POC-820 | Complete | Add an explicit transition from Recommended Officer to Assigned Officer. | The new starter and topic list recommended-but-unassigned proposals, validate the selection, and require a Yes/No Adaptive Card. The non-triggerable flow revalidates eligibility before mutation. No left `2650001` unchanged; Yes assigned PO-002; the reset passed with `assignedOfficersAfter = 0` and independent readback confirmed the lookup returned to null. |
| POC-821 | Complete | Emulate notification to the assigned program officer without sending to a synthetic identity. | After assignment, the flow creates a no-recipient Outlook draft labeled `[SIMULATED - NOT SENT]`, returns its Graph `webLink`, and treats draft failure as non-blocking. The acceptance run assigned PO-002 and opened the linked Outlook item showing Aaron Blake, proposal `2650001`, `[Draft]`, and `This message hasn't been sent.` |
| POC-822 | Complete | Replace typed panel-summary proposal IDs with clickable selection. | The ready-proposals topic builds a single-select Adaptive Card directly from eligible Dataverse rows and binds the selected proposal number into the existing validation and confirmation path. Copilot Studio acceptance displayed `2650001` as a radio option and advanced it to the existing confirmation card; publication readback was `2026-09-23T18:29:43Z`. |

## Deferred Production Work

Do not mark these as POC blockers. Detailed implementation and gates are in section 19 of [DESIGN.md](DESIGN.md).

- GCC feature-parity validation and approved fallbacks.
- Research.gov, eJacket, and reviewer-database integration.
- Service-owned connections and role-based authorization.
- DLP, Purview, privacy, records retention, and formal policy approval.
- Durable Approvals workflows and reusable document-generation child flows.
- Idempotency, retries, concurrency control, failure queues, and reconciliation.
- Representative evaluation corpus, statistical quality thresholds, and trained extraction if needed.
- Capacity sizing, Copilot Credit forecasting, load testing, and resilience testing.
- Monitoring, alerting, support ownership, runbooks, backup, and disaster recovery.
- Power BI reporting with governed measures and row-level security.
- Managed solutions, environment variables, deployment pipelines, rollback, and release governance.

## Session Notes

Add the newest entry at the top. Keep entries concise and factual.

### September 23, 2026 - POC-822 clickable panel-summary selection verified

- Replaced the free-text proposal-ID question in `PanelSummaryReadyProposals` with an expanded single-select Adaptive Card populated from the filtered ready-proposal records.
- Each option includes the proposal number, title, stage, and submitted-review count; the selected proposal number continues through the existing readiness validation and explicit document-creation confirmation.
- Copilot Studio displayed `2650001` as a radio option; selecting it and choosing **Review summary draft** produced the existing **Create panel summary draft?** card for proposal `2650001` without creating a document.
- The agent was published, and Dataverse reported `publishedon = 2026-09-23T18:29:43Z`.

### September 23, 2026 - POC-821 assignment notification preview verified

- Extended `AssignRecommendedOfficer` to create a no-recipient Outlook draft only after the guarded Dataverse assignment succeeds. The draft and agent response clearly state that nothing was sent.
- Kept notification generation non-blocking by returning separate `notificationStatus` and `notificationDraftUrl` outputs; a draft failure does not roll back a successful assignment.
- The Copilot Studio Yes-path acceptance run returned a labeled link. Outlook showed `[SIMULATED - NOT SENT] Assignment notification - Proposal 2650001`, intended recipient `PO-002 - Dr. Aaron Blake`, `[Draft]`, and `This message hasn't been sent.`
- Updated reset cleanup to delete both synthetic reviewer invitations and assignment-notification drafts.

### September 22, 2026 - POC-820 recommended-officer assignment verified

- Added a read-only eligible-proposals action, an explicit Yes/No assignment topic, and the guarded `AssignRecommendedOfficer` flow. The flow re-reads the selected proposal and refuses mutation unless Recommended Officer is populated and Assigned Officer is empty.
- The No-path acceptance test left proposal `2650001` unassigned. The approved Yes path assigned `PO-002 - Dr. Aaron Blake`, and direct Dataverse readback verified the persisted lookup.
- Updated the demo reset to require zero assigned officers across current synthetic proposals. The post-test reset passed with `assignedOfficersAfter = 0`, and independent readback confirmed proposal `2650001` returned to null.
- Aligned the direct Draft Panel Summary topic with the same compliance, assignment, stage, and submitted-review readiness rules as the gallery path.
- Published with **Force newest version** at 5:49 PM on September 22, 2026. The exact starter appeared in Microsoft 365 Copilot and returned the expected empty-state response after reset; a Copilot Studio regression confirmed the direct panel-summary path rejected unassigned proposal `2650001` before confirmation or document creation.

### September 22, 2026 - POC-819 reviewer invitation links verified

- Replaced user-facing Outlook message hashes with reviewer-labeled Markdown links while retaining raw draft IDs in Dataverse for traceability.
- Added an Office 365 HTTP request for the Graph message `webLink`. The first acceptance run exposed a connector parser error for the relative URI; using the documented full Graph URI corrected it.
- The confirmed Copilot Studio run completed successfully and displayed links for RV-006, RV-008, and RV-007. The RV-006 link opened the matching Outlook item with the expected proposal, reviewer, `[Draft]` marker, and unsent notice.
- No invitations were sent.

### September 22, 2026 - POC-818 reviewer invitation continuation corrected

- Kept the invitation-draft decision inside Suggest Reviewers after the officer confirms the conflict-screened candidates, preserving the original proposal ID instead of starting Draft Invitations as a fresh topic.
- Added an explicit Yes/No question naming proposal `2650001`. Yes invokes the existing action with invitation creation enabled; No creates nothing, reports that nothing was sent, and ends the workflow while leaving chat available.
- The Copilot Studio No-path acceptance test passed without a second proposal-ID question or generated follow-on. The mutating Yes path was not executed during this test.
- Published with **Force newest version** at 4:06 PM on September 22, 2026, and synchronized the published metadata back to source control.

### September 22, 2026 - POC-817 gallery workflows published

- Added purpose-built aggregate topics for signed-in assignments and program-officer workloads, including deterministic empty-state behavior and tie-aware lightest-load reporting. Prevented direct orchestration of the panel-summary action and terminated operational reports without generated restatements or error follow-ons.
- Replaced the fixed `2650001` panel-summary gallery prompt with **Which active proposals are ready for a panel summary?** Eligibility requires Compliant status, an assigned program officer, Reviewers Suggested or In Review stage, and at least two submitted reviews.
- Validated the current empty state in Copilot Studio and Microsoft 365 Copilot. A reversible synthetic test temporarily assigned `2650001`, listed it with PO-002, Reviewers Suggested, and three submitted reviews, then stopped at the confirmation card without creating a document; the assignment was restored to null.
- Published with **Force newest version** at 3:44 PM on September 22, 2026. Microsoft 365 Prompt Gallery displays the revised starter, and all six starters passed their expected routing, data, confirmation, and stopping behavior.

### September 22, 2026 - POC-816 weekly compliance report published

- Reproduced the **Review compliance failures** gallery prompt routing to the single-record Proposal Status topic and incorrectly asking for a proposal ID.
- Added a dedicated multi-record topic whose exact trigger is the gallery text and a non-triggerable, zero-input Dataverse action that filters failed checks to proposals received since Monday and returns each stored rule, reason, and citation.
- The equivalent live Dataverse query returned proposal `2650004`, `Data Management Plan is absent.`, and `PAPPG 24-1 Chapter II.D.2.i(ii)`. The exact starter returned those values in one turn in both Copilot Studio and Microsoft 365 Copilot without asking for a proposal ID.
- Added an orchestration guard that treats report rows as completed checks and prevents the agent from offering to rerun compliance/RWR or implying that a notice exists. Microsoft 365 regression testing returned only the stored failure results and a neutral restatement.
- Published the final correction with **Force newest version** at 2:30 PM on September 22, 2026.

### September 22, 2026 - POC-815 reviewer COI view published

- Created and published the dedicated public **Reviewer COI Screening** view (`11016a4e-abb6-f111-aaae-70a8a5114222`) on the Reviewer table without changing the default reviewer view.
- Included all twelve reviewer business columns, sorted by Reviewer ID. The reviewer-side COI evidence columns are Institution, Collaborators, Advisees, Advisors, and Last Panel Date; proposal-declared conflicts and assignment history remain in their owning tables.
- Added an approved-owner-only idempotent deployment and read-only validator. Both modes pass against the live POC environment, including active-state and solution-containment checks. Authenticated Power Apps validation confirmed all eight reviewers in ascending ID order, the Active filter, all twelve headers, and the planted collaborator, advisor, and last-panel-date values. POC-815 is complete.

### September 17, 2026 - POC-814 demo upload preparation automated

- Added `prep-for-demo.ps1` to validate the clean and defective canonical proposal files, create second-resolution timestamped copies under `%TEMP%`, report their paths, and open the containing folder for the presenter.
- Replaced the external Word demo script's five inline copy commands with one relative hyperlink to the preparation script and a single-command instruction. Office retains its normal security prompt, so the presenter chooses **Run with PowerShell** after opening the link.
- Updated the design, repository instructions, evidence index, and rehearsal checklist. PowerShell parsing, a real preparation run, byte-for-byte file comparison, DOCX package validation, relative-target resolution, and a read-only Word open all passed. [Machine-readable POC-814 evidence](../Proposals/poc-814-results.json) records the result. POC-814 is complete.

### September 17, 2026 - POC-813 proposal presenter view source-controlled

- Captured the manually published **POC Demo Proposals** view (`ca971c7f-ccb2-f111-aaac-70a8a5114222`) in an approved-owner deployment and read-only validation script. Live validation passed for the active `265%` filter, seven presenter columns, Proposal ID sort, and solution containment.
- Updated the design, schema checklist, repository instructions, evidence index, and rehearsal runbook to use the dedicated proposal view while retaining historical COI records outside the presenter list.
- Updated the external Word demo script's first shortcut from the generic Active Proposals view to the direct **POC Demo Proposals** URL and validated the saved hyperlink. [Machine-readable POC-813 evidence](../Proposals/poc-813-results.json) records the result. POC-813 is complete.

### September 17, 2026 - POC-812 pre-processing reset baseline corrected

- Corrected the generated reset seed so all six current proposals begin Pending and Received with no recommended program, recommended officer, assigned officer, or current compliance checks. Preserved the historical proposal and assignment used for recent-panel COI evidence and the three submitted reviews used for panel summaries.
- Strengthened offline and live validation to assert the complete intake lifecycle state, including empty lookups, and added explicit baseline metadata to reset output. The live apply removed three stale compliance checks; the subsequent validation-only run passed with zero drift.
- Updated and deployed the active ProposalIntake-NewFile flow to clear all three routing lookups whenever intake begins. Direct Dataverse readback confirmed the three saved null bindings. [Machine-readable POC-812 evidence](../Proposals/poc-812-results.json) records the correction. POC-812 is complete.

### September 17, 2026 - Program Officer Workloads view published

- Created and published the dedicated public **Program Officer Workloads** view (`2e8ef4bb-c2b2-f111-aaac-70a8a5114222`) without changing the default Active Program Officers view.
- Included Officer ID, Officer Name, Current Load, and Is New Hire; sorted by Current Load ascending and Officer ID ascending. The Program Officer solution component includes its subcomponents, so the view is contained in `NSFProposalIntake`.
- Added an approved-owner-only idempotent deployment and read-only validator. Both modes pass against the live POC environment.
- Updated the design, schema checklist, repository instructions, evidence index, and presenter runbook to use the published Data view rather than the view designer.

### September 16, 2026 - Official NSF agent logo approved

- Confirmed that NSF's public proposal-submission page uses the standard NSF agency logo and does not define a proposal-submission-specific mark. Research.gov uses a separate platform-wide wordmark.
- Received explicit approval to replace the neutral demo glyph with the standard NSF logo. Updated `icon.png` from NSF's official public SVG while preserving the extension's 160-by-160 transparent PNG format.
- Updated the separate Microsoft 365 and Microsoft Teams channel icon with the same approved logo. Copilot Studio saved and normalized the channel asset to 192 by 192 pixels; the live channel preview displays the NSF mark. Teams clients may briefly retain the prior cached icon.
- Updated the design identity decision and retained the completed POC status. The logo change does not alter the agent's synthetic-data restriction or grant production branding approval.

### September 16, 2026 - POC-810 final evidence and production handoff completed

- Added the final demo evidence index with six representative screenshots covering defective intake status, reviewer confirmation, panel-summary creation, attributed Word output, merit refusal, and a grounded policy answer. Recorded SHA-256 hashes and linked all POC-801 through POC-809 acceptance results.
- Verified the live agent, both SharePoint demo libraries, and all five demonstration flows. Documented nine POC limitations, the ten section 19 production workstreams, seven required owner roles, and a written sign-off sequence; production promotion remains explicitly unapproved.
- A capture-only Word session left its synthetic summary file locked after the browser closed. The approved demo owner used supported Graph checkout/check-in operations to clear the stale lock, and the final reset removed the file and passed with zero baseline drift. POC-810 and the synthetic-data POC are complete.

### September 16, 2026 - POC-809 demonstration rehearsals completed

- An excluded pre-acceptance run exposed that compliance completion persisted ReturnWithoutReview but left Proposal Stage as Received. Updated and redeployed ProcessProposalCompliance to persist Stage = Checked; a separate smoke run verified the correction before acceptance began.
- Completed three consecutive full rehearsals in 4.69, 5.14, and 6.32 minutes. Each passed defective and clean intake, reviewer screening and confirmation, panel-summary confirmation, exact merit refusal, and cited policy lookup without editing data or repairing the agent during the run.
- Each post-run reset passed and removed all transient Dataverse rows, uploaded proposal copies, generated drafts, and synthetic Outlook drafts. [Machine-readable POC-809 evidence](../Proposals/poc-809-results.json) records distinct flow runs and checkpoints. POC-809 is complete.

### September 16, 2026 - POC-808 repeatable demo reset completed

- Added a guarded reset procedure with read-only validation by default and explicit `-Apply` mutation. It is restricted to the approved demo identities, validates the synthetic manifest hash, and requires Graph mail and SharePoint write scopes.
- The first live preflight found 135 nonbaseline Dataverse rows, 15 extra intake files, four generated documents, and 24 synthetic invitation drafts. The apply run removed those artifacts, restored the exact seed, and replaced all six canonical proposal bodies in place without creating new trigger items.
- Corrected the seed loader's historical date assertion to compare the date-only panel-service value without a local-time conversion. Final validation confirmed exact Dataverse counts and relationships, six matching proposal bodies, and zero extra rows, files, generated documents, or synthetic drafts.
- Reapplied the procedure from the clean baseline; it passed with zero drift, demonstrating idempotence. [Machine-readable POC-808 evidence](../Proposals/poc-808-results.json) records the reset scope and final state. POC-808 is complete.

### September 16, 2026 - POC-807 draft-only and confirmation testing completed

- Ran TC7 across return-without-review notices, reviewer invitations, routing recommendations, and panel summaries. Live notice confirmation left the generated DOCX explicitly marked as a draft and unsent; routing remains an administrative recommendation with an Accept action.
- Stopped reviewer invitations and panel-summary generation at their confirmation cards. Before confirmation, their latest flow run IDs and persistence timestamps remained unchanged. After invitation confirmation, the authoritative run created exactly three Outlook drafts through `DraftEmail`; no send operation exists.
- TC7 exposed an invitation traceability defect: an unordered top-one lookup attached new draft IDs to older Suggested assignments. Updated the flow to target the exact `assignmentId` carried by the current candidate, redeployed it in place, and validated a clean run with three new assignments linked one-to-one to three draft IDs.
- Pulled the repaired remote flow through the Copilot Studio extension and ran Preview changes; Agent Changes reported `No differences detected`. [Machine-readable TC7 evidence](../Proposals/poc-807-results.json) records all controls and the correction. POC-807 is complete.

### September 16, 2026 - POC-806 merit-boundary testing completed

- Ran the five exact TC6 trigger queries through the authenticated Copilot Studio test pane, covering intellectual-merit scoring, proposal ranking, scientific-strength comparison, broader-impacts quality comparison, and a funding recommendation.
- Every completed acceptance turn returned the same deterministic refusal character-for-character. Each response reserved merit evaluation and funding decisions for program officers and review panels and redirected to the four supported administrative capabilities.
- Discarded an initial rapid batch because prompts overlapped before prior turns completed; no ambiguous capture was counted. Reran the funding case in a new test session.
- [Machine-readable TC6 evidence](../Knowledge/poc-806-results.json) records the expected response and five exact matches. POC-806 is complete.

### September 16, 2026 - POC-805 policy grounding completed

- Ran all seven TC5 prompts from the POC-207 policy matrix through the authenticated Copilot Studio test pane: three supported, two unsupported, and two permission-sensitive cases.
- Supported answers returned the expected policy facts and cited approved PAPPG or synthetic Cluster SOP sources. Unsupported answers stated that the sources did not answer, did not treat user-supplied assumptions as policy, and offered the synthetic escalation path without presenting it as real NSF authority.
- Both operational-library prompts were declined. The responses identified Proposal Intake and Generated Documents as non-knowledge operational libraries, required a purpose-built authorized tool, and disclosed no PI, proposal, filename, or reviewer text.
- [Machine-readable TC5 evidence](../Knowledge/poc-805-results.json) records observed signals, citations, prohibited-signal checks, and all seven passing results. POC-805 is complete.

### September 16, 2026 - POC-804 panel-summary attribution completed

- Ran TC4 through the authenticated Copilot Studio test pane for proposal `2650001` and confirmed document creation. Flow run `08584120340517999757281710296CU10` succeeded using three submitted reviews and reported `statementsWithoutSource=0`.
- Opened the generated SharePoint DOCX in Word Online and validated its eight content controls: three metadata fields and five generated summary sections. All 12 sentence terminators had immediate `R1` through `R3` attribution; no invalid aliases, raw reviewer IDs, or ratings appeared.
- Verified that Panel Recommendation has no generated content control and its body paragraph is empty. The separate static notice remains `Intentionally blank for the program officer.`
- SharePoint read-back found the 20,898-byte draft, and Dataverse read-back confirmed proposal stage `SummaryDrafted` (`511550005`). [Machine-readable TC4 evidence](../Proposals/poc-804-results.json) records the flow and document checks. POC-804 is complete.

### September 16, 2026 - POC-803 reviewer screening completed

- Ran TC3 through the authenticated Copilot Studio test pane using proposal `2650001`. The Suggest Reviewers topic invoked flow run `08584120343493523179506806660CU08` with three requested candidates and `createInvitations=false`.
- The confirmation card returned eligible reviewers `RV-006`, `RV-008`, and `RV-007` in rank order, and excluded `RV-001` through `RV-005` for SameInstitution, RecentCoauthor, AdvisorAdvisee, PIDeclared, and RecentPanelService respectively. No planted conflict appeared among the eligible reviewers.
- Dataverse read-back found exactly three acceptance-run assignments, all `Suggested`, with all five COI checks recorded and null invitation draft IDs and invited dates. The flow response returned an empty invitation-draft array, and the confirmed topic stated that nothing was created or sent.
- Initial starter wording was diverted to the shared action's invitation input. Set the shared SuggestReviewers action to `triggerCondition: false` so only the Suggest Reviewers and Draft Invitations topics can invoke it with their explicit false/true bindings; aligned the starter text with the topic's proposal-ID question.
- Applied the final agent changes through the Copilot Studio extension and ran Preview changes; Agent Changes reported `No differences detected`.
- [Machine-readable TC3 evidence](../Proposals/poc-803-results.json) records the flow output, exclusions, rankings, assignment IDs, and draft-only checks. POC-803 is complete.

### September 16, 2026 - POC-802 routing acceptance started

- Live officer workloads were `PO-001=6`, `PO-002=5`, and `PO-003=5` after earlier routing runs. TC2 will reset only these synthetic counters to the manifest baseline `6/2/3` before sequentially testing clean proposals `2650001`, `2650002`, and `2650003`.
- Reset the synthetic workload counters to `6/2/3`, then uploaded and processed the three clean proposals sequentially to avoid concurrent updates to PO-003.
- Runs `08584120359742478916109819394CU09`, `08584120358537711852538970357CU27`, and `08584120357757186459400705191CU20` all returned `Compliant`, `Recommended`, High confidence, the expected program, and the expected officer. Prior loads were `2`, `3`, and `4` respectively.
- Dataverse read-back found all three proposals at `Routed` with matching program/officer lookups. Final workloads were `PO-001=6`, `PO-002=3`, and `PO-003=5`; no clean run created a notice draft.
- [Machine-readable TC2 evidence](../Proposals/poc-802-results.json) records expected versus actual values. POC-802 is complete.

### September 16, 2026 - POC-801 compliance acceptance completed

- Uploaded fresh TC1 inputs `2650004 - Emergency Response Networks POC801.docx`, `2650005 - Algal Bloom Forecasting POC801.docx`, and `2650006 - Immersive Engineering Studios POC801.docx` to Proposal Intake at `2026-09-16T14:53:56Z` through `14:53:58Z`.
- The expected failures are Data Management and Sharing Plan (`PAPPG 24-1 Chapter II.D.2.i(ii)`), 15-page Project Description limit (`PAPPG 24-1 Chapter II.D.2.d(ii)`), and Current and Pending Support (`PAPPG 24-1 Chapter II.D.2.h(ii)`).
- Runs `08584120364179152987165875354CU25`, `08584120364179152986165875354CU04`, and `08584120364179152985165875354CU04` all succeeded. Each returned `ReturnWithoutReview`, the expected failed rule and citation, a notice draft URL, `routingInvoked: false`, and `routingStatus: NotRouted`.
- Dataverse read-back found eight latest checks and exactly one expected failure per proposal, persisted `ReturnWithoutReview`, no recommended officer, and the POC-801 file URL. SharePoint read-back found all three nonempty notices regenerated between `14:55:00Z` and `14:55:04Z`.
- [Machine-readable TC1 evidence](../Proposals/poc-801-results.json) records expected versus actual values. POC-801 is complete.

### September 15, 2026 - Agent clone synchronized after POC-705

- Ran Copilot Studio Apply changes and verified the extension completed `powerplatformls/syncPush` for `NSF Proposal Intake Assistant`.
- Pulled the approved `GroupMembership` access policy and current publication timestamp into `settings.mcs.yml`.
- Moved the solution-exported Proposal Intake trigger and compliance/routing child workflows from the extension-managed `workflows/` folder to `Docs/Proposals/solution-workflows/`. The extension does not round-trip these Power Automate-owned artifacts.
- Ran Copilot Studio Preview changes after the move; Agent Changes reported `No differences detected`.

### September 15, 2026 - POC-705 approved channels published

- Published the synchronized `NSF Proposal Intake Assistant` at `2026-09-15T21:54:23Z`, forcing the newest version into existing Teams conversations. The published manifest registers Teams app version `1.0.7` and Microsoft 365 Copilot title `T_c9455332-f34c-e740-d9d1-d002a01b7419`; no public web channel is configured.
- Changed organization-wide access to `No permissions, unless specified`. The share dialog lists only Galen Brown (`galenb@caldova51155962.onmicrosoft.com`) as Owner, and Dataverse reports access-control policy `Group membership`.
- Opened the published agent from its Microsoft 365 Copilot share link as Galen. Starter prompts rendered under the correct agent identity, and the first Dataverse call required Galen to authorize the connection.
- Asked for proposal `2650001` status. The published channel returned title `Resilient Federated Control for Community Microgrids`, stage `Routed`, compliance status `Compliant`, and recommended officer Dr. Aaron Blake.
- Four tools continue to use the author's credentials, matching the POC's single demo connection-owner model. This remains a production blocker; production must use service-owned autonomous connections and delegated user-context connections as described in the design.

### September 15, 2026 - POC-704 clean intake completed

- Added solution-aware flow `ProcessProposalRouting` (`8c9d5b95-02a4-4d9c-a948-88e584500704`) with supported `Button` and `PowerApp` request/response kinds while preserving the tested `ClassifyAndRouteProposal` definition and agent-facing flow.
- Extended `ProposalIntake-NewFile` with an explicit condition that invokes routing only when compliance returns `Compliant`; the final payload includes the routing status, program, confidence, rationale, officer, and prior workload.
- Uploaded synthetic clean proposal `2650001 - Resilient Federated Control POC704b.docx`. Trigger run `08584120995977102171636729704CU02` succeeded from `2026-09-15T21:21:28Z` through `21:21:50Z`.
- Compliance returned `Compliant`, all eight administrative checks passed, no notice was created, and routing returned `Recommended` with High confidence for `Secure Distributed Systems`.
- The run recommended `PO-002`, Dr. Aaron Blake, with prior load 4. Dataverse persisted the compliant status, routed stage, expected program and officer lookups, and incremented PO-002's load to 5.

### September 15, 2026 - POC-703 defective intake completed

- Power Automate rejected direct child invocation of agent flow `CheckProposalCompliance` because its `Skills` trigger is not a supported child-flow trigger (`ChildFlowUnsupportedTriggerType`). Added solution-aware flow `ProcessProposalCompliance` (`7b8c4a84-f193-4c8b-9837-77d473400703`) with supported `Button` and `PowerApp` request/response kinds while preserving the tested compliance definition and the agent-facing flow.
- Extended `ProposalIntake-NewFile` to call the compliance child after SharePoint and Dataverse intake metadata persistence. The parent contains no routing action and emits compliance status, findings, notice URL, and an explicit `routingInvoked` flag.
- Uploaded synthetic defective proposal `2650004 - Emergency Response Networks POC703b.docx`. Trigger run `08584121003661335887402706535CU10` succeeded from `2026-09-15T21:08:39Z` through `21:09:01Z`.
- The final payload returned `ReturnWithoutReview`, the failed Data Management Plan finding with citation `PAPPG 24-1 Chapter II.D.2.i(ii)`, the draft notice URL, and `routingInvoked: false`.
- Dataverse stored all eight compliance checks and `nsf_compliancestatus = 511550002`; the recommended officer remained empty. The existing recommended-program lookup is seeded baseline data for defective proposal `2650004`, not a routing result.
- SharePoint regenerated `2650004 - Return-Without-Review Notice.docx` at `2026-09-15T21:09:01Z`, matching this run's completion time.

### September 15, 2026 - POC-702 intake metadata completed

- Added repeatable deployment script `deploy-proposal-intake-flow.ps1`. It preserves the SharePoint trigger, derives the proposal ID from controlled filename format `NNNNNNN - Title.ext`, looks up `nsf_proposals`, and handles a missing Proposal row without writing metadata.
- The matched path updates SharePoint `ProposalID`, `Solicitation`, `ReceivedDate`, and `IntakeStatus`; resets the Dataverse Proposal file URL, received date, stage, compliance status, and days in stage; and emits a final verification payload.
- Uploaded `2650005 - Algal Bloom Forecasting POC702.docx`. Trigger run `08584121013843922651197739662CU10` succeeded, including `PatchFileItem` and `UpdateOnlyRecord`.
- Compose returned proposal `2650005`, solicitation `NSF POC-CED-26`, the file identifier, file URL, and `proposalFound: true`. Independent SharePoint and Dataverse reads confirmed the persisted metadata and Proposal-row updates.
- Exported unmanaged solution `NSFProposalIntake`; the synchronized workflow JSON is semantically identical to the export, includes both connector references, and passes diagnostics.

### September 15, 2026 - POC-701 SharePoint fallback published

- Created and published automated flow `ProposalIntake-NewFile` (`b8af4e6a-42b1-f111-aaac-70a8a5114222`) with SharePoint `When a file is created (properties only)` on site `NSF Cluster Operations - POC` and library `Proposal Intake`.
- Added a Compose checkpoint containing dynamic values for `ProposalID`, `Solicitation`, `{Identifier}`, `{FilenameWithExtension}`, and `{Link}`; the flow details page reports `Status: On`.
- Uploaded synthetic file `POC701-trigger-test-2650001.docx`. No run appeared, and the details page subsequently reported a problem that must be fixed before the flow can trigger; both new and classic Flow checker panels rendered blank while portal requests returned 404 and JavaScript errors.
- Backend trigger history identified `ResponseSwaggerSchemaValidationFailure`: SharePoint emitted the file-created event before required property `ProposalID` existed. Changed `ProposalID`, `Solicitation`, `ReceivedDate`, and `IntakeStatus` to optional library columns, reselected `Proposal Intake` in the trigger, and saved to refresh the connector schema.
- Uploading `POC701-trigger-test-2650004.docx` then produced successful trigger history and run `08584121021521053834552615368CU29`. Compose returned the file identifier, file name, and file URL; `ProposalID` and `Solicitation` were blank because the upload did not supply metadata.
- Added workflow `b8af4e6a-42b1-f111-aaac-70a8a5114222` to unmanaged solution `NSFProposalIntake`, exported it through Dataverse, and synchronized the generated `workflow.json` and `metadata.yml` into the workspace. The JSON parses cleanly and is semantically identical to the exported artifact.
- The earlier flow record `a22c628b-40b1-f111-aaac-70a8a5114222` is orphaned: both designers return XRM `0x80040217` because its workflow entity does not exist.
- Connector search and Power Automate Copilot both confirmed that Microsoft Copilot Studio `Execute Agent and wait` is unavailable in environment `test`. POC-701 remains in progress pending a test run and a pullable trigger artifact or an approved architecture exception.

### September 15, 2026 - POC-701 autonomous trigger blocked

- Opened the supported Copilot Studio Add trigger path and selected SharePoint `When a file is created (properties only)` as required by the design.
- The generated Power Automate designer rejected the request before showing connector fields with `The template definition is invalid`; support session ID `ad027e30-b139-11f1-bc58-4b9807b44ecd` at `2026-09-15T19:14:30.726Z`.
- Re-saved classic and generative orchestration in sequence, published the agent, waited for the gallery save transaction to finish, and retried with a new widget ID. The designer remained blank and created no trigger artifact.
- A fresh Copilot Studio session then reported aborted shell, feature-configuration, tenant-settings, and environment-metadata requests. No trigger or backing workflow was created, so `POC-701` remains blocked and uncommitted until the service recovers.

### September 15, 2026 - POC-607 topic routing verified

- Verified generative orchestration, high content moderation, disabled general model knowledge and file analysis, enabled semantic search, administrative-only instructions, and draft-only consequential actions.
- Live prompts routed to Check Compliance, Route Proposal, Suggest Reviewers, Proposal Status, Draft Invitations, Draft Panel Summary, and Merit Review Refusal as intended.
- A Generated Documents listing request selected Operational Library Boundary, disclosed no operational data, and invoked no tool.
- Unrelated weather and staff-scheduling requests used approved-knowledge search and then Fallback without activating a business topic.
- Replaced the broad system Escalate recognizer with focused human-support phrases after staff scheduling exposed a false positive; the regression prompt now falls back, while an explicit human-representative request still selects Escalate.
- The synchronized topics are diagnostics-clean. Marked `POC-607` `Complete`; next action is `POC-701` create the SharePoint new-file autonomous trigger for Proposal Intake.

### September 15, 2026 - POC-606 panel-summary topic completed

- Exposed the DraftPanelSummary proposal-ID input and added `Draft Panel Summary` with focused triggers and explicit confirmation before document generation.
- Used the flow's authoritative summary URL as the result discriminator because connector number outputs were not stable under topic condition coercion; the flow emits a URL only after its minimum-review and attribution validations pass.
- Live insufficient-review test for proposal `2650002` reported zero submitted reviews, the two-review NSF 26-200 minimum, and that no document was created.
- Live eligible test for proposal `2650001` returned the Generated Documents DOCX link for three submitted reviews, confirmed zero unattributed statements, and stated that Panel Recommendation remains blank for the program officer.
- The action and topic are schema-clean and the synchronized live topic is enabled with no listed errors. Marked `POC-606` `Complete`; next action is `POC-607` verify topic routing metadata and generative selection.

### September 15, 2026 - POC-605 invitation drafts completed

- Added and synchronized `Draft Invitations` with focused triggers, proposal-ID capture, and an explicit confirmation card before invoking SuggestReviewers with three candidates and invitation creation enabled.
- Live test for proposal `2650001` stopped at the confirmation card and stated that nothing would be sent before any consequential action ran.
- Clicking Create drafts returned three distinct Outlook message IDs and stated that nothing was sent; the drafts remain pending program officer review and manual approval.
- The topic is schema-clean and the synchronized live topic is enabled with no listed errors. Marked `POC-605` `Complete`; next action is `POC-606` build the draft-panel-summary topic.

### September 15, 2026 - POC-604 reviewer suggestions completed

- Added and synchronized `Suggest Reviewers` with focused trigger phrases, proposal-ID capture, fixed suggestion-only flow inputs, formatted candidate and exclusion evidence, and an explicit confirmation card.
- Removed a redundant proposal preflight after live testing showed its shared Dataverse action could prompt for filter input in the newly compiled topic; the validated SuggestReviewers flow owns proposal lookup and validation.
- Live test for proposal `2650001` returned eligible reviewers `RV-006`, `RV-008`, and `RV-007` in order, each with expertise rationale and all five COI checks passed.
- The card excluded `RV-001` SameInstitution, `RV-002` RecentCoauthor, `RV-003` AdvisorAdvisee, `RV-004` PIDeclared, and `RV-005` RecentPanelService with the expected reasons.
- Confirmation produced the expected acknowledgment and stated that no invitation drafts were created or sent. Marked `POC-604` `Complete`; next action is `POC-605` build the draft-invitations path.

### September 15, 2026 - POC-603 routing presentation completed

- Exposed the ClassifyAndRouteProposal proposal-ID input and added `Route Proposal` with focused triggers, proposal-title lookup, all routing-status branches, and a uniquely identified Accept action.
- Live test for compliant proposal `2650001` displayed Secure Distributed Systems, Dr. Aaron Blake (`PO-002`), prior workload 3, High confidence, the subject-matter rationale, and the explicit no-merit/no-funding boundary.
- Clicking Accept produced the expected acknowledgment; Dataverse read-back confirmed Routed (`511550002`), the persisted program/officer lookups, and PO-002 workload incremented from 3 to 4.
- Live negative test for `2650004` stated that a noncompliant proposal cannot be routed and did not display an acceptance card. The VS Code Problems pane is clear. Marked `POC-603` `Complete`; next action is `POC-604` build the suggest-reviewers topic.

### September 15, 2026 - POC-602 compliance topic completed

- Added canonical Proposal Intake file URLs to the repeatable synthetic Dataverse seed, uploaded the two missing synthetic proposal files, and populated only `nsf_fileurl` on all six live Proposal rows.
- Exposed the CheckProposalCompliance trigger inputs, extended the typed one-row Proposal lookup with `nsf_fileurl`, and added `Check Compliance` with focused triggers, proposal lookup, all three result branches, and uniquely identified confirmation cards.
- Live test for `2650001` returned `Compliant`, displayed all-eight-rules-passed findings, required confirmation, and stated that no notice was created or sent.
- Live test for `2650004` returned `ReturnWithoutReview`, cited `PAPPG 24-1 Chapter II.D.2.i(ii)`, linked the 11,219-byte draft notice, required confirmation, and stated that the notice remained a draft and was not sent.
- Proposal manifest and seed validators pass, the VS Code Problems pane is clear, and Dataverse read-back confirms compliance choice values `511550001` and `511550002`. Marked `POC-602` `Complete`; next action is `POC-603` build the routing presentation and confirmation interaction.

### September 15, 2026 - POC-601 proposal-status topic completed

- Added the purpose-specific Microsoft Dataverse List rows action for the Proposal table with a one-row limit, fixed selected columns, and recommended-officer expansion.
- Added `Proposal Status` with five focused trigger phrases, proposal-ID prompting, escaped OData filtering, readable Dataverse choice labels, and an explicit not-found branch.
- Live test for `2650001` returned `Resilient Federated Control for Community Microgrids`, `Summary drafted`, `Compliant`, and recommended officer `Dr. Aaron Blake`.
- Live test for `9999999` returned the expected not-found response. The VS Code Problems pane is clear. Marked `POC-601` `Complete`; next action is `POC-602` build the on-demand compliance topic.

### September 15, 2026 - POC-505 generated workflow source synchronized

- Attached `CheckProposalCompliance`, `ClassifyAndRouteProposal`, `SuggestReviewers`, and `DraftPanelSummary` to the live agent as tools, then used Copilot Studio Preview changes and Get changes to generate the local source.
- Verified exactly four workflow folders, each containing extension-generated `metadata.yml` and parseable `workflow.json`, with IDs matching the four active flows.
- Verified four generated action bindings and `connectionreferences.mcs.yml` covering Dataverse, OneDrive for Business, SharePoint, Word Online (Business), and Office 365 Outlook.
- Confirmed the VS Code Problems pane reports no errors. Marked `POC-505` `Complete`; next action is `POC-601` build the proposal-status topic against Dataverse.

### September 15, 2026 - POC-504 panel summary flow completed

- Created and activated `DraftPanelSummary` (`982482e6-46f3-47ab-ba15-bcb4674aafeb`) with submitted-review retrieval, deterministic `R1` through `Rn` aliases, the published Draft Panel Summary model (`46441800-8c43-44ed-8a6f-955acf9626a4`), attribution validation, Word population, SharePoint creation, and Proposal stage update.
- The minimum-review test for proposal `2650002` returned `reviewCount=0`, a blank URL, and no stage change.
- The three-review test for proposal `2650001` returned `reviewCount=3`, `statementsWithoutSource=0`, and a link to `Generated Documents/Panel Summaries/2650001 - Panel Summary Draft.docx`; SharePoint read-back found exactly one 11,614-byte document and Dataverse read-back confirmed `SummaryDrafted` (`511550005`).
- Document creation is gated on five nonempty generated sections, sentence-level bracket counts, an empty `panelRecommendation`, and absence of raw `RV-` identifiers. The existing template validator proves the Panel Recommendation body is blank and outside all flow-populated controls.
- Added repeatable deployment script `Docs/Proposals/deploy-draft-panel-summary-flow.ps1`. Marked `POC-504` `Complete`; next action is `POC-505` pull generated workflow definitions and connection references into VS Code.

### September 15, 2026 - POC-503 reviewer suggestion flow completed

- Created and activated `SuggestReviewers` (`25b3b702-9f59-4e0b-91fe-fd35942afb16`) with the published Rank Reviewers model, sequential deterministic COI screening, assignment creation, optional Outlook drafts, and proposal-stage update.
- Live suggestion-only test returned eligible reviewers `RV-006`, `RV-008`, and `RV-007`; returned `RV-001` SameInstitution, `RV-002` RecentCoauthor, `RV-003` AdvisorAdvisee, `RV-004` PIDeclared, and `RV-005` RecentPanelService in deterministic rule order; created three Suggested assignments; and returned no draft IDs.
- Live invitation test returned three distinct Outlook message IDs and persisted the same IDs on three `Invited` assignments. The `DraftEmail` connector operation created each message from the approved synthetic template; the deployed definition contains neither `SendDraftEmail` nor `SendEmail`.
- Added repeatable deployment script `Docs/Proposals/deploy-suggest-reviewers-flow.ps1`, widened `nsf_invitationdraftid` from 100 to 500 characters for Outlook message IDs, and retained authoritative `HIST-RA-001` evidence for the panel-service rule.
- Marked `POC-503` `Complete`; next action is `POC-504` build DraftPanelSummary.

### September 15, 2026 - POC-503 started; Outlook authorization blocked

- Confirmed the published Rank Reviewers model (`88ef1768-6dda-4f48-93a5-24f12235a207`), all eight live Reviewer conflict profiles, and the primary proposal fields required for deterministic screening.
- Found that the seed generator and loader omitted the required `HIST-ORTIZ-001` proposal and `HIST-RA-001` Review Assignment while asserting zero assignments. Added separate historical-proposal and review-assignment seed sets, lookup-aware loading, count/reference validation, and authoritative planted-scenario checks.
- Widened the unmanaged `nsf_proposalnumber` column from 7 to 50 characters so the required historical identifier fits; published and verified the live metadata and updated the schema checklist.
- Regenerated and validated the seed: six intake proposals, one historical proposal, eight reviewers, three reviews, one historical assignment, and three compliance checks. Live read-back verified `HIST-RA-001` links `HIST-ORTIZ-001` to `RV-005` with Submitted status and panel-service date `2026-03-15`.
- Environment connection inventory contains no Office 365 Outlook connection. Power Automate reaches the connector creation dialog, but the integrated browser blocks its OAuth consent popup. Owner/action: Galen must allow popups for `make.powerautomate.com`, select Office 365 Outlook > Create, and complete Microsoft consent; then resume POC-503 flow construction and tests.

### September 15, 2026 - POC-502 proposal routing flow completed

- Built and activated agent flow `ClassifyAndRouteProposal` (`4fb0710b-ae67-4e4d-b58b-3fdf4aa5ac6f`) with a compliant-status guard, current Dataverse Program serialization, the published Classify Proposal prompt, live program-officer relationship filtering, deterministic lowest-load selection, Proposal recommendation writes, and officer-load increments.
- Added repeatable deployment script `Docs/Proposals/deploy-classify-and-route-flow.ps1`; PowerShell parsing passed and the deployed flow is `Started` with a `Skills` request trigger.
- Live tests routed `2650001` to Secure Distributed Systems and `PO-002`, `2650002` to Coastal Ecosystem Dynamics and `PO-003`, and `2650003` to STEM Learning Pathways and `PO-003`; all classifier responses reported `High` confidence.
- Dataverse read-back confirmed all three recommended Program and Program Officer lookups, `Routed` stage, and expected final loads `PO-001=6`, `PO-002=3`, and `PO-003=5`.
- Negative test `2650004` returned `NotCompliant` with blank recommendation fields and left all officer loads unchanged. Marked `POC-502` `Complete`; next action is `POC-503` build SuggestReviewers.

### September 10, 2026 - POC-501 proposal compliance flow completed

- Built and published agent flow `CheckProposalCompliance` (`3fa582f1-56ad-f111-aaac-70a8a5114222`) with SharePoint DOCX retrieval, OneDrive PDF conversion, AI Builder OCR and structured extraction, eight deterministic administrative rules, Dataverse Compliance Check writes, Proposal status update, and temporary-file cleanup.
- Integrated the six-field Return-Without-Review Word template and SharePoint draft creation under `Generated Documents/Notices`; the live definition has one `ReturnWithoutReview` predicate and cleanup runs after every condition outcome. Flow checker reports zero errors and zero warnings.
- Live tests for defective proposals `2650004`, `2650005`, and `2650006` each returned HTTP 200, `ReturnWithoutReview`, the expected single failed rule and PAPPG citation, eight current Compliance Check rows, and a nonempty DOCX notice.
- Re-ran clean proposal `2650001`; it returned HTTP 200 and `Compliant`, all eight rules passed, notice actions were skipped, cleanup succeeded, and `noticeDraftUrl` was empty. Removed the older duplicate validation batch so eight current clean checks remain.
- Marked `POC-501` `Complete`; next action is `POC-502` build ClassifyAndRouteProposal.

### September 10, 2026 - POC-407 panel summary prompt completed

- Created and published the `Draft Panel Summary` AI Builder prompt with GPT-4.1 mini, native JSON output, and typed `Reviews` and `Template` inputs.
- Mapped stable citation aliases `R1` through `R3` to the three synthetic Dataverse reviewer IDs and limited model input to section-specific review text plus summary statements.
- Corrected two pre-save failures: grouped paragraph-level citations and a technical privacy weakness placed under Broader Impacts. The final input contract and prompt enforce per-sentence citation and source-field separation.
- Machine validation confirmed exactly six fields, attribution on every generated sentence, supplied aliases only, no ratings or raw reviewer IDs, clean section mapping, and an exactly empty `panelRecommendation`.
- Verified the saved prompt is Published and owned by Galen Brown in My prompts. Marked `POC-407` `Complete`; Phase 4 is complete and the next action is `POC-501` build CheckProposalCompliance.

### September 10, 2026 - POC-406 reviewer ranking prompt completed

- Created and published the `Rank Reviewers` AI Builder prompt with GPT-4.1 mini, native JSON output, and typed `ProposalKeywords`, `Abstract`, and `CandidateList` inputs.
- Limited ranking to subject-matter overlap and prohibited proposal-quality, merit, funding-priority, likely-opinion, favorability, prestige, personal-characteristic, conflict, and availability judgments.
- Live-tested proposal `2650001` against all eight synthetic reviewer profiles; machine validation confirmed the exact root field, eight unique expected IDs, integer scores from 1 through 5 in descending order, and a rationale for every row.
- Applying the five manifest conflict exclusions to the ranked output retained `RV-006`, `RV-008`, and `RV-007` in the expected order; conflict screening remains a deterministic POC-503 flow responsibility.
- Verified the saved prompt is Published and owned by Galen Brown in My prompts. Marked `POC-406` `Complete`; next action is `POC-407` create and test the Draft Panel Summary prompt.

### September 10, 2026 - POC-405 proposal classification prompt completed

- Created and published the `Classify Proposal` AI Builder prompt with GPT-4.1 mini, native JSON output, and typed `Title`, `Abstract`, `Keywords`, and `ProgramList` inputs.
- Kept classification limited to administrative subject-matter routing, treated all inputs as untrusted data, and prohibited merit, impact-quality, and funding-priority assessment.
- Constrained output to an exact supplied program name or `NoneMatched`, `High`/`Medium`/`Low` confidence, and a two-sentence subject-fit rationale; officer selection and workload ordering remain deterministic flow operations.
- Live-tested clean proposals `2650001`, `2650002`, and `2650003` against the same three-profile program list; all returned their exact expected program names with valid JSON and `High` confidence.
- Verified the saved prompt is Published and owned by Galen Brown in My prompts. Marked `POC-405` `Complete`; next action is `POC-406` create and test the Rank Reviewers prompt.

### September 10, 2026 - POC-404 proposal structure prompt completed

- Created and published the `Extract Proposal Structure` AI Builder prompt with GPT-4.1 mini, a typed `DocumentText` input, native JSON output, an untrusted-data boundary, and no merit or funding evaluation.
- Live-tested extracted text from clean proposal `2650001`; the response matched all identity fields, all eight canonical sections, three Project Description pages, SciENcv, and the research-security certification marker.
- Live-tested defective proposal `2650004`; tightened the allow-list filter after an initial response treated a certification heading as a section, then confirmed exactly seven canonical sections with Data Management Plan absent and every other expected field correct.
- Recorded AI Builder's `{ "item": "value" }` array-row representation for downstream flow parsing and explicitly documented that the future flow must extract DOCX text before prompt invocation.
- Verified the saved prompt is Published and owned by Galen Brown in My prompts. Marked `POC-404` `Complete`; next action is `POC-405` create and test the Classify Proposal prompt.

### September 10, 2026 - POC-403 panel summary skeleton completed

- Added a deterministic Panel Summary Skeleton DOCX generator with proposal metadata and flow-populated controls for Summary of Proposal, Intellectual Merit Strengths and Weaknesses, and Broader Impacts Strengths and Weaknesses.
- Added an attributed payload for proposal `2650001`; validation rejects generated text that does not follow the required `sentence. [R1]` reviewer-attribution form.
- Kept Panel Recommendation outside all flow-populated controls and validated that the paragraph following its heading is structurally empty for program officer authorship.
- Desktop Word recognized all eight intended controls as plain text and no recommendation control. Uploaded the template to SharePoint and read back Word MIME type, `TemplateType=PanelSummarySkeleton`, and `EffectiveDate=2026-09-10`.
- Marked `POC-403` `Complete`; next action is `POC-404` create and test the Extract Proposal Structure AI Builder prompt.

### September 10, 2026 - POC-402 reviewer invitation draft template completed

- Selected the design-approved Outlook HTML body option instead of a Word intermediary and added explicit synthetic, draft-only, conflict-disclosure, response-date, proposal, reviewer, program, and program-officer fields.
- Added a versioned payload for eligible reviewer `RV-006` and primary proposal `2650001`, using the reserved `reviewer-poc.example` address.
- Added a validator that HTML-encodes payload values, rejects missing or unresolved fields, creates a message only through Microsoft Graph `/me/messages`, and never calls a send endpoint.
- Live read-back confirmed the exact synthetic recipient and subject, all required body signals, `isDraft=true`, and `Sent=false`. The test draft was deleted after verification.
- Marked `POC-402` `Complete`; next action is `POC-403` create the Panel Summary Skeleton Word template.

### September 10, 2026 - POC-401 return-without-review template completed

- Added a deterministic DOCX generator for the Return-Without-Review Notice with six Word plain-text content controls: `ProposalId`, `ProposalTitle`, `PrincipalInvestigator`, `SubmittingInstitution`, `FailedRulesWithCitations`, and `DraftNoticeDate`.
- Added a versioned test payload for defective proposal `2650004`; the generator populated every required control and verified that no placeholder remained.
- Opened the generated template through desktop Word automation and confirmed Word recognizes all six controls as plain-text content controls with matching titles and tags.
- Uploaded the template to the live Templates library and read back `TemplateType=ReturnWithoutReviewNotice` and `EffectiveDate=2026-09-10`. Word Online flow integration remains owned by `POC-501`.
- Marked `POC-401` `Complete`; next action is `POC-402` create and test the reviewer invitation template or Outlook draft body.

### September 10, 2026 - POC-308 connection-owner access verified with accepted exceptions

- Selected `isaacf@Caldova51155962.onmicrosoft.com` as the connection-owner identity and synchronized it as an enabled Dataverse user. Added it to the NSF Cluster Operations - POC Microsoft 365 group.
- Created and assigned `NSF Proposal Intake POC User` with 42 organization-level runtime privileges limited to create, read, write, delete, append, and append-to on the seven NSF tables; Basic User is also assigned.
- Live effective-access tests as Isaac passed for Dataverse `WhoAmI`, all six seeded Proposal records, and the approved POC SharePoint site.
- Strict identity isolation failed because Isaac has pre-existing unrelated tenant group memberships and the built-in Salesperson role. Removal of Salesperson was skipped; live probes confirmed non-NSF Account reads and tenant-root SharePoint access. The user accepted these as POC exceptions.
- Verified the configured agent surface remains isolated: only Policy, Solicitations, and Cluster SOPs are knowledge sources; operational libraries have a deterministic boundary; and no broad Dataverse tool is attached. Added [POC-308 Access Verification](POC-308-ACCESS.md), marked Phase 3 complete, and set `POC-401` as the next action.

### September 10, 2026 - POC-307 Dataverse seed load completed

- Added `load-dataverse-seed.ps1`, an idempotent Dataverse Web API upsert restricted to the approved `galenb@caldova51155962.onmicrosoft.com` account and current synthetic manifest hash.
- Resolved the seven live entity sets, local-choice numeric values, lookup navigation properties, and `nsf_ProgramOfficer_Programs` many-to-many relationship from Dataverse metadata before loading.
- Loaded and live-verified exact counts of three programs, three program officers, six proposals, eight reviewers, three reviews, and three compliance checks; Review Assignment remained empty as expected before reviewer confirmation workflows run.
- Verified Proposal-to-Program/Officer, Review-to-Proposal/Reviewer, Compliance-Check-to-Proposal, and Program-Officer-to-Program relationships. A second full run retained exact counts, proving the upsert does not duplicate records.
- Performed no SharePoint uploads, so autonomous intake triggers were not invoked. Marked `POC-307` `Complete`; next action is `POC-308` verify positive POC access and negative isolation for the demo connection owner.

### September 10, 2026 - POC-306 planted scenarios completed

- Generated three Compliance Check seed rows for proposals `2650004`, `2650005`, and `2650006`, preserving each expected failed rule, PAPPG citation, and ReturnWithoutReview status.
- Added `validate-planted-scenarios.ps1` to execute the existing DOCX and seed validators before evaluating every planted condition from generated artifacts.
- Verified reviewer conflicts through five distinct physical evidence paths: same institution for `RV-001`, recent coauthor JSON for `RV-002`, advisor relationship for `RV-003`, PI declaration for `RV-004`, and recent panel date for `RV-005`.
- Verified `RV-006`, `RV-008`, and `RV-007` remain available and carry none of the five planted conflict indicators. The validator passed with three compliance defects, five distinct exclusions, and three eligible reviewers.
- Marked `POC-306` `Complete`; next action is `POC-307` load the synthetic records and resolved relationships into Dataverse without uploading SharePoint trigger files.

### September 10, 2026 - POC-305 Dataverse seed records completed

- Added three synthetic submitted reviews for primary proposal `2650001`, one from each eligible reviewer, with partially agreeing and disagreeing text suitable for later attribution tests.
- Enriched the six proposal manifest entries with synthetic abstracts, keywords, and fixed received timestamps needed by the Dataverse rows.
- Added `generate-dataverse-seed.ps1` and generated `dataverse-seed.json` with physical `nsf_*` column names and stable references for three programs, three officers, six proposals, eight reviewers, and three reviews. No live Dataverse writes or trigger-file uploads occur in this step.
- Added `validate-dataverse-seed.ps1`; it verifies the current manifest hash, synthetic-only classification, exact counts, ordered identifiers, and review lookup references. Regeneration produced the same SHA-256 hash and all validations passed.
- Marked `POC-305` `Complete`; next action is `POC-306` verify the planted compliance defects and all five reviewer-conflict types in the seed data while retaining three eligible reviewers.

### September 10, 2026 - POC-304 deterministic synthetic generator completed

- Made the DOCX generator byte-for-byte deterministic by assigning a fixed valid timestamp to every package entry and allowing isolated output directories.
- Added `validate-generation.ps1`, which generates all six proposals twice in temporary directories, compares each SHA-256 hash, and runs the expected-results validator against the isolated output.
- Added an explicit synthetic-only data classification to the manifest and assertions that generated proposal identifiers, titles, investigators, institutions, programs, and solicitations match it.
- Regenerated the six tracked DOCX fixtures with canonical package timestamps. The generation validator passed for all six files, and the manifest validator passed for six proposals, three officers, eight reviewers, five conflict exclusions, and three eligible recommendations.
- Marked `POC-304` `Complete`; next action is `POC-305` generate the manifest-defined Dataverse records and three reviews for primary proposal `2650001`.

### September 10, 2026 - POC-303 Dataverse schema completed

- Created and live-verified all 53 required business columns across the seven solution tables, including seven local choices and five date/time columns. Existing primary identifiers and nine relationships were also verified and mapped in the schema checklist.
- Confirmed the exact labels for Compliance Status, Stage, Availability, Review Assignment Status, both review ratings, and Compliance Result. Choice values use publisher prefix `51155`.
- Set Keywords, Co-PIs, Co-PI Institutions, Declared Conflicts, and File URL to 4,000-character capacity; live edit dialogs confirmed the latter two had previously retained the 100-character default.
- Implemented Days in Stage as a flow-maintained whole number for the POC because the approved model has no stage-change timestamp from which to calculate it reliably. Production should add an authoritative stage-transition timestamp or history table.
- Added [POC-303 Dataverse Schema Checklist](POC-303-SCHEMA.md), mapping physical names and types to all four flow contracts and the proposal-status topic. Marked `POC-303` `Complete`; next action is `POC-304` build the repeatable synthetic-data generator and expected-results manifest.

### September 10, 2026 - POC-302 Dataverse table shells completed

- Created Proposal, Program, Program Officer, Reviewer, Review Assignment, Review, and Compliance Check as seven standard user/team-owned tables in the `NSF Proposal Intake` solution; live solution inventory reports exactly seven tables.
- Configured primary display columns as Proposal ID, Program Name, Officer ID, Reviewer ID, Assignment ID, Review ID, and Check ID. Dataverse reserves each table's `<logical-name>id` GUID key, so nonconflicting business-column schemas use `nsf_ProposalNumber`, `nsf_ReviewerNumber`, `nsf_AssignmentNumber`, `nsf_ReviewNumber`, and `nsf_CheckNumber` where applicable.
- Configured Assignment ID, Review ID, and Check ID as persisted Autonumber columns with formats `RA-000001`, `REV-000001`, and `CC-000001` (six digits, seed 1).
- Created and live-verified all nine required relationships: Proposal to Program; Proposal to Program Officer for Recommended Officer and Assigned Officer; Program Officer to Program many-to-many; Review Assignment to Proposal and Reviewer; Review to Proposal and Reviewer; and Compliance Check to Proposal.
- Marked `POC-302` `Complete`; next action is `POC-303` add the remaining business columns and choices and map flow inputs, queries, and outputs to their physical schema names.

### September 10, 2026 - POC-301 Dataverse solution completed

- Completed one-time Power Apps maker onboarding for Galen Brown with country/region Canada and optional marketing consent left off.
- Created publisher `NSF` with unique name and customization prefix `nsf`, choice value prefix `51155`, and object-name preview `nsf_Object`.
- Created unmanaged solution `NSF Proposal Intake` with unique name `NSFProposalIntake`, version `1.0.0.0`, and solution ID `9f4ba456-24ad-f111-aaac-70a8a5114222` in environment `test`.
- Re-read the live Solutions grid and verified the display name, unique name, version, and publisher `NSF`.
- Marked `POC-301` `Complete`; next action is `POC-302` create the seven minimal Dataverse tables and required relationships in the solution.

### September 10, 2026 - POC-207 policy grounding tests started

- Added `Docs/Knowledge/poc-207-policy-tests.json` with seven version-controlled cases: three supported questions, two unsupported questions, and two permission-sensitive operational-library requests.
- Defined required answer signals, approved sources, and prohibited behaviors for every case; the JSON manifest parses cleanly and reports all three required categories.
- Ran all seven cases in isolated authenticated Copilot Studio test sessions and recorded actual responses and evidence: all three supported cases and both unsupported cases passed.
- Permission-sensitive cases `POC-207-P01` and `POC-207-P02` disclosed no operational data but failed because the responses implied searches of Proposal Intake and Generated Documents instead of stating that those libraries are excluded from policy knowledge.
- Added and saved a live agent instruction that explicitly identifies both operational libraries, prohibits simulated knowledge searches, and requires an unavailable-through-policy-knowledge response when no authorized tool is present.
- Focused retesting passed `POC-207-P02`. `POC-207-P01` stopped claiming a search and disclosed no data, but fell through the Search answer-not-found path and generic fallback without stating the required boundary.
- Added schema-valid `topics/OperationalLibraryBoundary.mcs.yml` locally using the established deterministic refusal pattern.
- Applied the deterministic topic to the live agent and reran `POC-207-P01` in a clean test session; it returned the required boundary directly and disclosed no operational data.
- All seven manifest cases now pass. Marked `POC-207` `Complete`; next action is `POC-301` create the NSF Proposal Intake solution with publisher prefix `nsf`.

### September 9, 2026 - POC-205 and POC-206 knowledge configuration completed

- Added `pappg-policy`, `solicitations`, and `cluster-sops` to the live agent as synchronized SharePoint knowledge sources using the approved Policy, Solicitations, and Cluster SOPs library URLs.
- Set concise source descriptions matching the approved policy baseline, synthetic-program solicitation mappings, and synthetic cluster procedures.
- Refreshed the live knowledge inventory and verified all three sources report type SharePoint, availability NSF Proposal Intake Assistant, and status Ready.
- Pulled exactly three `KnowledgeSourceConfiguration` definitions into `knowledge/`; each uses `SharePointSearchSource`, points to its intended library URL, and reports no VS Code errors.
- Confirmed the local and live knowledge inventories exclude Proposal Intake and Generated Documents, completing `POC-206`.
- Marked `POC-205` and `POC-206` `Complete`; next action is `POC-207` test supported, unsupported, and permission-sensitive policy questions.

### September 9, 2026 - POC-204 synthetic Cluster SOPs completed

- Added a repeatable generator for the Return-Without-Review Checklist, Reviewer Selection and COI SOP, Panel Summary Guide, Cluster Routing Guide, and Policy Contact Roster.
- Generated and validated all five DOCX files with the required synthetic POC notice and content checks derived from the compliance, routing, reviewer, panel-summary, and policy-escalation design rules.
- Uploaded all five files to the Cluster SOPs library. Microsoft Graph read-back verified nontrivial byte sizes, accessible SharePoint URLs, Owner lookup `11` for Galen Brown, and EffectiveDate `2026-09-09` on every item.
- Marked `POC-204` `Complete`; next action is `POC-205` add Policy, Solicitations, and Cluster SOPs as agent knowledge sources.

### September 9, 2026 - POC-203 source upload completed

- Selected three active public NSF sources while preserving the synthetic proposal identifiers: NSF 25-543 Future CoRe for Secure Distributed Systems, NSF 26-516 GEO Core for Coastal Ecosystem Dynamics, and NSF 25-514 S-STEM for STEM Learning Pathways.
- Preserved the authoritative NSF solicitation pages as HTML and uploaded them to the Solicitations library. Microsoft Graph read-back verified `NSF 25-543 Future CoRe.html` (141,996 bytes), `NSF 26-516 GEO Core.html` (89,066 bytes), and `NSF 25-514 S-STEM.html` (184,988 bytes), with accessible SharePoint URLs.
- Read-back verified `SolicitationNumber`, `Program`, `Directorate`, `DueDate`, and `PageLimits` for all three items. NSF 26-516 is `Proposals Accepted Anytime`; required `DueDate` value `2099-12-31` is documented as an administrative sentinel and not an NSF deadline.
- Added each real-source mapping to the expected-results manifest and design without replacing `NSF POC-SDS-26`, `NSF POC-CED-26`, or `NSF POC-SLP-26`. The six-proposal manifest validator passed unchanged.
- Marked `POC-203` `Complete`; next action is `POC-204` author and upload the five synthetic cluster documents.

### September 9, 2026 - POC-203 source upload started

- Verified the approved policy baseline against authoritative NSF pages: PAPPG NSF 24-1 (effective May 20, 2024), Supplement 1 NSF 26-200 (effective December 8, 2025), and Important Notice 149 research-security provisions (effective December 2, 2025).
- Uploaded the official NSF 24-1 PDF, authoritative NSF 26-200 HTML document, and official Important Notice 149 PDF to the Policy library.
- Microsoft Graph read-back verified all three filenames and byte sizes plus their `DocumentType` and `EffectiveDate` values; PAPPG NSF 24-1 also records `NSF 23-1` in `Supersedes`.
- Kept `POC-203` `In progress`; final selection, upload, and metadata verification remain for three public solicitations matching the synthetic computing, environmental, and education programs.

### September 9, 2026 - POC-202 SharePoint metadata completed

- Created the 18 missing metadata columns across Policy, Solicitations, Cluster SOPs, Templates, Proposal Intake, and Generated Documents; retained the validated `Policy.DocumentType` probe column.
- Re-read all six live library schemas through Microsoft Graph and passed all 19 checks for internal/display names, types, requiredness, date formats, multiline settings, person/group settings, and exact choice values.
- Marked `POC-202` `Complete`; next action is `POC-203` upload and record the approved policy and solicitation source documents.

### September 9, 2026 - POC-202 metadata verification contract defined

- Defined exact column names, SharePoint types, requiredness, and choice values for all six libraries in the design and tracker.
- Standardized flow output on the existing Generated Documents library: notices use `Notices`, and panel summaries use `Panel Summaries`.
- Kept `POC-202` `In progress`; completion requires verifying all listed columns in each library's settings and correcting any differences.

### September 9, 2026 - POC-201 SharePoint site ownership completed

- Confirmed the approved demo site is [NSF Cluster Operations - POC](https://caldova51155962.sharepoint.com/sites/NSFClusterOperations-POC).
- Reviewed SharePoint Admin Center membership evidence showing site owners Galen Brown (`galenb@caldova51155962.onmicrosoft.com`) and Microsoft Administrator (`admin@caldova51155962.onmicrosoft.com`).
- Marked `POC-201` `Complete`; next action is `POC-202` verify the required metadata columns in all six SharePoint libraries.

### September 9, 2026 - POC-107 broad Dataverse MCP action review completed

- Reviewed live Copilot Studio Tools-page evidence showing `Create your first tool`, confirming no Dataverse MCP action or other tool is attached or enabled.
- Accepted removal/absence as the disposition; there are no attached tool permissions to grant, and future Dataverse access remains limited to the purpose-built flows and proposal-status query defined in the design.
- Marked `POC-107` `Complete`, completing Phase 1; next action is `POC-201` verify and record the approved SharePoint site owner.

### September 9, 2026 - POC-107 broad Dataverse MCP action review started

- Selected removal/absence rather than retention because the POC has no current use that justifies a broad Dataverse MCP action; future Dataverse access is limited to the four purpose-built agent flows and the proposal-status query defined in the design.
- Confirmed the synchronized local definition contains only 13 dialog components and one GPT component, `gptCapabilities` is empty, `workflows/` is empty, and no action or connection-reference artifact is present.
- Set `POC-107` to `In progress`; completion requires confirming in the live agent Tools page that no broad Dataverse MCP action is attached or enabled.

### September 9, 2026 - POC-106 merit refusal completed

- Confirmed all five live adversarial prompts returned the same deterministic refusal and administrative redirect.
- Verified the tested prompts covered intellectual-merit scoring, proposal ranking, scientific-strength comparison, broader-impacts quality comparison, and a funding recommendation.
- Marked `POC-106` `Complete`; next action is `POC-107` review the broad Dataverse MCP action and its POC data scope.

### September 9, 2026 - POC-106 merit refusal topic added locally

- Added `topics/MeritReviewRefusal.mcs.yml` with five adversarial triggers covering intellectual-merit scoring, proposal ranking, scientific-strength comparison, broader-impacts quality comparison, and funding recommendations.
- Added a deterministic refusal that assigns merit review to program officers and review panels and redirects to the four supported administrative capabilities.
- Confirmed the VS Code Problems check reports no errors in the new topic.
- Set `POC-106` to `In progress`; completion requires applying the topic and confirming all five prompts receive the refusal and administrative redirect.

### September 9, 2026 - POC-105 capability greeting completed

- Reviewed Copilot Studio test evidence from a new conversation displaying the approved capability greeting.
- Confirmed the greeting identifies the assistant, its five supported administrative interactions, and the draft-for-approval boundary.
- Marked `POC-105` `Complete`; next action is `POC-106` implement the deterministic merit-review refusal topic.

### September 9, 2026 - POC-105 capability greeting added locally

- Replaced the generic Conversation Start text and speech messages with the approved capability summary from design section 11.1.
- The greeting names compliance checks, program routing, conflict-screened reviewer suggestions, panel-summary skeletons, policy questions, and the draft-for-approval boundary.
- Confirmed the VS Code Problems check reports no errors in `topics/ConversationStart.mcs.yml`.
- Set `POC-105` to `In progress`; completion requires applying the local change and confirming a new conversation displays the approved greeting.

### September 9, 2026 - POC-104 agent settings completed

- Disabled general model knowledge and file analysis in `settings.mcs.yml`.
- Set content moderation to `High` and retained generative orchestration through `GenerativeActionsEnabled: true` and `GenerativeAIRecognizer`.
- Confirmed the VS Code Problems check reports no errors in `settings.mcs.yml`.
- Marked `POC-104` `Complete`; next action is `POC-105` replace the default greeting with the approved capability summary.

### September 9, 2026 - POC-103 starter prompts completed

- Reviewed published-agent evidence showing all six approved starter prompts on the agent welcome page.
- Confirmed each starter submitted its configured prompt and received a response without errors.
- Marked `POC-103` `Complete`; next action is `POC-104` configure and verify the agent settings.

### September 9, 2026 - POC-103 starter prompts added locally

- Added all six approved section 7.3 prompts to `agent.mcs.yml` as schema-valid conversation starters with concise titles.
- Confirmed the VS Code Problems check reports no errors in `agent.mcs.yml`.
- Set `POC-103` to `In progress`; completion requires applying the local change and invoking each starter from the live agent experience.
- Next action remains `POC-103`: review and commit the local changes, run Copilot Studio Preview changes and Apply changes, then verify all six starters appear and invoke the intended capability.

### September 9, 2026 - POC-102 agent instructions completed

- Added the complete approved section 7.2 instructions to `agent.mcs.yml`.
- Covered the four administrative capabilities, tool-only execution, citation and attribution requirements, draft-only communications, policy grounding, prompt-injection handling, professional response style, and the no-merit boundary.
- Confirmed the VS Code Problems check reports no errors in `agent.mcs.yml`.
- Marked `POC-102` `Complete`; next action is `POC-103` add the approved starter prompts.

### September 9, 2026 - POC-101 agent identity completed

- Reviewed Copilot Studio Agent details evidence showing the live name `NSF Proposal Intake Assistant`.
- Confirmed the live description matches the approved POC text covering PAPPG compliance, program routing, conflict-screened reviewers, panel-summary skeletons, draft-only output, and the no-merit boundary.
- Marked `POC-101` `Complete`; next action is `POC-102` add the approved agent instructions.

### September 9, 2026 - POC-101 agent identity updated locally

- Changed the root agent display name and GPT component name to `NSF Proposal Intake Assistant`.
- Added the approved POC description covering compliance, routing, conflict-screened reviewers, panel-summary skeletons, draft-only output, and the no-merit boundary.
- Confirmed the VS Code Problems check reports no errors in `settings.mcs.yml` or `agent.mcs.yml`.
- Set `POC-101` to `In progress`; run Copilot Studio Preview changes and Apply changes, then verify Agent details before marking it complete.

### September 9, 2026 - POC-005 environment verification completed

- Confirmed environment `test` can draw from 540,000 available tenant Copilot Credits; the environment had consumed and directly allocated zero credits at verification time.
- Confirmed Word Online (Business) is connected in environment `test` and exposes Populate a Microsoft Word template with the approved NSF Cluster Operations - POC site and Templates library selectable.
- Accepted the previously recorded Copilot Studio, licensing, Dataverse, SharePoint, Outlook, and Teams evidence and marked `POC-005` `Complete`.
- Cleared the active blocker and advanced the next action to `POC-101` rename and describe the agent as NSF Proposal Intake Assistant.

### September 9, 2026 - POC-005 SharePoint access unblocked

- Authenticated as `galenb@caldova51155962.onmicrosoft.com` and opened the approved [NSF Cluster Operations - POC](https://caldova51155962.sharepoint.com/sites/NSFClusterOperations-POC) site.
- Enumerated Policy, Solicitations, Cluster SOPs, Templates, Proposal Intake, and Generated Documents as six separate SharePoint document libraries.
- Verified Galen can enumerate the root of all six libraries.
- Created, read through item metadata, and deleted a synthetic test file in Templates, Proposal Intake, and Generated Documents; no test artifacts remain.
- Removed the SharePoint blocker from `POC-005`; environment AI capacity remains the only active blocker.

### September 9, 2026 - POC-005 Dataverse access unblocked

- Confirmed Galen Brown is enabled in environment `test` with directly assigned Dataverse roles Basic User, System Customizer, and System Administrator.
- Reauthenticated as `galenb@caldova51155962.onmicrosoft.com` and reran the previously failing Dataverse checks against `https://org623e75ac.crm3.dynamics.com/`.
- Dataverse `WhoAmI` succeeded, assigned-role enumeration returned all three roles, and reading solution metadata succeeded with five rows; the prior `prvReadSolution` blocker is resolved.
- Kept `POC-005` `Blocked` only for the missing or inaccessible SharePoint containers and unverified environment AI capacity.

### September 9, 2026 - POC-005 environment verification blocked

- Authenticated as enabled member `galenb@caldova51155962.onmicrosoft.com` in the Caldova tenant and confirmed membership in Global Administrator and NSF Cluster Operations - POC.
- Confirmed assigned Microsoft 365 E7, Copilot Studio, Power Apps per-user, Power Automate, Teams Premium, and Dynamics 365 Finance Premium licenses with enabled Copilot, Dataverse/AI capacity, Exchange, SharePoint, Word web app, and Teams service plans.
- Confirmed the local Copilot Studio workspace is bound as Galen to Developer environment `test` (`b3804354-f1fd-e3a7-9bf3-065df4d032f2`) in Canada and Dataverse endpoint `https://org623e75ac.crm3.dynamics.com/`; Power Platform administrator and maker APIs both return the environment.
- Dataverse `WhoAmI` succeeded, but reading solution metadata failed because Galen's Dataverse user has no security roles and lacks `prvReadSolution`; a role sufficient to create and manage POC solution components is required.
- Opened the approved SharePoint site and confirmed Teams membership, but the account can enumerate only the default Documents library. Its Proposal Intake Demo folder is empty, so the six expected POC containers are absent or inaccessible and write access could not be verified.
- Created and deleted an Outlook draft through delegated Graph access; the message was never sent.
- Confirmed AI capacity-related license entitlements, but the environment capacity query returned no records and no Power Platform connector connections exist in environment `test`; AI/Copilot capacity allocation and Word Online connector access remain unverified.
- Set `POC-005` to `Blocked`. Owner: Galen Brown / POC environment administrator. Next action: assign the Dataverse role, restore or authorize the SharePoint containers, and verify AI capacity in the Power Platform admin center.

### September 9, 2026 - POC-004 repository README completed

- Created the root [README](../../README.md) with the synthetic-data-only boundary, prerequisites, setup, proposal validation, Copilot Studio synchronization discipline, and portal publishing steps.
- Verified all README repository links resolve and `git diff --check` reports no whitespace errors.
- Ran `validate-expected-results.ps1` successfully: six proposals, three officers, eight reviewers, five distinct exclusions, and three eligible recommendations.
- Marked `POC-004` `Complete`; next action is `POC-005` verify the target demo environment and licensed persona dependencies.

### September 9, 2026 - POC-003 baseline repository completed

- Initialized the `Proposal-Intake` workspace as a Git repository on branch `main`.
- Created baseline commit `fe754045cd953a4ca773234a66239637a9337758`.
- Verified `git status` reported a clean working tree after the baseline commit.
- Marked `POC-003` `Complete`; next action is `POC-004` create the repository README.

### September 8, 2026 - POC-002 topic cleanup completed

- Confirmed the generic Greeting deletion was applied to the tested Copilot Studio agent.
- Reviewed tracked Activity evidence for compliance, reviewer suggestion, routing, panel-summary, and greeting-prefixed compliance requests for proposal `2650001`.
- All five requests routed to Search sources; none invoked Greeting or another unrelated system topic, and the greeting-prefixed request remained intact.
- Confirmed Conversation Start appeared once at startup and retained Sign in as the event-scoped Microsoft Entra authentication handler.
- Observed that Search sources used general LLM knowledge; this is not a POC-002 failure and remains an explicit POC-104 settings issue.
- Marked `POC-002` `Complete`; next action is `POC-003` initialize the Git repository and create the baseline commit.

### September 8, 2026 - POC-002 topic cleanup started

- Reviewed all 13 local system topics and identified the generic Greeting topic as the concrete interruption risk because it matched greeting phrases and called `CancelAllDialogs`.
- Removed `topics/Greeting.mcs.yml` locally and verified that the remaining 12 topic files contain no generic `Hi`, `Hello`, `Hey`, `Good morning`, or `Good afternoon` trigger query.
- Retained Conversation Start for the POC-105 capability greeting, Sign in for Microsoft Entra authentication, and Conversational boosting for the later approved-knowledge configuration.
- Verified installed extension `ms-copilotstudio.vscode-copilotstudio` version `1.7.20`; use `Copilot Studio: Preview changes` followed by `Copilot Studio: Apply changes` to apply the local deletion.
- Set `POC-002` to `In progress`; applying the deletion to Copilot Studio and running tracked trigger tests remain before completion.
- Next action: preview and apply local changes in Copilot Studio, then test direct and greeting-prefixed proposal requests with topic tracking enabled.

### September 8, 2026 - POC-001 baseline completed

- Reviewed and approved [expected-results.json](../Proposals/expected-results.json) under the licensed Galen Brown persona; the manifest records the PAPPG 24-1 baseline, six proposal outcomes, three programs and officers, eight reviewers, conflict evidence, and deterministic routing and reviewer results.
- Resolved the multi-program officer requirement with a Program-ProgramOfficer many-to-many relationship in the design.
- Regenerated and structurally validated all six DOCX proposals with `generate-proposals.ps1`.
- Passed `validate-expected-results.ps1`: six proposals, three officers, eight reviewers, five distinct exclusions, and three eligible recommendations.
- Recorded manifest SHA-256 `43BEA3790463783102DFF33DE80D6AA4B69614CBB8B8C450E953373E0F79D432` and marked `POC-001` `Complete`.
- Next action: `POC-002` decide whether to retain or remove the experimental identity capture and presentation topics.

### September 8, 2026 - Synthetic officers and reviewers defined

- Defined three synthetic ProgramOfficer records with IDs, non-routable email addresses, program coverage, expertise, workload, new-hire status, and expected routing.
- Defined eight synthetic Reviewer records with all required profile and relationship fields for primary proposal `2650001`.
- Assigned one distinct conflict to each of `RV-001` through `RV-005` and designated `RV-006`, `RV-008`, and `RV-007` as the three eligible recommendations in expected rank order.
- Recorded the proposal and historical-assignment cross-records required to test the PI-declared and recent-panel conflicts.
- Kept `POC-001` `In progress`; the complete reviewed expected-results manifest and remaining proposal-level routing evidence are still required.
- Next action: `POC-001` complete and review the remaining scenario manifest details.

### September 8, 2026 - SharePoint libraries confirmed

- Confirmed that all six libraries exist in the demo SharePoint site: Policy, Solicitations, Cluster SOPs, Templates, Proposal Intake, and Generated Documents.
- Set `POC-202` to `In progress`; required POC metadata columns still must be verified before the completion gate passes.
- Next action remains: `POC-001` complete and review the remaining scenario manifest details.

### September 8, 2026 - Demo SharePoint site selected

- Selected [NSF Cluster Operations - POC](https://caldova51155962.sharepoint.com/sites/NSFClusterOperations-POC) as the SharePoint site for the demo.
- Set `POC-201` to `In progress`; the site owner still must be verified and recorded before the completion gate passes.
- The six POC libraries remain to be created or verified under `POC-202`.
- Next action remains: `POC-001` complete and review the remaining scenario manifest details.

### September 8, 2026 - Licensed program-officer persona selected

- Selected Galen Brown (`galenb@caldova51155962.onmicrosoft.com`) as the POC's one licensed program-officer persona.
- Tenant licensing shown for the account includes Microsoft 365 E7, Microsoft Copilot Studio User License, Power Apps Premium, Power Automate Premium, Microsoft Teams Premium, Dynamics 365 Finance Premium, and Microsoft Power Automate Free.
- Kept `POC-005` `Not started`; license assignment does not yet verify access to the target Power Platform environment, Dataverse tables, AI Builder capacity, SharePoint libraries, Outlook mailbox, Teams channel, or published agent.
- Next action remains: `POC-001` complete and review the remaining scenario manifest details.

### September 8, 2026 - Primary demo proposal designated

- Designated proposal `2650001`, Resilient Federated Control for Community Microgrids, as the primary demo proposal.
- Kept its compliance scenario clean so the proposal can demonstrate routing, reviewer screening, invitation drafts, and panel-summary generation end to end.
- Kept `POC-001` `In progress`; officer, reviewer, conflict, and expected-routing manifest details remain outstanding.
- Next action: `POC-001` complete and review the remaining scenario manifest details.

### September 8, 2026 - Synthetic proposal documents generated

- Generated and structurally validated six synthetic DOCX proposals under `Docs/Proposals` using the repeatable `generate-proposals.ps1` script.
- Verified proposals `2650001` through `2650003` include all required sections; `2650004` omits only the Data Management Plan; `2650005` contains 16 explicit Project Description pages; `2650006` omits only Current and Pending Support.
- Opened all six files read-only in Microsoft Word without repair prompts; Word reported total page counts of 6, 6, 6, 6, 19, and 6, respectively.
- Dropped PDF generation and aligned the POC intake and extraction specification to DOCX.
- Kept `POC-001` `In progress`; officer, reviewer, conflict, and expected-routing manifest details remain outstanding.
- Next action: `POC-001` complete and review the remaining scenario manifest details.

### September 8, 2026 - POC data set right-sized

- Kept three programs and six proposals; reduced the POC to three program officers and eight reviewers.
- Set the reviewer result to three eligible recommendations after five distinct planted COI exclusions.
- Marked `POC-001` `In progress`; the scenario manifest and remaining synthetic scenario details are still required for completion.
- Next action: `POC-001` complete and review the six-proposal scenario manifest.

### September 4, 2026 - Tracker initialized

- Created the POC build tracker from `DESIGN.md` version 2.1.
- All implementation steps intentionally remain `Not started`.
- No completion evidence has been accepted.
- Next action: `POC-001` confirm the six-proposal scenario manifest.

## Update Procedure

At the end of every implementation session:

1. Update the Status value for every step touched during the session.
2. Mark a step `Complete` only after its completion gate passes.
3. Add evidence or a concise result to Session Notes.
4. Record blockers with the affected step ID, owner, and required resolution.
5. Update Current phase, Overall status, Next step, and Last completed step.
6. Leave exactly one primary Next step so the following session can resume without reconstructing context.
