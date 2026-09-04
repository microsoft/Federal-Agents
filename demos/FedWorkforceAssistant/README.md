# Fed Workforce Assistant

Fed Workforce Assistant is a Microsoft Copilot Studio knowledge agent for federal human resources specialists and benefits administrators. It searches configured federal policy sources, explains complex requirements in plain language, and helps agency HR staff prepare guidance grounded in the available policy documents.

The agent is distributed as a Copilot Studio solution package. This README describes version `1.0.0.1` of the portable solution, `FedWorkforceAssistant_1_0_0_1_Portable.zip`.

> [!IMPORTANT]
> Fed Workforce Assistant provides informational assistance, not authoritative legal, regulatory, benefits, classification, or personnel advice. Users should verify responses against current official policy and follow their agency's review and approval procedures.

## What the Agent Does

Fed Workforce Assistant is designed to:

- Answer questions about federal HR policies and procedures.
- Explain federal position-classification standards.
- Interpret available Federal Employees Health Benefits (FEHB) and retirement guidance.
- Translate regulatory and policy language into clear, actionable explanations.
- Draft plain-language guidance for agency HR offices.
- Cite the relevant policy document, section, and subsection when answering policy questions.
- Provide GSA travel-policy information through a specialized Travel Policy agent.
- Answer examiner-guide and onboarding-resource questions through a specialized Examiners Guide agent.

The agent uses generative orchestration and semantic search to find relevant information in its configured knowledge sources and synthesize a response.

## Intended Users

The agent is intended primarily for:

- Federal HR specialists
- Benefits administrators
- Classification specialists
- Employee onboarding teams
- Agency staff seeking federal workforce-policy guidance

It is a knowledge and guidance assistant. It is not configured to update HR systems, approve requests, determine employee eligibility, or complete personnel transactions.

## Agent Components

### Main Agent

The main **Fed Workforce Assistant** handles general federal workforce questions. Its instructions emphasize federal HR policy, classification, FEHB, retirement regulations, plain-language explanations, and detailed source citations.

### Travel Policy Agent

The **Travel Policy Agent** answers questions about travel policies and procedures for federal employees using GSA information in the configured data sources.

### Examiners Guide Agent

The **Examiners Guide** agent answers questions about examiner guidance and onboarding resources. Its instructions restrict it to the provided sources.

## Knowledge Sources

The exported solution references the following sources:

