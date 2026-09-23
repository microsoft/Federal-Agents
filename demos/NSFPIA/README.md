# NSF Proposal Intake Assistant POC

This repository contains the Microsoft Copilot Studio source and synthetic test assets for the NSF Proposal Intake Assistant proof of concept. The POC demonstrates administrative proposal intake, compliance checks, program routing, reviewer conflict screening, draft communications, and attributed panel-summary drafting.

> **Synthetic data only.** All proposals, people, institutions, relationships, reviews, and email addresses in this repository are fictional demo data. Do not add real proposal content, personally identifiable information, controlled information, or production credentials. This POC is not approved for production NSF data or decisions.

The operational build status is maintained in [Docs/Design/POC-BUILD.md](Docs/Design/POC-BUILD.md). Architecture and production-enablement requirements are documented in [Docs/Design/DESIGN.md](Docs/Design/DESIGN.md).

The final synthetic-data demonstration package, representative screenshots, acceptance links, known limitations, and production recommendations are indexed in [Docs/Evidence/POC-810/DEMO-EVIDENCE.md](Docs/Evidence/POC-810/DEMO-EVIDENCE.md).

## Prerequisites

- Git.
- PowerShell 5.1 or later.
- Visual Studio Code 1.80 or later.
- Microsoft Copilot Studio extension for Visual Studio Code (`ms-CopilotStudio.vscode-copilotstudio`).
- GitHub Copilot extension for Visual Studio Code.
- A licensed account with read and write access to the target Copilot Studio agent and Power Platform environment.
- Access to the POC's Dataverse, AI Builder, SharePoint, Word Online (Business), Outlook, and Teams resources as required by the current build phase.

Use a consistent local clone location, preferably a path without spaces.

## Setup

1. Clone the repository and open the workspace:

   ```powershell
   git clone <repository-url> nsf-proposal-intake-agent
   Set-Location .\nsf-proposal-intake-agent
   code ".\NSF Proposal Agent.code-workspace"
   ```

2. In Visual Studio Code, sign in through the Copilot Studio extension and confirm that the target environment appears in the Agents pane.

3. Run **Copilot Studio: Reattach Agent** to bind the cloned folder to the existing agent and environment.

4. Confirm the repository starts clean:

   ```powershell
   git status
   ```

5. Validate the deterministic synthetic proposal baseline:

   ```powershell
   .\Docs\Proposals\validate-generation.ps1
   ```

To regenerate the six synthetic DOCX proposals before validation, run:

```powershell
.\Docs\Proposals\generate-proposals.ps1
.\Docs\Proposals\validate-generation.ps1
```

The generation validator runs twice in temporary directories, compares SHA-256 hashes, and validates the generated documents against `expected-results.json`. Review generated changes before committing them.

To regenerate and validate the offline Dataverse seed records, run:

```powershell
.\Docs\Proposals\generate-dataverse-seed.ps1
.\Docs\Proposals\validate-dataverse-seed.ps1
.\Docs\Proposals\validate-planted-scenarios.ps1
```

The seed artifact uses physical `nsf_*` column names and stable manifest identifiers. The planted-scenario validator confirms the three compliance defects, five distinct conflict types, and three eligible reviewers. These scripts do not write to Dataverse; live loading is a separate controlled step.

To upsert the seed into the POC Dataverse environment and verify exact counts and relationships, authenticate Azure CLI as the approved demo owner and run:

```powershell
.\Docs\Proposals\load-dataverse-seed.ps1
```

For a read-only verification after loading, run:

```powershell
.\Docs\Proposals\load-dataverse-seed.ps1 -ValidateOnly
```

The loader is restricted to `galenb@caldova51155962.onmicrosoft.com`, checks the manifest hash and synthetic-only classification, and never uploads SharePoint trigger files.

To create or update and publish the solution-contained POC Demo Proposals view, run:

```powershell
.\Docs\Proposals\deploy-poc-demo-proposals-view.ps1
```

For read-only validation of its columns, active synthetic-proposal filter, ascending Proposal ID sort, and solution membership, run:

```powershell
.\Docs\Proposals\deploy-poc-demo-proposals-view.ps1 -ValidateOnly
```

For presentation, open **Solutions > NSF Proposal Intake > Tables > Proposal > Data**, then select **POC Demo Proposals**. The view shows only active proposal numbers beginning with `265` and excludes historical COI evidence from the presenter list.

