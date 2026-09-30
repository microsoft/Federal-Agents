# CropSure

CropSure is a Microsoft Copilot Studio solution that combines agricultural
reference data with a structured, human-reviewed case workflow. The agent uses
USDA NASS Quick Stats and CropScape Cropland Data Layer (CDL) services to help
assemble validation packages, record findings, and route recommendations for
review.

> [!IMPORTANT]
> CropSure supports validation and review; it does not make final eligibility,
> compliance, approval, or enforcement decisions. An authorized reviewer must
> make and record the final decision.

## Contents

- [Capabilities](#capabilities)
- [Architecture](#architecture)
- [Repository contents](#repository-contents)
- [Prerequisites](#prerequisites)
- [Pre-deployment configuration](#pre-deployment-configuration)
- [SharePoint Case Review list](#sharepoint-case-review-list)
- [USDA service configuration](#usda-service-configuration)
- [Deploy the solution](#deploy-the-solution)
- [Configure Power Automate](#configure-power-automate)
- [Configure and publish the agent](#configure-and-publish-the-agent)
- [Validation](#validation)
- [Security and governance](#security-and-governance)
- [Known issues and limitations](#known-issues-and-limitations)
- [Troubleshooting](#troubleshooting)
- [Operations and support](#operations-and-support)
- [Contributing](#contributing)

## Capabilities

CropSure provides:

- A conversational Copilot Studio agent for creating validation packages.
- Automatic Case ID generation when a package is created.
- USDA NASS Quick Stats lookups for agricultural acreage and production data.
- USDA CropScape/CDL lookups for crop classification and acreage evidence.
- Consolidated findings that identify evidence, discrepancies, confidence,
  limitations, and missing information.
- Recommendations using these supported values:
  - `Approve`
  - `Review Required`
  - `Escalate`
  - `Unable To Determine`
- A Power Automate workflow for creating and routing validation packages.
- SharePoint-backed case storage and human review.
- Microsoft Teams and Microsoft 365 Copilot channels.

The agent can create a validation package when information is incomplete.
Missing information is documented in the findings for the reviewer rather than
silently inferred or used to block an explicitly requested package.

## Architecture

```text
User
  |
  v
CropSure Copilot Studio agent
  |
  +--> USDA NASS Quick Stats custom connector
  |
  +--> USDA CropScape CDL custom connector
  |      |
  |      +--> CropScape results custom connector
  |
  v
Create CropSure Validation Package flow
  |
  +--> SharePoint: CropSure Case Review
  |
  +--> Standard Approvals
  |
  v
Authorized human reviewer
```

## Repository contents

The current export is an **unmanaged** Power Platform solution:

| Item | Current value |
|---|---|
| Solution unique name | `Cropsure` |
| Solution version | `1.1.0.0` |
| Publisher | `Cr4a132` |
| Agent schema name | `cr8ac_agent` |
| Packaged cloud flows | 1 |
| Packaged custom connectors | 3 |
| Packaged connection references | 2 |

Key paths:

- `bots/cr8ac_agent/` - agent definition and runtime configuration.
- `botcomponents/` - agent instructions, system topics, and tool components.
- `modernflows/` - the **Create CropSure Validation Package** flow.
- `connectors/` - NASS and CropScape custom connectors.
- `connectionreferences/` - SharePoint and Standard Approvals references.
- `solutions/Cropsure/` - solution manifest, component list, and dependency
  metadata.

The agent is configured with generative actions, semantic search, file
analysis, and model knowledge. It is connectable from other agents and is
configured to publish on import. Review these settings against customer policy
before production deployment.

## Prerequisites

### Platform

- A Power Platform environment with Dataverse.
- Managed Environments enabled where required by the Git integration.
- Permission to import or synchronize solutions and configure connection
  references.
- Copilot Studio access for the agent owners.
- Power Automate access for flow owners.
- A customer-approved SharePoint Online site.
- Network access to the required USDA public endpoints.
- A customer-owned USDA NASS Quick Stats API key.
- Test submitter and reviewer accounts with representative permissions.

### Recommended roles

| Role | Responsibility |
|---|---|
| Power Platform administrator | Environment readiness, policies, capacity, imports, and dependencies |
| Copilot Studio maker | Agent configuration, tools, testing, publishing, and channels |
| Power Automate owner | Connections, flow configuration, testing, monitoring, and recovery |
| SharePoint administrator/site owner | List schema, permissions, views, retention, and versioning |
| API credential owner | NASS API key storage, rotation, and monitoring |
| Business reviewer | Review findings and make the final decision |
| Solution owner | Release coordination, support ownership, and change control |

Use durable organizational or service-account ownership where organizational
policy discourages maker-specific ownership.

## Pre-deployment configuration

Record the following before importing or synchronizing the solution:

| Configuration item | Customer value |
|---|---|
| Target environment name and URL | |
| Environment type (`DEV`, `TEST`, or `PROD`) | |
| SharePoint site URL | |
| Case Review list name | `CropSure Case Review` |
| Solution/service account owner | |
| SharePoint connection owner | |
| NASS credential owner | |
| Agent security group or audience | |
| Primary and backup support contacts | |

Environment-specific values and secrets must not be committed to this
repository.

## SharePoint Case Review list

Create a blank custom list named **CropSure Case Review** in the approved
customer SharePoint site. Keep the built-in `ID` column.

Flow mappings are sensitive to SharePoint internal names and field types.
Create the schema before mapping flow actions, record each internal name from
List settings, and do not rename internal columns after the flows are mapped.

### Required baseline schema

| Display name | SharePoint type | Purpose |
|---|---|---|
| Validation Package | Single line of text | Package label or summary |
| CaseID | Single line of text | CropSure-generated case identifier |
| FarmerName | Single line of text | Farmer/producer name |
| FIPS | Single line of text | FIPS code; text preserves leading zeros |
| ReportedCrop | Single line of text | Reported crop |
| ReportedAcres | Number | Reported acreage |
| CropScapeCrop | Single line of text | Crop returned or derived from CropScape |
| CropScapeAcres | Number | Acreage returned or derived from CropScape |
| Findings | Multiple lines of text | Consolidated evidence and findings |
| Recommendation | Choice | CropSure recommendation |
| Status | Choice | Case workflow status |
| ReviewerComments | Multiple lines of text | Human reviewer comments |
| ReviewOutcome | Choice | Human review outcome |
| CreatedByAgent | Yes/No | Identifies agent-created records |

Recreate the source values for all Choice fields exactly. Before production,
compare the deployed flow mappings with the target list and add any fields the
flow requires, such as `ReviewerEmail` or review timestamps.

> [!WARNING]
> Do not require `CaseID` when the item is initially created. The workflow
> generates the Case ID, so making it required too early can cause SharePoint's
> Create item action to fail.

Recommended list configuration:

- Enable version history.
- Create views filtered by `Status` and `ReviewOutcome`.
- Separate submitter, reviewer, and administrator permissions.
- Define retention and sensitivity requirements for findings and raw API data.
- Confirm that the flow owner can create, read, and update list items.

## USDA service configuration

### NASS Quick Stats

The packaged **USDA NASS Quick Stats** connector targets:

```text
https://quickstats.nass.usda.gov/api
```

1. Request a customer-owned key from the USDA NASS Quick Stats API.
2. Store the key in an approved secret-management location.
3. Create the custom connector connection and provide the key through its
   secure `api_key` connection parameter.
4. Run a simple query and confirm a valid response before enabling production
   workflows.

Never put the API key in agent instructions, SharePoint columns, screenshots,
flow outputs, or source-controlled files.

### CropScape

The packaged connectors target:

```text
https://nassgeodata.gmu.edu/axis2/services/CDLService
https://nassgeodata.gmu.edu/webservice/nass_data_cache/byfips
```

The CropScape service can return XML or a reference to a generated results
file. Preserve any required XML-to-JSON transformation and validate the
response content type before parsing it as JSON.

## Deploy the solution

Use the deployment method required by the customer's application lifecycle
management process.

### Native Git synchronization

1. Connect the development environment or solution to the intended repository,
   branch, and folder.
2. Open the solution's **Source control** page.
3. Pull or synchronize the intended revision into the target development
   environment.
4. Review all incoming components and dependency messages.
5. Configure customer-owned connections and environment-specific values.

### Solution package import

1. In Power Apps, select the target environment.
2. Go to **Solutions** and select **Import solution**.
3. Select the CropSure solution package.
4. Confirm the solution name, publisher, version, and package type.
5. Resolve missing dependencies before continuing.
6. Map every connection reference to a customer-owned connection.
7. Complete the import and inspect solution history/import logs.
8. Confirm that the agent, flow, connectors, and connection references are
   present.

This repository contains an unmanaged development solution. Use a managed
build artifact for controlled production deployments unless the customer's ALM
standard explicitly requires an unmanaged solution.

## Configure Power Automate

The current repository packages one cloud flow:

- **Create CropSure Validation Package**

Open the flow from inside the imported solution and verify:

- The Copilot/agent trigger is present and connected.
- Every SharePoint action points to the customer site and list.
- Field mappings use the target list's correct internal names and types.
- Case ID generation occurs before downstream actions require the ID.
- NASS authentication, query parameters, and response parsing are valid.
- CropScape FIPS/year inputs and response conversion are valid.
- Every conditional branch reaches a Respond to Copilot/agent action.
- Error responses are understandable and do not expose credentials, connection
  details, or stack traces.
- No-data results are handled as a valid outcome.
- The flow passes Flow checker and a representative test run.

The implementation guide also describes status lookup, NASS lookup, and
CropScape lookup as logical capabilities. In this repository export, these are
not packaged as three additional standalone cloud flows. Confirm whether those
operations are implemented as agent tools/custom connectors or whether
additional solution components must be added before release.

## Configure and publish the agent

1. Open the imported **CropSure** agent in Copilot Studio.
2. Review authentication and apply the customer's required identity model.
3. Open **Tools/Actions** and verify each imported connector and flow.
4. Review all tool descriptions, inputs, outputs, and connection mappings.
5. Confirm that `CaseID` is not requested when creating a package; it is
   generated by the validation workflow.
6. Confirm that `ReviewerEmail` is supplied by the user or an approved
   configured default.
7. Test each tool independently.
8. Run the end-to-end validation scenarios below.
9. Publish only to approved audiences and channels.

The current configuration includes Microsoft Teams and Microsoft 365 Copilot
channels. Publishing is enabled on import, so verify channel access and
authentication promptly after deployment.

## Validation

### Smoke tests

| Test | Expected result |
|---|---|
| NASS lookup | Structured response or clear no-data response; no authentication error |
| CropScape lookup | Usable result after any required XML/result-file processing |
| Create package | One SharePoint record and a generated Case ID |
| Human review | Authorized reviewer can update the decision and comments |
| Access control | Unauthorized user cannot perform reviewer-only operations |
| Failure handling | Invalid input/credential returns a controlled message without exposing secrets |

### Suggested acceptance prompts

```text
Create a CropSure validation package for corn in the selected state and
county for the specified crop year. Use the provided county FIPS and claimed
acreage, compare NASS and CropScape results, save the package, and give me the
Case ID.
```

```text
Use the NASS connection to retrieve available corn acreage or production data
for the selected county and year. Explain when no matching record is returned.
```

```text
Use the CropScape connection for county FIPS [FIPS] and year [YEAR]. Return
the crop-land result and note any service or data limitations.
```

### Release acceptance

- [ ] Solution imported or synchronized without unresolved dependencies.
- [ ] Customer-owned connections are mapped and healthy.
- [ ] SharePoint schema and internal names are verified.
- [ ] NASS and CropScape operations are tested.
- [ ] Validation flow is enabled and tested.
- [ ] Agent authentication and audience are approved.
- [ ] Agent is published to approved channels.
- [ ] Human review and access-control scenarios pass.
- [ ] Monitoring, support, and credential rotation owners are documented.

## Security and governance

- **Human authority:** Treat agent output as a recommendation; the reviewer
  makes the final decision.
- **Least privilege:** Grant only the SharePoint, connector, environment, and
  agent permissions each role needs.
- **Secrets:** Use customer-approved secret storage and suppress secure values
  from inputs, outputs, logs, and documentation.
- **DLP:** Confirm that SharePoint, Approvals, and custom connector combinations
  comply with the target environment's data loss prevention policy.
- **Ownership:** Assign primary and backup organizational owners for the agent,
  flow, connections, list, and credentials.
- **Auditability:** Retain list version history and operational evidence needed
  to reconstruct the package and final human decision.
- **Retention:** Apply customer records policy to cases, findings, comments,
  and raw API responses.
- **Change management:** Test upgrades in nonproduction and document versions,
  configuration changes, rollback steps, and a known-good test case.
- **AI settings:** Review file analysis, semantic search, model knowledge,
  content moderation, and selected model against customer policy.

## Known issues and limitations

### Unresolved connection-reference dependencies

The current solution manifest reports that **Create CropSure Validation
Package** depends on:

- `cr8ac_sharedapprovals_71abd`
- `cr8ac_sharedsharepointonline_e4c01`

The repository currently packages differently named references:

- `cr8ac_sharedapprovals_4c436`
- `cr8ac_sharedsharepointonline_fde94`

Before release, open the flow in the source environment, bind its Approvals and
SharePoint actions to the packaged connection references, save it, add required
objects to the solution, and commit a new export. Do not treat the deployment
as production-ready while these dependencies remain unresolved.

### Portability

The current export does not include documented environment-variable components
for the SharePoint site/list or service endpoints. Verify that environment
specific values are not hard-coded. Add solution environment variables before
production if these settings are embedded in flow actions or connector
configuration.

### Package completeness

The guide describes several logical flow capabilities, while the repository
contains one packaged cloud flow. Confirm that the current agent tools satisfy
all intended status, NASS, and CropScape scenarios. Add any omitted flows,
references, variables, or tools to the solution before building a release.

## Troubleshooting

| Symptom | Likely cause | Corrective action |
|---|---|---|
| Import reports missing dependencies | Required references/components are absent or mismatched | Rebind the source flow, add required components, export again, and retry |
| Flow uses an old connection | Connection reference or action was not remapped | Map the reference, inspect every action, save, and enable the flow |
| Case ID appears required | Tool input or SharePoint column marks it required | Make it optional during creation and refresh the agent tool schema |
| CropScape JSON error starts with `<` | Service returned XML rather than JSON | Check content type and preserve XML-to-JSON conversion |
| SharePoint create/update fails | Wrong site/list, internal-name mismatch, required field, or permissions | Verify list metadata, mappings, required fields, and connection-owner access |
| Flow succeeds but agent receives no answer | A branch lacks a Respond action or returns null required outputs | Ensure every branch returns all required outputs |
| NASS returns unauthorized | API key is missing, invalid, or passed incorrectly | Recreate/test the customer connection and rotate the key if necessary |
| NASS returns no records | Valid query has no matching published data or is too narrow | Return a no-data result and review crop, geography, year, statistic, and aggregation |

## Operations and support

At production handoff, record:

- Deployed solution version, source commit, import date, and environment.
- Primary and backup owners for the agent, flow, connections, list, and API
  credentials.
- Published channels and approved audience.
- Credential rotation and connection reauthentication procedures.
- Failed-run monitoring and connector-health ownership.
- Business escalation path for disputed recommendations or insufficient data.
- Last successful smoke test and a known-good test case.
- Support queue and response expectations.

Rerun the known-good test after solution upgrades, connection changes,
credential rotation, or upstream USDA API changes.

## Contributing

1. Make changes in a development environment connected to a feature branch.
2. Keep solution components and this README synchronized.
3. Do not commit credentials, customer data, or environment-specific values.
4. Run Solution checker and the validation checklist.
5. Commit and push from the solution's **Source control** page.
6. Review the generated source diff before merging to `main`.

When changing the SharePoint schema, flow inputs/outputs, recommendation values,
or connector contracts, update the relevant sections of this README in the
same change.

## Version

README based on the CropSure Customer Implementation Guide, version 1.0
(September 2026), reconciled with the source-controlled CropSure solution
version `1.1.0.0`.