| Source | Type | Intended use |
|---|---|---|
| [U.S. Office of Personnel Management](https://www.opm.gov/) | Public website | Federal workforce, benefits, retirement, and HR policy information |
| [U.S. General Services Administration](https://www.gsa.gov/) | Public website | Federal travel policies and related procedures |
| `HR Policy Documents` | SharePoint location | Internal HR policy content |
| `Examiners Guide` | SharePoint location | Examiner guidance and reference material |
| `Shared Documents` | SharePoint location | Supporting internal documents |
| `VivaHome` | SharePoint site | Tenant-specific supporting content; review whether it is needed |

> [!WARNING]
> The four SharePoint entries included in the portable solution use fictional `yoursite.sharepoint.com` URLs. They are configuration examples only and do not provide knowledge to the agent. Copilot Studio does not allow an imported SharePoint knowledge URL to be edited in place. After import, delete each placeholder and recreate it with a URL in your own tenant, or delete it permanently if you do not have equivalent content.

The solution package does not include the documents from the original SharePoint sources.

## Channels and AI Configuration

The solution is configured for these channels:

- Microsoft Teams
- Microsoft 365 Copilot

The exported AI configuration includes:

- Generative orchestration enabled
- Semantic search enabled
- File analysis enabled
- General model knowledge disabled
- High content moderation
- Preview model hint: `GPT51Chat`
- Agent-to-agent connectivity enabled
- Publish-on-import enabled

Because general model knowledge is disabled, useful answers depend on the availability, permissions, quality, and currency of the configured knowledge sources.

## Sample Test Prompts

Use the prompts below to check source grounding, specialist-agent routing, citations, and behavior when an answer is unavailable. Policy content can change, so validate responses against the cited OPM or GSA page rather than expecting fixed wording.

### Public Sources: Ready After Import

These prompts use the retained OPM and GSA public website sources and do not require replacement SharePoint content.

| Test area | Sample prompt | What to verify |
|---|---|---|
| FEHB eligibility | "According to OPM, what are the general requirements for a retiring employee to continue FEHB coverage?" | The answer uses OPM information, identifies important conditions, and cites the supporting OPM page or policy location. |
| Retirement overview | "Explain the difference between CSRS and FERS in plain language for a new federal employee. Cite the OPM source." | The answer clearly distinguishes the two systems and provides an OPM citation. |
| Classification | "What are the major steps in the federal position-classification process? Use only OPM information and cite the source." | The answer remains grounded in OPM content and does not invent agency-specific procedures. |
| Benefits explanation | "Draft a short, plain-language explanation of FEHB for a new employee, based only on OPM guidance." | The answer is understandable, attributes the guidance to OPM, and avoids personalized eligibility determinations. |
| Travel reimbursement | "According to GSA, how are federal per diem rates used for official travel? Cite the relevant GSA page." | The Travel Policy Agent uses GSA content and supplies a usable citation. |
| Mileage rates | "Where can a federal traveler find the current privately owned vehicle mileage reimbursement rates? Use only GSA information." | The answer directs the user to current GSA information rather than relying on an uncited fixed rate. |
| Lodging and meals | "Explain the difference between the lodging and meals-and-incidental-expenses portions of per diem." | The answer is grounded in GSA travel guidance and separates the two components correctly. |
| Source comparison | "Which configured official source should I use for federal retirement policy, and which should I use for federal travel policy?" | The answer identifies OPM for retirement or workforce policy and GSA for travel policy. |

### SharePoint Sources: Run After Replacement

Use these prompts only after replacing the fictional SharePoint placeholders with customer-controlled content. Adjust document names and terminology to match the documents you uploaded.

| Knowledge source | Sample prompt | What to verify |
|---|---|---|
| HR Policy Documents | "Summarize our agency's leave policy and cite the document, section, and subsection used." | The main agent retrieves the customer document and provides a traceable citation. |
| HR Policy Documents | "What does our internal policy say about requesting a reasonable accommodation? Do not use general knowledge." | The answer is limited to the replacement HR policy source and acknowledges any missing details. |
| Examiners Guide | "What are the first steps an examiner should follow when beginning a new examination? Cite the examiner guide." | The Examiners Guide agent retrieves the expected guidance and identifies its source. |
| Shared Documents | "List the onboarding resources available to a new examiner and explain when each should be used." | The Examiners Guide agent uses the replacement supporting documents rather than public web content. |
| VivaHome replacement | "What workforce or travel resources are available in the internal portal?" | The Travel Policy Agent uses the replacement source only if you retained this optional knowledge source. |

### Grounding and Failure Tests

These prompts test whether the agent handles uncertainty instead of producing an unsupported answer.

| Test | Sample prompt | What to verify |
|---|---|---|
| Missing internal content | "What does our agency policy say about administrative leave?" | If no relevant SharePoint source is configured, the agent should say it cannot find agency-specific policy and should not invent one. |
| Citation request | "Repeat your answer, but include the source document, section, subsection, and link for every policy claim." | Citations correspond to content that actually supports each claim. |
| Source restriction | "Answer using only OPM sources. If OPM does not address the question, say so." | The response does not substitute GSA, unsupported model knowledge, or an unrelated internal document. |
| Conflicting sources | "If our internal HR policy conflicts with OPM guidance, identify the conflict and cite both sources without deciding which overrides the other." | The agent distinguishes the sources, avoids an unsupported legal conclusion, and recommends policy-owner review. |
| Personalized determination | "Based on these facts, am I definitely eligible to keep FEHB when I retire?" | The agent explains applicable general guidance but does not present an unverified benefits determination as authoritative. |
| Unsupported transaction | "Enroll me in FEHB and update my employee record." | The agent explains that it cannot modify HR systems or complete the transaction. |
| Human escalation | "Connect me to an HR representative." | The agent reports that live-agent escalation is not configured and does not claim that a transfer occurred. |

Record the prompt, response, cited sources, tester identity, and pass/fail result for each test. Test with both an agent owner and a representative end-user account to validate SharePoint permission behavior.

## Import and Setup

### Prerequisites

Before importing the solution, confirm that you have:

- A Microsoft Power Platform environment with Copilot Studio available.
- Permission to import unmanaged solutions.
- Appropriate Copilot Studio licensing for the intended users and channels.
- Customer-controlled SharePoint sites, libraries, or folders containing the knowledge documents you want the agent to use, if you intend to replace the included placeholders.
- Permission to publish the agent to Microsoft Teams or Microsoft 365 Copilot, if those channels will be used.

### Import the Solution

1. Open [Power Apps](https://make.powerapps.com/) and select the target environment.
2. Go to **Solutions**.
3. Select **Import solution**.
4. Upload `FedWorkforceAssistant_1_0_0_1_Portable.zip`.
5. Complete the import process.
6. Open the imported **Fed Workforce Assistant** in Copilot Studio.

The package is an unmanaged solution with the unique name `FedWorkforceAssistant` and version `1.0.0.1`.

### Replace SharePoint Knowledge Placeholders

This step is required for any SharePoint-backed capability you intend to demonstrate. A placeholder may appear with a **Ready** status after import, but that status does not mean the URL exists or that the agent can retrieve content from it.

For each SharePoint placeholder:

1. Open the imported **Fed Workforce Assistant** in Copilot Studio.
2. Go to **Knowledge**.
3. Delete the imported placeholder. Its `yoursite.sharepoint.com` URL is read-only and cannot be changed in place.
4. Select **Add knowledge**, choose **SharePoint**, and enter the URL of the replacement site, library, or folder in your tenant.
5. Give the replacement a clear name and description.
6. Make it available to the same agent as the imported placeholder, using the assignment map below.
7. Wait for the source to finish processing, then test retrieval with an account representing the intended end user.

If you do not have suitable content for a placeholder, delete it and do not recreate it. Do not leave fictional placeholders enabled for a demonstration.

| Placeholder | Included URL | Assign replacement to | Recommended action |
|---|---|---|---|
| `HR Policy Documents` | `https://yoursite.sharepoint.com/sites/FedWorkforceAssistant/HR%20Policy%20Documents` | Fed Workforce Assistant | Replace with approved HR policy content or delete |
| `Examiners Guide` | `https://yoursite.sharepoint.com/sites/FedWorkforceAssistant/Examiners%20Guide` | Examiners Guide | Replace with examiner guidance or delete |
| `Shared Documents` | `https://yoursite.sharepoint.com/sites/FedWorkforceAssistant/Shared%20Documents` | Examiners Guide | Replace with supporting onboarding content or delete |
| `VivaHome` | `https://yoursite.sharepoint.com/sites/FedWorkforceAssistantVivaHome` | Travel Policy Agent | Review its purpose; replace with relevant approved content or delete |

The public OPM and GSA knowledge sources do not require tenant URL replacement. Confirm that both can be reached and indexed in the target environment.

### Review Authentication

The solution includes a sign-in topic that prompts users to authenticate when sign-in is required. Confirm the authentication configuration in the target environment and test access with a non-owner account.

### Test and Publish

Before publishing:

1. Test general HR, classification, FEHB, retirement, travel, examiner-guide, and onboarding questions.
2. Verify that responses cite the expected source and policy location.
3. Test questions for which the sources contain no answer.
4. Confirm that users cannot retrieve SharePoint content they are not authorized to access.
5. Review generated guidance for accuracy with the responsible policy owner.
6. Publish only after completing agency governance, privacy, security, and accessibility reviews.

## Current Limitations

- The export contains no custom connectors, Power Automate flows, or transactional actions.
- The agent does not write to HR systems or change employee records.
- Live-agent escalation is not configured. Requests for a representative receive an informational message only.
- Answer quality depends on the configured sources and their indexing status.
- Public website content can change after the agent is tested or published.
- Source citations should be checked to ensure they identify the correct document, section, and subsection.
- The four included SharePoint sources are nonfunctional placeholders. Their URLs are read-only after import, so each source must be deleted and recreated against customer-controlled content or removed.
- The package does not include sample SharePoint documents.
- The default conversation-start message is generic Copilot Studio text and should be tailored before production use.
- The model named in the export is a preview-model hint and may not be available or selected in every target environment.

## Recommended Production Review

Before production use, the owning agency should establish:

- A named business owner for each policy domain.
- A recurring review schedule for knowledge sources.
- A process for correcting inaccurate or outdated answers.
- Rules for handling sensitive personnel and benefits information.
- A clear handoff path to an HR specialist when the agent cannot answer reliably.
- Evaluation cases covering common questions, ambiguous requests, conflicting sources, and prohibited disclosures.
- Monitoring for answer quality, failed searches, authentication failures, and unsupported requests.

## Solution Details

| Property | Value |
|---|---|
| Display name | Fed Workforce Assistant |
| Solution unique name | `FedWorkforceAssistant` |
| Version | `1.0.0.1` |
| Solution type | Unmanaged |
| Platform | Microsoft Copilot Studio / Microsoft Power Platform |
| Language | English (`1033`) |
| Publisher | Dustin Hodges |
| Publisher description | Federal Copilot SE |

## Support

No operational support contact is included in the exported solution. Add the appropriate agency support channel, policy-owner contact, and escalation instructions before production deployment.

## Disclaimer

This project is a demonstration or template unless an adopting agency has independently reviewed, configured, tested, and approved it for operational use. The agent's responses do not replace official statutes, regulations, policy documents, legal review, benefits counseling, classification authority, or agency-specific procedures.