To create or update and publish the solution-contained Program Officer Workloads view, run:

```powershell
.\Docs\Proposals\deploy-program-officer-workloads-view.ps1
```

For read-only validation of the view columns, ascending workload sort, active state, and solution membership, run:

```powershell
.\Docs\Proposals\deploy-program-officer-workloads-view.ps1 -ValidateOnly
```

The script is restricted to the approved demo owner. For presentation, open **Solutions > NSF Proposal Intake > Tables > Program Officer > Data**, select **Program Officer Workloads**, and do not use the view designer.

To create or update and publish the solution-contained Reviewer COI Screening view, run:

```powershell
.\Docs\Proposals\deploy-reviewer-coi-screening-view.ps1
```

For read-only validation of all reviewer and COI columns, Reviewer ID sort, active state, and solution membership, run:

```powershell
.\Docs\Proposals\deploy-reviewer-coi-screening-view.ps1 -ValidateOnly
```

For presentation, open **Solutions > NSF Proposal Intake > Tables > Reviewer > Data** and select **Reviewer COI Screening**. Its COI evidence columns are Institution, Collaborators, Advisees, Advisors, and Last Panel Date.

To verify the complete demo baseline without changing it, run:

```powershell
.\Docs\Proposals\reset-demo-state.ps1
```

To remove test artifacts and restore the baseline, run:

```powershell
.\Docs\Proposals\reset-demo-state.ps1 -Apply
```

The reset requires the approved Azure CLI account plus Microsoft Graph `Sites.ReadWrite.All` and `Mail.ReadWrite` scopes. It preserves the six canonical intake files, replaces their proposal content in place so the new-file trigger does not run, removes other intake files and generated documents, deletes only synthetic POC invitation drafts, and restores exact manifest-keyed Dataverse records. It refuses to apply when a canonical intake file is missing.

Before a demonstration, create uniquely named upload copies of proposals `2650004` and `2650001` and open their temporary folder with:

```powershell
.\Docs\Proposals\prep-for-demo.ps1
```

The script reads the canonical repository files without changing them and creates timestamped copies under `%TEMP%\NSF-Proposal-Demo-<timestamp>`.

## Synchronization

Copilot Studio synchronization changes the live agent in the connected environment. Use this sequence for each implementation session:

1. Run **Copilot Studio: Preview changes** at the start of the session and before major edits.
2. Run **Copilot Studio: Get changes** when remote changes are available. Resolve and review those changes locally before continuing.
3. Edit the local agent definitions and keep the VS Code Problems pane clear.
4. Review and commit the local changes to Git.
5. Run **Copilot Studio: Preview changes** again.
6. Run **Copilot Studio: Apply changes** only after the preview is understood and the repository changes are committed.
7. Test the affected behavior in Copilot Studio and record completion evidence in the build tracker.

Do not hand-author generated workflow definitions or connection-reference files. Pull those artifacts with **Copilot Studio: Get changes** after creating or changing flows in Copilot Studio or Power Automate. Files under `.mcs` are local extension metadata and are ignored by Git.

## Publish

**Copilot Studio: Apply changes** updates the live agent but does not publish it.

1. Complete the relevant acceptance checks in [Docs/Design/POC-BUILD.md](Docs/Design/POC-BUILD.md).
2. In the Copilot Studio portal, review and publish the agent.
3. Enable only the approved Microsoft 365 Copilot and Teams demo channels.
4. Restrict access to approved demo users and require Microsoft Entra authentication.
5. Verify that generated notices, invitations, and summaries remain drafts and that reviewer invitations are not sent.

The POC does not use a public web channel. Production deployment requires the security, governance, integration, capacity, and GCC feature-parity work identified in the design.

## Repository Layout

| Path | Purpose |
|---|---|
| `agent.mcs.yml` | Agent metadata and instructions. |
| `settings.mcs.yml` | Agent authentication, orchestration, and AI settings. |
| `topics/` | Copilot Studio topic definitions. |
| `workflows/` | Agent-flow definitions synchronized by the Copilot Studio extension. |
| `Docs/Proposals/solution-workflows/` | Git-owned exports of solution-aware Power Automate trigger and child flows; keep these outside the extension-managed `workflows/` folder. |
| `Docs/Design/` | POC tracker and target design. |
| `Docs/Proposals/` | Synthetic proposal generator, manifest, validator, and generated documents. |
