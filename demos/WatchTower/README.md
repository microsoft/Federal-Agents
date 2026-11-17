# WATCHTOWER

## Overview

**WATCHTOWER** is an executive cybersecurity intelligence and decision-support
agent built with Microsoft Copilot Studio. It turns public NIST National
Vulnerability Database (NVD) records and the CISA Known Exploited Vulnerabilities
(KEV) catalog into evidence-supported briefings for CIOs, CISOs, mission owners
and program leaders.

This is the complete customer README: downloads, integrity hashes, tenant
deployment, custom connectors, tools, evidence retention, prompts, acceptance,
publication and operations are documented together below. WATCHTOWER is an
independent agent with its own destination connections and private evidence
cache; deploying another agent is not a prerequisite.

Users can research a specific CVE, check whether CISA lists it as exploited,
review recent KEV additions, request catalog-wide priorities, and ask for
leadership recommendations aligned to the NIST Cybersecurity Framework.
Responses distinguish published facts, conditional mission impact, evidence
gaps and proposed human actions.

WATCHTOWER supports human judgment. It does not inspect the customer's assets,
establish exposure or compromise, perform remediation, or make operational
decisions. A KEV listing is not proof that your organization is affected; a
missing listing is not proof of safety.

## Customer start here: ZIP import, no scripts

**Version 1.0.2.4 is an import-and-configure solution.** Download two solution
ZIPs, import them through Power Apps, create the destination CISA and NVD
connections and a designated SharePoint connection, configure an isolated
WatchTower evidence list, and verify the agent in Copilot Studio.

**Qualification:** this is a deployment-validation candidate, not production
sign-off. The destination owner must complete evaluation, identity, channel and
answer-quality acceptance before approving publication.

**Targeted sandbox result:** consecutive version/fix follow-ups now select a
scoped read-only evidence tool and distinguish affected boundaries from
verified fixes without refreshing sources. This release includes a dedicated
version reader, current-question context kept separate from source evidence,
and compact provenance outputs. These checks do not qualify a fresh customer
installation, other identities or a published channel. A fallback, fabricated
fix, altered source link or silent refresh still fails acceptance.

**Response wording check:** CISA's `requiredAction` is a published source field,
not proof of a customer's obligations. Version 1.0.2.4
adds a narrow pre-send check for selected English obligation phrases. A match
withholds the generated answer and sends one explicit validation notice,
without generating a replacement report. This does not certify every claim or
establish customer applicability; complete destination acceptance below.

Customers do not need Git, Python, PowerShell, Power Platform CLI, a workstation
service or an on-premises gateway. The connectors, agent and supporting flow
run in Power Platform. SharePoint retains source evidence for no-refresh
follow-ups; it does not provide an asset inventory or a vector database.
No validation-package list, approval workflow or additional Azure resource
is part of this solution. Retention ships disabled until the customer
configures its private destination; never use another agent's cache.

The packages are **unmanaged**. Start in an approved isolated environment and
follow your organization's application lifecycle management process for
production. Import does **not** publish the agent. Complete your destination
configuration and acceptance checks before the owner explicitly publishes.

| Start here | What you will find |
|---|---|
| [Downloads and SHA256](#downloads-and-sha256) | Both ZIPs, import order, integrity hashes and upgrade notes |
| [Architecture and data boundaries](#architecture-and-data-boundaries) | Source retrieval, authentication and private-cache boundaries |
| [Prepare the destination](#prepare-the-destination) | Required environment, owners, permissions and cloud portals |
| [Deployment steps](#deploy-to-your-tenant-step-by-step) | Browser-only setup, connections, import and verification |
| [Custom connector reference](#custom-connector-reference) | Endpoints, operations, authentication and response fields |
| [Tool and flow reference](#native-tools-and-flow-contract) | Seven native flow tools, the internal NVD capture adapter and two flows |
| [Evidence retention](#isolated-evidence-retention) | Identity isolation, expiry, failure states and no-refresh behavior |
| [Verification prompts](#verification-prompts) | Customer checks and expected behavior |
| [Copy-and-paste walkthrough](#copy-and-paste-walkthrough) | Ordered prompts for source retrieval, retained follow-ups and the wording check |
| [Publish and share](#publish-and-share) | Authentication, audience and channel rollout |
| [Troubleshooting](#troubleshooting) | Import, connection, routing and response issues |
| [Operations, upgrades and recovery](#operations-upgrades-and-recovery) | Ownership, monitoring, credential rotation and recovery |
| [Handoff checklist](#handoff-checklist) | Destination acceptance and operational ownership |
| [Source reference](#source-reference) | Exported solution, component definitions and focused regression tests |

## Downloads and SHA256

Download both **1.0.2.4 unmanaged** packages. Keep the ZIPs compressed and
import them through Power Apps **Solutions > Import solution** in this order:

| Order | Download | Purpose |
|---|---|---|
| 1 | [WatchTowerConnectors_1_0_2_4_unmanaged.zip](install/WatchTowerConnectors_1_0_2_4_unmanaged.zip) | Register the CISA KEV and NVD custom connectors before creating their destination connections |
| 2 | [WatchTower_1_0_2_4_unmanaged.zip](install/WatchTower_1_0_2_4_unmanaged.zip) | Import the agent, tools, both flows, connection references and environment variables after the connections and private cache are ready |

On each GitHub file page choose **Download raw file** or the download icon.
Use your approved file-verification tool to compare the downloaded bytes
with these SHA256 values:

| File | SHA256 |
|---|---|
| `WatchTowerConnectors_1_0_2_4_unmanaged.zip` | `aecafa116691d574d122c320fd59e1e6220caaa61d76a0fb476d6f6ae5d4f343` |
| `WatchTower_1_0_2_4_unmanaged.zip` | `9c7496a70254a5be93ddcc92f2766c21cba9ca9cc8353aeefe75e5036d6808ea` |

Follow the [complete deployment sequence](#deploy-to-your-tenant-step-by-step)
in this README, including the private SharePoint list and three customer
environment-variable values. Retention defaults to **disabled**; neither
package supplies a source tenant's private site/list binding. Import does
not publish the agent or complete customer acceptance.

**Existing installations:** an installation already using the typed NVD
connector can reimport the complete solution with its existing CISA/NVD
connections, the designated SharePoint reference and customer configuration.
Back up first and verify destination bindings. Do not create another copy of
the agent or replace users' Invoker credentials with a shared key. The CISA
flow retains its configured embedded public-feed connection.

**Earlier packages:** older ZIPs remain available for recovery/reference.
Version 1.0.2.1 does not include persistent retention; 1.0.2.2 lacks the
dedicated version reader and compact provenance outputs; 1.0.2.3 does not
include the new obligation wording check. Do not mix package versions or
import a raw tenant export containing another installation's bindings.

## Capabilities and boundaries

| Capability | What WATCHTOWER does | Boundary |
|---|---|---|
| Specific-CVE research | Retrieves an exact NVD record and independently checks the full KEV catalog | Missing fields remain unknown; a source failure is not a negative finding |
| Recent additions | Reports KEV additions within the returned UTC calendar-date window | CISA supplies dates, not precise rolling 24-hour timestamps |
| Leadership priorities | Selects up to five full-catalog candidates using a defined published-signal order | Not a tenant-specific risk ranking or a comprehensive intelligence feed |
| Morning briefing | Separates three catalog priorities from recent additions | Does not establish customer exposure, incidents or operational telemetry |
| Top-three briefing | Explains three catalog-prioritized threats and conditional mission impact | Uses returned source associations, not invented product or severity labels |
| Follow-up analysis | Reads captured NVD/CISA evidence for the same authenticated user and conversation | Requires configured retention; no silent refresh or use of another user's evidence |

The agent writes **one tailored answer** from native tool evidence, rather than
displaying a canned report followed by a second generated report. Full
assessments cover the subject, verified facts, risk basis, affected technology,
conditional impact, limitations and proposed leadership decisions. Short
follow-ups can be concise; one-page requests have a 400-word instruction budget.

Evidence grounding is instruction-guided, not a deterministic factuality
guarantee. Review the actual tool results before using generated statements.
Substantive assessments end with:

> All recommendations require human review and approval before operational action.

### Pre-send obligation wording check

The **Check organizational obligation claims** event topic examines
`System.Response.FormattedText` when an orchestration response is about to be
sent. It recognizes selected positive English phrases such as "requires
organizations to", "your organization must patch" and "is mandatory for your
agency", including common Markdown emphasis.

An unmatched answer passes unchanged. A match sets `System.ContinueResponse`
to false and sends one short notice that the answer was withheld. The topic
does not call an API, retrieve evidence, ask a second model to rewrite text or
substitute a fixed briefing template. System sign-in/error topics remain.

Quoted `requiredAction:` / `Required action:` fields are excluded from this
wording scan so a source field can be reported literally. That exclusion
does not authenticate a quotation: compare it with actual tool evidence.
Conditional "may/should/consider" recommendations, "does not require" and
"must not assume" are not positive obligation claims in this check.

This is a **conservative wording guard, not semantic or legal validation**.
Other languages, paraphrases and unsupported claims outside its selected
patterns are not comprehensively detected. A quoted example or a more complex
negation containing a matched phrase can still be withheld. It does not check
static messages or prove that a customer's policy creates an obligation.
Keep authorized human applicability review and evaluate the intended channel.

## Architecture and data boundaries

```text
Authenticated user -> Copilot Studio WATCHTOWER
                       |
                       +-> NVD CVE Evidence internal data-only adapter
                       |      -> User's Invoker NVD connection
                       |      -> services.nvd.nist.gov
                       |      -> Bound actual response -> retention flow
                       |
                       +-> Five CISA native flow tools
                       |      -> Get Current Federal Threat Brief cloud flow
                       |      -> Embedded CISA connection -> www.cisa.gov
                       |      -> Source-written evidence -> private cache
                       |
                       +-> Retained Evidence / Version and Fix Evidence tools
                       |      -> Retention flow -> private SharePoint cache
                       |      -> Same user + conversation + CVE + source
                       |      -> No NVD/CISA request
                       |
                       +-> One evidence-supported, tailored response
                              -> Human review and approval
```

The solution uses the standard Copilot Studio harness with generative
orchestration. Seven capabilities are native flow tools: five CISA tools and
two scoped read-only evidence tools. NVD uses one internal procedural capture
adapter to bind the real connector output before retention. It returns data,
not a scripted answer; the orchestrator writes one natural response.
Standard topics remain for authentication, errors, fallback and conversation
control; those event handlers are not research tools.
General model knowledge is disabled: source facts must come from the connected
tools, not an unsourced expansion of the model's background knowledge.
Published CISA actions/dates do not determine customer legal applicability,
enforceability, compliance or whether obligations have changed.

**Acceptance requirement:** a no-refresh follow-up must select the retained
reader, preserve original dates, and make no NVD/CISA call. A no-information
fallback, silent refresh or invented finding is a failed check. Missing or
disabled retention must be reported explicitly. Do not enable general
knowledge to hide an evidence gap.

NVD receives the requested CVE identifier. CISA receives a public catalog
download request; its flow performs the exact-CVE matching and prioritization.
These sources do not receive a customer asset inventory. Conversations, tool
traces and flow run histories can retain requests and returned evidence under
the destination platform's policies. Review access, retention and classification.

NVD uses **Invoker** credentials: users authorize their own connection through
the supported connection manager. The CISA flow uses its configured embedded
no-auth connection. Cache access uses a separate designated SharePoint
connection, not the user's NVD credentials. Sharing the agent alone does not grant every required
connector permission or authorize the user's NVD connection.

The isolated cache stores public evidence together with user/conversation
identifiers. Treat those identifiers and run histories under the customer's
privacy and retention policies. Ordinary agent users must not have direct
access to the list; filtering is not a substitute for SharePoint permissions.

## Prepare the destination

Arrange the following before installation:

- A Dataverse-backed Power Platform environment with Copilot Studio available.
- An administrator or maker authorized to import solutions, manage custom
  connectors and connections, configure flows and edit the agent.
- An approved primary owner and backup owner for the agent, flow and connections.
- An activated NVD API key entered only in the secure connection interface.
- A private SharePoint site/list and designated connection owner for the
  WatchTower-only evidence cache, separate from other agents and business lists.
- Approved DLP classifications and outbound HTTPS access to
  `services.nvd.nist.gov`, `www.cisa.gov` and the customer's SharePoint host.
  The SharePoint connector must be permitted in the same flow combination.
- An intended audience, authentication policy and approved publication channel.
- A maker test account, an ordinary authorized account and an unauthorized
  account for access checks.

Confirm model, connector, authentication and channel availability in the
destination cloud. This guide is not certification of every cloud or region.
Use your organization's approved portals rather than modifying endpoints to
force unsupported services.

| Cloud | Power Apps | Copilot Studio | Power Automate |
|---|---|---|---|
| Commercial | `https://make.powerapps.com` | `https://copilotstudio.microsoft.com` | `https://make.powerautomate.com` |
| GCC | `https://make.gov.powerapps.us` | `https://gcc.powerva.microsoft.us` | `https://make.gov.powerautomate.us` |

Record the destination environment name/ID/URL, owners, package version, selected
connections, audience and acceptance evidence. Keep credentials out of that
record, source control, chat and screenshots.

## Get WATCHTOWER from GitHub

1. Open the private [Mission-Ready-Agents repository](https://github.com/alexalundie_microsoft/Mission-Ready-Agents)
   and select branch **WatchTower**. The repository owner must grant approved
   recipients access.
2. Read this complete README, shown at the root of the **WatchTower** branch
   and at **WatchTower/README.md** on **main**. Use the two ZIP links under
   [Downloads and SHA256](#downloads-and-sha256) above; no separate installation
   README is required.
3. Keep both ZIPs compressed. **Code > Download ZIP** downloads repository source,
   not an importable Power Platform solution.
4. Compare the downloaded files with the SHA256 values in this README using
   your approved file-verification tool.
5. Follow the runbook below. Use the distributed packages, not an arbitrary
   tenant export containing someone else's current connection bindings.

## Deploy to your tenant: step by step

### 1. Select and prepare the environment

1. Open Power Apps in the intended tenant and choose the approved isolated
   environment. Confirm its name and environment ID.
2. Open Copilot Studio and Power Automate; select that **same environment** in
   each portal.
3. Confirm the installation account can import solutions and configure custom
   connectors, connections, flows and agents.
4. Have the administrator approve both API hosts and the required connector
   combination under DLP. Do not disable DLP or weaken tenant-wide policies.
5. If updating an existing installation, export its solution and record its
   connection configuration before import. Use approved environment backups.
   An unmanaged import can overwrite components; deleting the solution
   container is not a rollback.
6. Keep the agent limited to the installation team until acceptance is complete.

### 2. Obtain and activate the NVD API key

1. Open the [NVD API key request page](https://nvd.nist.gov/developers/request-an-api-key).
2. Request a key using your approved organizational process and complete NVD's
   activation step.
3. Store the key in your approved secret-management system and assign a
   credential owner and backup.
4. Enter it only when creating the NVD connection. Never place it in an agent
   prompt, flow variable, connector definition, URL or checked-in settings file.

NVD can return an authentication error for an unactivated or invalid key.
That error does not mean a requested CVE is absent.

### 3. Import the connectors first

1. In Power Apps select **Solutions > Import solution**.
2. Upload **WatchTowerConnectors_1_0_2_4_unmanaged.zip**.
3. Review the identity and version: **WatchTowerConnectors**, **1.0.2.4**,
   unmanaged, publisher **CDS Default Publisher** (`Cr4a132`).
4. Complete import and wait for success. If it fails, download and inspect the
   import log before proceeding.
5. Open the imported solution and confirm both custom connectors:
   **CISA KEV** and **NVD-CVE-Connector**.
6. Allow connector registration to complete before creating connections or
   importing the full agent.

A fresh environment needs connector registration before their dependent
connection references and flow. Do not recreate equivalent connectors under
different identities and then mix them with these packages.

### 4. Create and test the CISA connection

1. Open **CISA KEV** in the custom connector designer. Confirm **No
   authentication**, host `www.cisa.gov`, operation `GetKEV`.
2. Open **Test > New connection** and create the destination connection.
   A connection is required even though the public feed has no credential.
3. Select that connection and test **GetKEV**.
4. Confirm HTTP 200 and a JSON object with `catalogVersion`, `dateReleased`,
   `count` and a populated `vulnerabilities` array. HTML, a blocked request or
   missing catalog fields is not a successful source result.
5. Record the connection's display name so it can be selected during full import.

### 5. Create and test the NVD connection

1. Open **NVD-CVE-Connector**. Confirm HTTPS, host
   `services.nvd.nist.gov`, base path `/rest/json`, operation **GetSpecificCVE**.
2. Confirm **API key** authentication, parameter name **apiKey**, location
   **Header**. Do not place the key in a query parameter.
3. Open **Test > New connection** and enter the activated key in the secure
   credential field.
4. Select the connection and test **GetSpecificCVE** with `cveId` set to
   **CVE-2023-23397**.
5. Confirm HTTP 200, `totalResults` equal to 1, exactly one vulnerability and
   `vulnerabilities[0].cve.id` equal to the requested CVE.
6. Inspect descriptions, status, available metrics and references. A score or
   version range that the source does not return must remain unknown.
7. Record the connection's display name. Resolve key, network or throttling
   errors before proceeding; do not retry in a tight loop.

Connector designer labels may vary by rollout. Use the corresponding
**Connections** or **Custom connectors** page in the destination portal.

### 5a. Prepare the private evidence cache

1. In SharePoint, use an approved private site or create one under your
   organization's normal process. Create a **blank list** named
   **WatchTower Evidence Cache**. Do not reuse AEGIS or another application's
   lists. Keep the standard **Title** and **ID** columns.
2. Create the following columns with these **exact names on first creation**.
   SharePoint internal names do not automatically change after a rename.
   Choose **Single line of text**, not Choice, for text fields.

| Exact column name | SharePoint type | Required | Configuration |
|---|---|---|---|
| `ActorKey` | Single line of text | Yes | Maximum 200; index |
| `ConversationKey` | Single line of text | Yes | Maximum 200; index |
| `CveKey` | Single line of text | Yes | Maximum 40; index |
| `SourceKind` | Single line of text | Yes | Maximum 16; index |
| `EvidenceStatus` | Single line of text | Yes | Maximum 50 |
| `EvidenceScope` | Single line of text | Yes | Maximum 40 |
| `EvidenceJson` | Multiple lines of text | Yes | Plain text; no rich text; no append-only changes |
| `RetrievedUtc` | Date and time | Yes | Include time; SharePoint stores UTC |
| `ExpiresUtc` | Date and time | Yes | Include time; index |

3. In **List settings > Indexed columns**, create the indexes indicated above.
   Confirm the actual internal names in each column's settings URL.
4. Review **Permissions for this list** with the SharePoint administrator.
   Use a private site or stop inheritance on this new list and remove access
   groups that would give ordinary agent users direct list access. Keep the
   designated flow owner and authorized administrators. Do not change another
   application's site/list permissions.
5. Give the designated SharePoint connection account permission to read,
   add and delete items in this list. Creation/column administration is a
   setup responsibility, not a reason to grant all agent users site ownership.
6. In Power Apps **Connections**, create a **SharePoint** connection as that
   designated account. Confirm it can access the new list. Never enter an NVD
   key in this connection.
7. Record the **site URL** (for example
   `https://<customer-host>/sites/WatchTower`, not a list view URL) and
   **list GUID**. The list settings URL exposes the GUID in its `List=`
   parameter; decode URL escapes and omit braces. Do not use the example
   values as a real destination.
8. Leave the list empty. Source-writing flows create the evidence rows.
   Do not seed it with generated JSON or pasted chat answers as verified evidence.

### 6. Import the complete solution

1. Return to **Solutions > Import solution**.
2. Upload **WatchTower_1_0_2_4_unmanaged.zip**.
3. Confirm **WatchTower**, version **1.0.2.4**, unmanaged, same publisher.
4. Map the **CISA KEV** connection reference to the CISA connection from step 4.
5. Map the **NVD Connector** reference to the NVD connection from step 5.
   Do not select another person's connection without authorization.
6. Map **WatchTower Evidence Cache SharePoint** to the designated connection
   from step 5a. Set the following **current values** in the import wizard.
   If the wizard does not show them, open the solution's environment variables
   after import and set them there before testing:

| Environment variable schema name | Customer current value | Distributed default |
|---|---|---|
| `cr8ac_WatchTowerRetentionEnabled` | `true` | `false` |
| `cr8ac_WatchTowerCacheSiteUrl` | Approved private site's HTTPS URL | Blank |
| `cr8ac_WatchTowerCacheListId` | New list's GUID | Blank |

7. Complete import and wait for success. Resolve dependency errors from the
   import log; do not accept partial installation.
8. Open the imported solution. Confirm WATCHTOWER, both custom connectors,
   three connection references, the three environment variables,
   **Get Current Federal Threat Brief** and **WatchTower Evidence Retention**.
9. Confirm each connection reference resolves to the intended destination
   connection. Credentials and source-user connection instances do not
   transfer with the package.

The full ZIP contains the connectors as well as their consumers; the
connectors-only import establishes their registration first.

### 7. Check and enable both flows

1. In the solution open **Get Current Federal Threat Brief** in Power Automate.
2. Confirm its CISA action uses the destination **CISA KEV** reference and
   operation **GetKEV**, not an unrelated connector or a replacement HTTP action.
3. Confirm the cache actions use **WatchTower Evidence Cache SharePoint** and
   the configured site/list, not another application's reference. Resolve
   connection or save errors.
4. In the flow details page confirm **Status: On**; use **Turn on** if needed.
   Turning on this flow does not publish the agent.
5. Verify the agent-compatible trigger and response remain present:
   optional text input titled `cveId`, internal property `text`; response
   titled `ThreatBrief`, internal property `threatbrief`.
   The optional Boolean `includeCatalogPriorities` (internal `boolean`) is
   manually fixed by the tools, not requested from users.
   `actor` and `conversation` are manually bound system identity inputs,
   not generated by AI or prompted from users.
6. Keep synchronous agent response behavior. After the agent's first test
   request, inspect run history for successful retrieval, catalog validation
   and response. Inspect duration as well as success.
7. Open **WatchTower Evidence Retention**. Confirm its only external connector
   is SharePoint and its status is **On**. Its trigger accepts `operation`,
   `actor`, `conversation`, `cve`, `raw` and current-question context
   `question`; its response returns `status`, `evidence`, `provenance` and
   `requestQuestion`. It does not retrieve federal source data.
8. If current environment values changed after activation, turn both flows
   off and on through their details pages, then start a new agent test session.
   Confirm the resolved destination through successful cache writes; do not
   assume a saved environment-variable form updated an existing run.

Do not redesign the flow, replace its failure result with zero counts, or
change its field names during installation.

### 8. Verify seven native tools and the internal NVD adapter

1. Open Copilot Studio in the destination environment and select **WATCHTOWER**.
2. Reload the page after import.
3. Open the agent's **Tools** tab. Expect **Flow (7)**. The NVD capture adapter
   is a procedural topic and can appear under **Topics**, not as a separate
   native connector row:

| Tool | Type | Input and evidence |
|---|---|---|
| WatchTower NVD CVE Evidence | Internal data-only adapter | Exact `cveId`; captures the real Invoker connector response and returns its validated evidence envelope |
| WatchTower KEV Membership | Flow | Exact CVE through `text`; returns full-catalog membership in `threatbrief` |
| WatchTower Recent KEV Additions | Flow | Manually fixed blank `text`; uses recent entries and recorded window |
| WatchTower Leadership Priorities | Flow | Manually fixed blank `text`; uses first five `topKev` |
| WatchTower Morning Briefing | Flow | Manually fixed blank `text`; separates three priorities from recent additions |
| WatchTower Top Three KEV Threats | Flow | Manually fixed blank `text`; uses first three `topKev` |
| WatchTower Retained Evidence | Flow | Current question and optional exact CVE or blank for general evidence; fixed read-only operation and system identity |
| WatchTower Version and Fix Evidence | Flow | Current version/fix question and exact contextual CVE; same scoped read-only flow, no source refresh or advisory retrieval |

4. Confirm the seven native tools and the NVD adapter are enabled, available
   to orchestration, and free of blocking
   checker errors.
5. Open **WatchTower NVD CVE Evidence**. Confirm **GetSpecificCVE** and
   **Invoker/user-provided credentials**. Do not change to a shared embedded
   key merely to avoid user authorization.
6. Confirm the four general tools bind an empty string manually rather than
   asking the model or user for a CVE. A general briefing must not reuse a
   CVE from an earlier turn.
   Recent KEV Additions also fixes `includeCatalogPriorities` to false: its
   response omits catalog-wide priorities so older threats cannot contaminate
   a recent-only update. The other four CISA tools fix that setting to true.
7. Verify both evidence readers fix `operation` to `READ`, `raw` to blank,
   `actor` to the authenticated system user and `conversation` to the current
   conversation. Users and AI must not choose these values.
   The NVD adapter alone captures its actual connector output and calls
   the internal `WRITE_NVD` path; do not expose that path as a generative tool
   or add a user/model input for evidence JSON.
8. Preserve sign-in, error and conversation-control system topics. Confirm
   **Check organizational obligation claims** is enabled with the
   **AI response generated** trigger. Open **More > Topic checker** and resolve
   any errors. Its condition examines generated wording; on a match it sets
   **ContinueResponse** to false and sends one validation notice. Safe answers
   pass unchanged. The former hidden evidence variable and report replacement
   are unused; do not restore the retired logic or disable the new check.

### 9. Review instructions, model and authentication

1. On **Overview**, review the imported instructions. They require exact
   CVE/product matching, independent NVD and CISA lookups, source status,
   version boundaries, conditional mission impact and human oversight.
2. Confirm generative orchestration is available and the intended model is
   selected. The source model hint is **GPT-5.1 Chat**; review availability,
   moderation and related AI settings under your destination policy.
   Keep **Use general knowledge** disabled; enabling it changes the
   evidence-only answer boundary.
3. In **Settings > Security > Authentication** or the corresponding
   authentication page, verify the approved organizational sign-in model.
4. Configure the intended audience and connector sharing. Do not enable
   anonymous access to work around a sign-in or connection issue.
5. Start a **new test session**. A stale test session may continue to use an
   earlier imported definition.
6. On the first exact-CVE request, use **Open connection manager** if prompted
   and authorize the signed-in user's existing NVD connection.
   Import-time reference mapping does not complete per-user authorization.
7. Repeat that identity check with an ordinary authorized user, not just
   the installation maker.

### 10. Run acceptance before publication

Use the [verification prompts](#verification-prompts) and inspect tool traces.
A plausible answer is not proof that the right sources were called.

Confirm exact-CVE questions call both NVD CVE Evidence and KEV Membership for
the same identifier. General questions should call the appropriate one of the
four briefing tools with a blank input and produce one tailored answer.
Repeat a general request after a CVE question to detect stale identifier reuse.

Review **Agent checker** and the built-in **Evaluation** experience. Resolve
blocking errors and record any remaining warnings with the owner's decision.
A no-evaluation warning means evaluation acceptance is still outstanding.

Retain sanitized evidence of package version, environment, user role, prompt,
tool/flow run, expected result and actual result. An installation or maker
check does not certify every destination, audience or channel.

## Custom connector reference

A **connector** describes the API; a **connection** is the environment-specific
instance, including credentials where required; a **connection reference**
binds the imported solution to that destination instance.

### CISA KEV

| Property | Value |
|---|---|
| Display/logical name | `CISA KEV` / `cr8ac_cisa-20kev` |
| Definition | Swagger 2.0, connector version `1.1` |
| Host/base path | `https://www.cisa.gov` / `/sites/default/files/feeds` |
| Operation | `GetKEV` |
| Request | `GET /known_exploited_vulnerabilities.json` |
| Parameters/authentication | No request input; no authentication |
| Consumer | Get Current Federal Threat Brief flow |

The connector downloads the **full catalog**, not a pre-ranked report:

| Returned field | Meaning |
|---|---|
| `catalogVersion`, `dateReleased` | Identify the source snapshot |
| `count` | Catalog-reported total, not the number of recent additions |
| `vulnerabilities[]` | Full set used for matching and prioritization |
| `cveID` | Exact CVE identifier |
| `vendorProject`, `product`, `vulnerabilityName`, `shortDescription` | Published descriptions |
| `dateAdded` | Calendar date added to KEV, not an exact exploitation timestamp |
| `dueDate`, `requiredAction` | Published action context; do not assume every deadline applies universally |
| `knownRansomwareCampaignUse` | Published Known/Unknown; Unknown does not mean never exploited |

### NVD CVE connector

| Property | Value |
|---|---|
| Display/logical name | `NVD-CVE-Connector` / `cr8ac_nvd-2dcve-2dconnector` |
| Definition | Swagger 2.0, connector version `1.2` |
| Host/base path | `https://services.nvd.nist.gov` / `/rest/json` |
| Operation | `GetSpecificCVE` |
| Request | `GET /cves/2.0?cveId=<exact CVE>` |
| Authentication | Secure API-key connection; HTTP header `apiKey` |
| Consumer | Internal NVD capture adapter; the connector call remains in Invoker mode |

The OpenAPI security scheme is named `api_key`; it maps to the HTTP header
`apiKey`. The connection parameter is a required secure string with clear-text
display disabled. The scheme name, header name and secret value are different
things. Do not rename the header or remove authentication.

The adapter binds the actual dynamic connector response before serialization.
Its `Any` output override preserves returned properties absent from the static
schema, including newer nested fields; it does not invent a `Response` wrapper.
The source response includes `totalResults`, `timestamp` and `vulnerabilities`.
Within `vulnerabilities[].cve`, the typed connector schema includes:

- Exact ID, `published`, `lastModified` and `vulnStatus`.
- Descriptions, available CVSS metrics, weaknesses and reference links.
- CPE configurations, logical operators, vulnerability flags and inclusive/
  exclusive version boundaries where the source supplies them.

Accept an exact record only when there is one result and its ID matches.
Zero results with an empty array means no returned record. A failed call,
invalid key or HTTP error is not proof of absence.

Instructions prefer CVSS 4.0, then 3.1, 3.0 and 2.0, preserving the selected
metric's actual provider, Primary/Secondary type, vector and score. A CNA score
hosted in NVD is not necessarily NVD-authored analysis. Missing metrics are not
zero. Preserve the source's analysis status.

Affected/fixed versions require explicit returned evidence. Reference links
are links, not retrieved advisory contents. Do not infer a vendor fix or
collapse complex CPE conditions into an unsupported version range.

## Native tools and flow contract

Seven tools are native `TaskDialog` components: five CISA tools and two scoped
readers. The NVD capture adapter is an `AdaptiveDialog` with deterministic
connector and flow actions, not a child agent or a generated preliminary
answer. It validates the CVE before calling NVD, binds the complete actual
response, and calls the retention flow's internal capture path.
The five CISA tools share **Get Current Federal Threat Brief**; their
descriptions select the intended evidence scope.

| Flow element | Contract |
|---|---|
| Input title/internal name | Optional text `cveId` / `text` |
| Priority-scope title/internal name | Optional Boolean `includeCatalogPriorities` / `boolean`; false for Recent KEV Additions, true for the other flow tools |
| Normalization | Trim whitespace and uppercase before matching |
| Valid input | Blank, or CVE + four year digits + at least four suffix digits, with hyphens |
| Output title/internal name | Text `ThreatBrief` / `threatbrief`, plus text `provenance` |
| Output content | Serialized JSON; inspect `sourceStatus` first |
| General-tool input | Fixed blank string; no user prompt |
| Membership-tool input | Exact selected CVE; independent of the NVD request |
| Identity inputs | `actor` and `conversation`, fixed from authenticated system variables |
| Cache outcome | `cacheStatus`; cache failure does not turn a valid source result into a negative finding |

`provenance` is a compact JSON string returned separately from the larger
evidence. The CISA result preserves its actual source URL, `sourceStatus`,
`catalogVersion` and measured `retrievalTimestampUtc`. This does not replace
inspection of `threatbrief` or make its evidence scope broader.

### NVD capture envelope

The adapter returns `evidence`, a JSON string containing `sourceStatus`,
`requestedCveId`, `retrievalTimestampUtc`, the actual `sourceEvidence` response,
and `cacheStatus`. An explicit zero-result response with an empty
`vulnerabilities` array is `NotFound`. A missing, failed or mismatched response
is unavailable, never evidence of absence. The source's original `timestamp`
remains separate from the flow-measured capture time.
The adapter also returns compact `provenance`; its NVD entry separates
`apiTimestamp` from `retrievalTimestampUtc`. Missing source dates remain
unavailable, not the current time. Copy canonical source URLs exactly;
changing a CVE URL's ASCII hyphens into typographic hyphens breaks the link.

The adapter does not accept evidence JSON from the user or AI. It uses
`InvokeConnectorAction` with Invoker credentials, a dynamic output binding,
then `InvokeFlowAction`. Keep those bindings intact. A native connector tool's
outputs cannot simply be assigned to a cache variable by adding an output
name; that would not establish trustworthy capture.

### Source status and exact membership

| Field/result | Meaning |
|---|---|
| `sourceStatus: Available` | Catalog retrieval, validation and processing succeeded |
| `catalogVersion`, `count`, `retrievalTimestampUtc` | Source snapshot, complete catalog total and flow retrieval time |
| `sourceUrl` | Canonical CISA catalog link |
| `assessmentScope` | WATCHTOWER-authored boundary, not a CISA statement: customer exposure, legal applicability and obligations are not assessed |
| `requestedCveId` | Normalized requested identifier, or blank |
| `kevStatus: Yes` | Exact matching `kevMatches` entry from the complete catalog |
| `kevStatus: No` | Successfully searched complete catalog; no matching entry |
| `kevStatus: Not requested` | Blank general-briefing input |
| `kevStatus: Unknown` | Invalid input, source failure or processing failure; never treat as No |

The flow validates required fields, nonempty catalog, CVE/date shape and
agreement between catalog `count` and actual entries. A successful response is
set only after validation and processing finish. Failure results are explicit;
they do not present empty arrays or zero recent counts as successful retrieval.

Malformed input returns `sourceStatus: InvalidInput`,
`errorCode: INVALID_CVE_ID` and Unknown without calling CISA.
Source/processing failure returns Unavailable/Unknown with an error code such
as `KEV_LOOKUP_FAILED`; an incomplete catalog can return
`KEV_CATALOG_INCOMPLETE`. Inspect run history without exposing credentials.

NVD and CISA are independent. If one fails, any remaining evidence must be
labeled partial, not complete. Instructions request the other source once;
verify connector-failure behavior in the actual destination because platform
errors can interrupt orchestration. The system error handler explicitly
reports incomplete source coverage and does not print raw connector messages.
CISA and NVD requests have a 30-second source-request timeout; this is not a
guarantee of overall conversation latency.

Exact-CVE flow responses contain only provenance, scope and that identifier's
membership evidence. They omit unrelated recent/priority vulnerabilities.

### Recent additions and priority order

`newKevLast24hCount` is the full recent-window count. `newKevLast24h` contains
at most 20 prioritized rows; `recentTruncated` reports omitted rows.
`recentWindowStartDate` and `recentWindowEndDate` record the inclusive UTC date
boundaries. The names retain API compatibility, but the window is date-based:

> CISA dateAdded provides calendar dates, not exact rolling 24-hour timestamps.

Zero additions does not mean no exploitation, no new vulnerabilities or no risk.

`topKev` contains at most five entries from the **entire validated catalog**,
including older entries. Each projected entry retains its own CVE, vendor,
product, description, dates, source context and `publishedSignalRisk`.
The recent-only tool omits `topKev` and `priorityBasis` at the flow boundary;
it does not replace them with a misleading empty full-catalog result.
Callers omitting the optional scope setting retain the full response.
WATCHTOWER derives that label using:

| Published-signal category | Basis |
|---|---|
| Critical | Known ransomware use or due within seven days, including overdue |
| High | Otherwise due within thirty days |
| Moderate | Remaining entries |

Sorting is category first, newest `dateAdded` next, then due date and CVE ID.
The label is WATCHTOWER's heuristic, **not CISA-assigned severity, NVD CVSS or
customer-specific risk**. The agent must preserve returned associations and
ordering instead of silently re-ranking or moving facts between vulnerabilities.
Each returned entry includes `riskAssessmentAuthor` beside its calculated
label so the author is retained in exact-CVE and general responses alike.

## Isolated evidence retention

This is **retrieval-augmented answering over captured public-source
snapshots**, not vector/semantic search over SharePoint and not a general
document-ingestion system.

| Property | Behavior |
|---|---|
| Writers | Actual bound NVD connector output through the internal adapter; source-written CISA flow result |
| Readers | WatchTower Retained Evidence and WatchTower Version and Fix Evidence; fixed `READ`, blank `raw`; SharePoint only |
| Isolation | Exact authenticated user, conversation, normalized CVE or `GENERAL`, and NVD/CISA source |
| Freshness | Original NVD timestamp, measured source retrieval/capture times and returned CISA scope |
| Selection | Latest matching row by ID, separately for each source |
| Logical expiry | 24 hours after each capture; expired rows are excluded |
| Physical cleanup | Each write attempts to delete at most one expired row; no scheduled cleanup is installed |
| Size limit | 50,000 characters per serialized stored result |
| Oversize handling | Small `EvidenceTooLarge` status row, not a truncated result or fallback to an older successful snapshot |
| Cache failure | Explicit status; a valid fresh result remains usable even if it could not be saved |

The retention flow accepts `operation`, `actor`, `conversation`, `raw` and
optional `cve` and `question`. `WRITE_NVD` is called only by the deterministic
adapter. `READ` returns JSON through `evidence` plus separate `status`,
`provenance` and `requestQuestion` strings. Both native readers automatically
supply the current question and contextual CVE, while their identity and
read-only bindings remain fixed.
`requestQuestion` echoes untrusted request context; it is not source evidence
and is never added to cached evidence or provenance. Inputs/outputs can still
appear in flow run history, so do not put secrets in questions.
The read view contains `nvd`, `cisa`, `readTimestampUtc`,
`requestedCveId`, `externalSourceCalls: 0` and limitations. A cache-read
timestamp never makes old source evidence current.

| Status | Required interpretation |
|---|---|
| `CachedEvidence` | At least one scoped, unexpired result exists; inspect each source independently |
| `NoCachedEvidence` / per-source `NotCached` | No usable snapshot for that user/conversation/scope; not a negative CVE/KEV finding |
| `NotConfigured` | Retention is disabled or destination settings are incomplete |
| `InvalidInput` / `IdentityUnavailable` | Required request or trusted identity unavailable; no cross-user fallback |
| `InvalidCachedEvidence` / `ExecutionFailed` | Data validation or retrieval failed; no source facts should be inferred |
| `Saved` | Cache write succeeded |
| `WriteFailed` | Fresh evidence was not persisted; inspect flow history |
| `SavedCleanupFailed` | Write completed but opportunistic cleanup failed |
| `EvidenceTooLarge` | Only an explicit oversize status was retained; source facts were not cached |

Unavailable source attempts and oversized responses must not be presented
as fresh no-match findings. A newer status row intentionally prevents an older
successful result from being mistaken for the latest captured attempt.
If a write itself fails, an older row can still exist; inspect the current
`cacheStatus` and original dates instead of claiming the failed write persisted.

For general CISA evidence, the saved result preserves whether the original
request was recent-only or included catalog-wide priorities. Retention does
not expand a three-/five-entry projection into an exhaustive catalog report.
The reader validates returned identities and source/CVE consistency in addition
to its server-side list filter.

SharePoint permissions, flow run history and administrator access remain
separate access boundaries. A 24-hour logical expiry is not a guarantee of
physical deletion at exactly 24 hours. Recycle bins, backups, tenant retention
and audit policies can retain data longer. Assign an administrator to monitor
growth, remove expired rows under the customer's approved process, and review
list-threshold/index behavior at the intended volume.

## Verification prompts

Use a fresh session after import. Inspect the selected tools, their inputs and
outputs, and the final wording. These are functional checks, not prewritten
findings or assertions about current KEV membership.

### Copy-and-paste walkthrough

Send one prompt at a time. Keep prompts **2 through 6 in the same conversation**
so the follow-ups can use that conversation's captured evidence. Start a new
conversation only for prompt 7.

**1. Morning leadership briefing**

```text
Give me a morning cyber briefing. Separate recent CISA KEV additions from
the top three catalog-wide priorities. Explain potential mission impact
conditionally, identify evidence gaps, and cite sources and retrieval dates.
```

Expect a fresh CISA briefing that separates recent additions from catalog-wide
priorities without assuming the customer's assets or exposure.

**2. Research a specific vulnerability**

```text
Analyze CVE-2023-23397 using current NVD and CISA KEV evidence. Explain
severity, affected-version evidence, known exploitation and published actions.
Distinguish public facts from whether our organization is affected.
```

Expect both exact-CVE sources, actual source status and provenance. Verify
that both source results are retained before testing no-refresh follow-ups.

**3. Test version/fix follow-ups**

```text
Without refreshing either source, explain which affected versions the retained
evidence actually identifies. Does it verify a particular fixed build? Quote
the original version constraints and preserve source dates.
```

Expect a scoped evidence-reader call, literal source constraints and explicit
uncertainty about any fixed build the captured evidence does not verify.

**4. Generate an executive summary**

```text
Using only that retained evidence, create an executive brief under 400 words.
Map proposed actions to Govern, Identify, Protect, Detect, Respond and Recover.
Separate verified facts, conditional recommendations and unknowns.
```

Check all six NIST CSF 2.0 functions, actual word count, unchanged source dates
and no new NVD/CISA calls.

**5. Exercise the wording check**

```text
Using only retained evidence, contrast the unsupported assertion "CISA requires
organizations to apply updates" with the actual requiredAction field. Quote
that assertion exactly once, then explain the applicability gap. Do not
refresh sources.
```

If the generated response contains the matched wording, expect **one
withholding notice**, not the original generated answer or a second report.
This intentionally exercises the conservative treatment of quoted examples.
The requested wording alone does not prove interception: inspect the actual
response and topic trace.

**6. Confirm normal answers still work afterward**

```text
Now give the actual CISA requiredAction as a quoted source field and one
conditional leadership recommendation. State that customer applicability
is not assessed. Use retained evidence only and stay under 100 words.
```

Expect a useful permitted answer, not another withholding notice. The prior
withholding decision must not suppress the next ordinary response.

**7. Check conversation isolation**

Start a **new conversation**, then send:

```text
Using retained evidence only, summarize CVE-2023-23397.
Do not query NVD or CISA.
```

Expect an explicit cache miss for the new conversation, not facts borrowed
from the previous conversation, a silent source refresh or a claim that the
vulnerability does not exist.

### Additional verification cases

| Prompt | Expected behavior |
|---|---|
| What changed in CISA KEV in the last 24 hours? Give me a concise leadership update grounded in the live evidence. | Recent KEV Additions; blank CVE; actual count/window, up to three recent concerns and calendar-date caveat |
| I have five minutes before a leadership call. Give me the top five catalog-wide priorities, conditional mission impact and proposed leadership actions. | Leadership Priorities; first five available `topKev`, source order and heuristic explained |
| Analyze CVE-2023-23397 using current NVD and CISA KEV evidence. Separate verified facts, gaps, potential mission impact and leadership decisions. | Both exact-CVE tools for the same ID; precise status, source links/dates and no exposure claim |
| Create a morning cyber briefing. Separate catalog-wide priorities from new KEV additions and explain what leadership should do next. | Morning Briefing; three catalog priorities distinct from recent additions |
| Review the latest CISA KEV evidence and identify the three most concerning returned threats for a federal organization. | Top Three KEV Threats; three available catalog candidates with correct CVE/product/risk associations |
| For that CVE, use only the evidence already returned and map recommendations to Govern, Identify, Protect, Detect, Respond and Recover. | Retained evidence only, original dates, all six NIST CSF 2.0 functions; no NVD/CISA refresh or invented observations |
| Create a one-page executive summary for that CVE, including uncertainty and recommended next steps. Stay within 400 words. | Concise evidence-supported follow-up, actual length checked, no invented version range |
| Without refreshing sources, explain which affected and fixed versions the retained evidence actually verifies, and what remains unknown. | Version and Fix Evidence, literal affected records/CPE conditions; no inferred advisory contents or unsupported fixes |
| Using the same retained evidence, quote the affected-version constraints again and explain whether they establish a fixed build. | A new scoped version-reader call with this question; original dates, no fallback, invented interval or source refresh |
| Using retained evidence only, summarize CVE-2026-99999999. Do not query either public source. | A cache miss is explicit; no silent fresh lookup and no invented vulnerability facts |
| Now give me a general update on recent KEV additions, not the previous CVE. | General tool receives blank input; no stale-CVE lookup or unnecessary identifier question |

For a broader functional walkthrough:

```text
Prepare an executive cyber assessment of CVE-2023-23397 using current NVD and
CISA KEV evidence. Explain the vulnerability, NVD analysis status, available
CVSS score with its actual provider, affected-version evidence, exact KEV
membership and published action dates. Separate verified facts from conditional
mission impact and operational concerns. Map proposed leadership actions to
all six NIST CSF 2.0 functions, identify evidence gaps, cite sources and dates,
and conclude with human-review requirements. Do not assume our agency runs an
affected product, has been compromised, or has implemented a vendor fix.
```

Also exercise the following in an isolated test environment:

| Check | Acceptance |
|---|---|
| Exact old KEV entry | Yes even when outside recent/top lists |
| Valid CVE absent from current KEV | No only after successful full-catalog lookup |
| NVD zero-result case | No record returned; no fabricated vulnerability |
| Malformed CVE | Clarification or explicit invalid input, not a substituted identifier |
| Source failure | Unknown/partial coverage, not empty success or false No |
| Missing metric/version fields | Explicit uncertainty, not zero or model-memory facts |
| No recent additions | Under 150 words, accurate zero/window and no unrelated older threats or claim of no risk |
| Customer legal applicability | Explicitly not assessed; no conclusion that directives/obligations are binding, enforceable, satisfied or unchanged |
| Pre-send wording check | A matched generated phrase runs Check organizational obligation claims; one explicit notice, no original generated answer or duplicate report. Verify the actual generated text/trace, not merely the user's requested wording. |
| Allowed source wording | Literal quoted requiredAction fields and ordinary conditional recommendations remain usable; compare quotations with actual tool evidence |
| Large recent result | True total, bounded list and truncation disclosure |
| User suggests an unsupported version | Do not adopt it as verified evidence |
| Same-conversation no-refresh summary | Retained reader only; original NVD/CISA dates and no new public-source calls |
| Repeated no-refresh/NIST/version follow-ups | Every requested answer is grounded; no no-information fallback or silent source refresh |
| New conversation or different user | Cannot retrieve the previous conversation/user's rows |
| Expired, malformed or oversized cache | Explicit gap/status; no false no-match or reuse of an older success as the latest attempt |
| SharePoint write failure | Fresh valid source result survives with explicit failed cache status |
| SharePoint direct access | Ordinary agent users cannot read or modify the cache list |
| Ordinary authorized user | Correct sharing, sign-in and Invoker connection behavior |
| Unauthorized user | Access denied under the intended policy |
| Intended published channel | Same source, identity and response requirements as maker testing |

Choose current positive/negative membership cases from a fresh catalog.
Simulate failures safely; do not invalidate a production credential to test.
Check timing and payload limits with a representative catalog. Built-in
evaluation, ordinary-user access and channel acceptance are separate checks.

For built-in evaluation, use actual reference answers rather than instructions
to the grader in **Expected response**. Include correct abstentions for empty
or unavailable evidence. Inspect failed grades against the returned tool data
and the acceptance contract; preserve the run and review the rubric rather
than lowering thresholds or inventing facts to improve a score.

A withheld unsafe answer passes only the specific blocking check, not a
normal-answer acceptance case. Verify useful permitted answers separately.
Single-response evaluation does not prove a multi-turn sequence: also run fresh
capture, consecutive version/fix follow-ups, the retained NIST brief and
conversation/CVE isolation checks while comparing source dates and tool calls.

## Publish and share

1. Complete destination acceptance and review checker/evaluation results.
2. Confirm owners, connection references, Invoker authorization, model,
   authentication and intended audience one final time.
3. Have the authorized owner select **Publish** in Copilot Studio and confirm
   publication succeeds. This is separate from solution import or turning on
   the CISA flow.
4. Open **Channels** and configure the approved channel, such as Teams or
   Microsoft 365 Copilot where available.
5. Complete organizational app approval and restrict the pilot audience.
   Imported channel identifiers alone do not complete channel registration.
6. Open/install as an ordinary pilot user and repeat the source, sign-in,
   follow-up and failure checks in the real channel.
7. Confirm unauthorized access is denied before widening the audience.
8. Record environment, version, publication time, audience, channel and owner.

Do not rely only on a maker's test pane: channel identity and connection
behavior can differ.

## Troubleshooting

| Symptom | Corrective action |
|---|---|
| Custom connector missing during import | Import the connectors-only ZIP first and wait for registration |
| Import fails or dependencies remain | Download the import log; verify both same-version packages and destination environment |
| Wrong connection selected | Correct the relevant solution connection reference and verify actual tool/flow bindings |
| CISA connection requested despite public feed | Create/select its no-auth connection; public access does not eliminate the connection object |
| NVD asks the user to connect | Use Open connection manager and authorize that user's intended NVD connection |
| NVD 403/404 or Invalid apiKey | Verify activation and securely correct the connection credential; not a no-record result |
| NVD 429 | Respect upstream throttling and space requests; do not loop retries |
| Maker works, ordinary user fails | Review connector sharing, environment roles, audience and per-user authorization |
| General briefing asks for a CVE | Verify the four general tools manually bind an empty `text`; reload and start a fresh test session |
| Old research topics still execute | Verify version 1.0.2.4, seven native flow tools and the intended data-only NVD adapter; remove unapproved duplicate wrappers and restart testing. The obligation-check event topic is intentional. |
| Answer infers binding obligations or compliance | Confirm general knowledge is off and the current instructions are installed; source applicability is not assessed and requires authorized human governance review |
| Prior-evidence-only follow-up returns no information | Failed acceptance: inspect the selected evidence reader, current-question input, CVE, authenticated identity, current conversation, cache status and original dates. Do not hide the failure by refreshing sources or enabling general knowledge. |
| ContentValidationError after editing a tool | Check authoring errors and metadata lengths: capability descriptions must fit 1,024 UTF-16 units and names 64. Correct the metadata; do not disable validation. |
| Cache reports NotConfigured | Set the three customer environment-variable current values, verify the private SharePoint connection, reactivate both flows and retest |
| Cache reports IdentityUnavailable | Verify organizational authentication and actual system user/conversation bindings; never substitute a shared actor key |
| Cache reports WriteFailed or ExecutionFailed | Inspect failed SharePoint actions, permissions, exact internal column names, settings and indexes; preserve the valid fresh result without claiming it was retained |
| Cache reports SavedCleanupFailed | The write succeeded; investigate the expired-row read/delete action and designated account's delete permission |
| Cached source missing after a new session | Expected conversation isolation; do not broaden the filter to recover another conversation's data |
| Flow is off | Resolve connection/save errors, then Turn on from flow details; this does not publish the agent |
| Flow times out or returns no response | Inspect failed actions, response run-after settings, synchronous response and duration |
| Older KEV CVE reported No | Verify successful full-catalog matching; recent/top-list absence is insufficient |
| Response changes product/risk facts | Compare actual tool evidence, correct routing/instructions and rerun acceptance; generated prose is not deterministically certified |
| Answer withheld by the obligation check | Inspect the actual generated phrase and source evidence. Ask for source facts and conditional recommendations; an authorized reviewer determines applicability. A quoted example or complex negation can conservatively match. Do not disable the check to hide an acceptance failure. |
| NVD output Response not found | Preserve the adapter's dynamic binding; its envelope contains the actual `sourceEvidence`, not a connector `Response` field |
| Version/CVSS field missing | Preserve uncertainty; inspect actual typed source response before assuming a connector error |
| Reporting window seems longer than 24 hours | Inspect returned inclusive UTC date boundaries; CISA dates lack time precision |
| Agent checker warns about evaluations | Run/review the built-in evaluation and record acceptance; import does not satisfy this gate |
| Imported agent unexpectedly appears published | Verify artifact, actual destination state and previous publication history; supplied configuration has publish-on-import false |

## Operations, upgrades and recovery

Assign primary/backup owners for the solution, agent, flows, connector definitions,
connections, credentials and channels. Monitor flow failures/duration, NVD
throttling, source schema changes, access and generated factuality. Review
transcript/run-history retention and access, SharePoint cache growth, expired
records, connection-owner continuity and direct-list permissions.

For NVD key rotation, activate the replacement and update or recreate the
supported connection securely. Rebind the relevant references/tool settings
where necessary and test as both maker and ordinary user. Updating a flow
connection does not automatically authorize the separate user-invoked NVD tool.

For upgrades, retain a known-good package, source revision, configuration record
and supported backup. Qualify changes in an isolated environment, verify
connections and sharing, repeat acceptance, then publish deliberately.
Use the organization's approved process for managed downstream deployment;
these supplied ZIPs are unmanaged.

For a failed release, restrict the affected audience where appropriate,
preserve diagnostics and follow the approved environment restore or solution
recovery process. A Git revert does not roll back a live environment.
Uninstalling a solution can delete components or affect dependencies; do not
use it as a shortcut recovery method.

## Handoff checklist

- [ ] Destination environment, cloud, owner and backup are recorded.
- [ ] Required permissions, DLP, outbound access and audience are approved.
- [ ] Both same-version ZIPs were imported successfully in the correct order.
- [ ] CISA and NVD destination connections were tested and references mapped.
- [ ] The separate private cache, exact columns/indexes and designated SharePoint connection were verified.
- [ ] The three customer current values are set; no shipped/source-tenant binding was reused.
- [ ] NVD key handling and ordinary-user Invoker authorization were verified.
- [ ] Both flows are on; seven native tools and the data-only NVD adapter are enabled without blockers.
- [ ] Both actual sources write scoped evidence; no-refresh follow-ups preserve dates without new source requests.
- [ ] CVE, recent, top-five, morning, top-three and follow-up checks pass.
- [ ] Missing-data, source-failure, identity and unauthorized-access checks pass.
- [ ] Generated facts, version bounds, CVSS attribution and risk labels match evidence.
- [ ] Matched obligation wording is withheld once; allowed source quotations and normal answers remain usable.
- [ ] Checker/evaluation results and destination acceptance evidence were reviewed.
- [ ] The owner explicitly published and the intended channel passed pilot checks.
- [ ] Credential rotation, monitoring, retention and recovery responsibilities are assigned.

## Source reference

Customers install the ZIPs; the following is a reference for authorized
maintainers and reviewers, not an installation prerequisite.

| Source | Purpose |
|---|---|
| [Exported solution](WatchTower/) | Complete solution source; customers import the distributed ZIPs |
| [Focused regression tests](tests/) | Maintainer fixture checks; not a substitute for destination acceptance |
| [Solution manifest](WatchTower/solutions/WatchTower/solution.yml) | WatchTower 1.0.2.4, publisher and package identity |
| [Component manifest](WatchTower/solutions/WatchTower/solutioncomponents.yml) | Complete agent, tools, connectors, references and associations |
| [Agent instructions](WatchTower/botcomponents/cr8ac_agent5a7uFN.gpt.default/data) | Routing, evidence requirements, scope and human oversight |
| [Obligation wording check](WatchTower/botcomponents/cr8ac_agent5a7uFN.topic.ValidateCveResponse/data) | Narrow pre-send condition, generated-answer suppression and one explicit notice |
| [NVD connector](WatchTower/connectors/cr8ac_nvd-2dcve-2dconnector/cr8ac_nvd-2dcve-2dconnector_openapidefinition.json) | Exact-CVE endpoint, typed response and API-key header |
| [CISA connector](WatchTower/connectors/cr8ac_cisa-20kev/cr8ac_cisa-20kev_openapidefinition.json) | Public catalog endpoint and response schema |
| [Threat-brief flow](WatchTower/modernflows/f8cd138a-4eb9-f111-aaab-001dd809dc87/GetCurrentFederalThreatBrief-F8CD138A-4EB9-F111-AAAB-001DD809DC87.json) | Input/catalog validation, exact membership, ordering and explicit failure result |
| [NVD adapter](WatchTower/botcomponents/cr8ac_agent5a7uFN.action.NVD-CVE-Connector-GetSpecificCVE/data) | Actual dynamic response capture, retained evidence and preserved Invoker credentials |
| [Retained reader](WatchTower/botcomponents/cr8ac_agent5a7uFN.action.RetainedEvidence/data) | Fixed read-only operation and trusted identity bindings |
| [Version and Fix Evidence](WatchTower/botcomponents/cr8ac_agent5a7uFN.action.VersionEvidence/data) | Scoped version/fix follow-up reader with separate current-question context |
| [Retention flow](WatchTower/modernflows/e25f7552-aee2-4c62-9db3-9eaeb426b7a0/WatchTowerEvidenceRetention-E25F7552-AEE2-4C62-9DB3-9EAEB426B7A0.json) | NVD validation/capture, isolated SharePoint reads/writes and explicit cache failures |
| [Retention builder](retained_evidence.py) | Maintainer-only generator for source retention definitions and portable settings |
| [KEV membership](WatchTower/botcomponents/cr8ac_agent5a7uFN.action.KevMembership/data) | Independent exact-CVE flow action |
| [Recent additions](WatchTower/botcomponents/cr8ac_agent5a7uFN.action.GetCurrentFederalThreatBrief/data) | Fixed-blank recent-evidence action |
| [Leadership priorities](WatchTower/botcomponents/cr8ac_agent5a7uFN.action.LeadershipPriorities/data) | Top-five evidence action |
| [Morning briefing](WatchTower/botcomponents/cr8ac_agent5a7uFN.action.MorningBriefing/data) | Separate priority/recent scopes |
| [Top three](WatchTower/botcomponents/cr8ac_agent5a7uFN.action.TopThreeThreats/data) | Top-three evidence action |

Preserve relationship records and component identities when maintaining the
solution. A flow merely existing in a solution does not attach it as an agent
tool. Compatibility identities are retained for updates. The former response
interceptor's identity now hosts only the narrow obligation wording check;
the old hidden topic-owned evidence envelope and report replacement remain unused.

## Official references

- [Import/export Copilot Studio agents](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-solutions-import-export)
- [Solution custom connectors and import ordering](https://learn.microsoft.com/en-us/connectors/custom-connectors/customconnectorssolutions)
- [Agent flow requirements](https://learn.microsoft.com/en-us/microsoft-copilot-studio/flow-agent)
- [Agent authentication](https://learn.microsoft.com/en-us/microsoft-copilot-studio/configuration-end-user-authentication)
- [Publish an agent](https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-fundamentals-publish-channels)
- [NVD developer guidance](https://nvd.nist.gov/developers/start-here)
- [NVD CVE API reference](https://nvd.nist.gov/developers/vulnerabilities)
- [CISA Known Exploited Vulnerabilities catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
