# B2B/SaaS — AI SDR / Lead Research Agent

An AI-assisted sales development workflow built with **Zapier** to research B2B companies, identify potential business opportunities, qualify prospects, find publicly listed business contacts, and prepare personalized cold-email drafts for human review.

> **Workflow status:** Disabled in the provided export. Configure and test your own integrations before enabling it.
>
> **Important:** This project generates research and email drafts. It does not send emails automatically.

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Workflow Architecture](#workflow-architecture)
- [Lead Qualification](#lead-qualification)
- [Technology and Integrations](#technology-and-integrations)
- [Repository Structure](#repository-structure)
- [Setup and Configuration](#setup-and-configuration)
- [Input and Outputs](#input-and-outputs)
- [Testing Checklist](#testing-checklist)
- [Security and Responsible Use](#security-and-responsible-use)
- [Limitations](#limitations)

## Overview

Researching prospects manually can take time and make outbound sales preparation difficult to scale. This workflow brings several research and preparation tasks into one repeatable process.

Provide a company website URL, and the workflow uses AI steps to build a company profile, analyze possible operational opportunities, identify a potential pain point, score prospect fit, and prepare an outreach strategy and email draft. It also looks for business contact details published on the company's official website and records selected results in Google Sheets.

The workflow is designed to support sales research—not to make unsupported claims about a company's internal operations. Facts, observations, and hypotheses should be reviewed before being used in outreach.

## Key Features

- **Company research:** Summarizes the company's business, industry, products or services, target customers, business model, geographic focus, and publicly available company information.
- **Business analysis:** Identifies relevant workflows and potential areas for process improvement or automation.
- **Pain-point identification:** Selects a primary potential business challenge and records the supporting evidence or reasoning.
- **Lead qualification:** Scores prospect fit using a 100-point framework.
- **Outreach personalization:** Creates a company-specific opening observation, problem hypothesis, value proposition, and call to action.
- **Email drafting:** Produces a concise cold-email subject and body for review.
- **Contact research:** Looks for relevant business contacts and email addresses published on the company's official website. Unknown details should remain unknown rather than being guessed.
- **Google Sheets logging:** Saves selected research fields, qualification details, contact information, and email drafts for review.

## Workflow Architecture

The exported workflow contains the following logical stages:

1. **Webhook trigger** — receives the company website URL through the `company_url` input.
2. **Company Research** — creates a structured company profile from public information.
3. **Company Analysis** — reviews the company's activities and potential business or automation opportunities.
4. **Problem Identification** — identifies possible pain points, explains why they may matter, and records evidence and confidence.
5. **Lead Qualification** — scores business relevance, problem relevance, potential business value, and AI/SaaS opportunity.
6. **Personalization** — develops the company-specific messaging strategy for an outbound email.
7. **Email Generator** — drafts a subject line, email body, and call to action. The instructions explicitly prohibit sending the email.
8. **Contact Research** — checks relevant pages on the company's official website for publicly listed contact information.
9. **Google Sheets** — adds a row containing selected company research, qualification results, the email draft, and a review/send status.

The exported configuration contains separate workflow nodes and field mappings. Verify the live Zap's step order, dependencies, and field mappings in Zapier before relying on this description as an execution guarantee.

## Lead Qualification

The workflow uses four equally weighted criteria:

| Criterion | Maximum score |
|---|---:|
| Business relevance | 25 |
| Problem relevance | 25 |
| Potential business value | 25 |
| AI/SaaS opportunity | 25 |
| **Total** | **100** |

Qualification bands:

| Score | Classification |
|---|---|
| 80–100 | Strong Fit |
| 60–79 | Moderate Fit |
| 0–59 | Low Fit |

Scores should be evidence-based. A high score is not proof that a company has a particular problem or is ready to buy.

## Technology and Integrations

| Component | Role |
|---|---|
| Zapier workflow | Orchestrates the trigger and connected steps |
| Webhooks | Receives the company URL |
| AI/model steps | Research, analysis, pain-point identification, qualification, personalization, and email drafting |
| Google Sheets | Stores selected outputs for review |
| Human review | Validates research and approves any subsequent outreach |

The export references AI completion actions and a Google Sheets row-creation action. Working credentials, permissions, and provider configuration must be supplied in your own Zapier environment.

## Repository Structure

```text
AI-SDR-Lead-Research-Agent/
├── README.md
└── workflow/
    └── leadresearchagent.json
```

- `README.md` — project overview, workflow documentation, setup guidance, and limitations.
- `workflow/leadresearchagent.json` — exported workflow configuration for inspection and compatible import.

## Setup and Configuration

1. **Review the export.** Open `workflow/leadresearchagent.json` and confirm that it is the expected Zapier workflow export.
2. **Import or recreate the workflow.** Use the import or transfer process supported by your Zapier account. If direct import is unavailable, recreate the steps and mappings in the Zap editor.
3. **Configure the trigger.** Confirm that the workflow receives a valid company website URL in `company_url`.
4. **Connect your accounts.** Add your own AI/model provider and Google Sheets connections in Zapier. Do not reuse credentials from another environment.
5. **Check Google Sheets.** Select the intended spreadsheet and worksheet, and confirm the columns match the exported field mappings.
6. **Validate field mappings.** Ensure each AI step receives the previous step's expected outputs, including company research, analysis, primary pain point, qualification, and personalization.
7. **Review contact research.** Confirm it uses public information from the company's official website and returns `null` when a contact detail cannot be verified.
8. **Test with a sample company.** Inspect every step's input and output, and verify that the Google Sheets row contains the expected values.
9. **Keep the workflow disabled during testing.** Enable it only after resolving configuration errors and confirming the complete flow works as intended.

The supplied export reports the workflow status as `off` and its nodes as paused. Treat it as a configuration artifact, not as proof of a live, operational automation.

## Input and Outputs

### Input

| Field | Description |
|---|---|
| `company_url` | Website URL of the company to research |

### Main outputs

The workflow produces structured information in these categories:

- **Company profile:** name, website, industry, description, products/services, target customers, business model, geographic focus, company size when public, operations, key facts, and research observations.
- **Business analysis:** core activities, customer-facing activities, manual or repetitive processes, potential business problems, automation opportunities, and the highest-value opportunity.
- **Pain points:** possible problems, why they matter, potential impact, evidence, confidence, and one primary pain point.
- **Qualification:** individual criterion scores, total score, fit classification, rationale, and recommended sales angle.
- **Personalization:** opening observation, problem hypothesis, solution connection, value proposition, reason for contact, and call to action.
- **Email draft:** subject, email body, and CTA.
- **Contact details:** contact name, role, publicly listed email, and source URL.

The Google Sheets step maps selected fields into a row and includes status values such as `Pending` and `Not Sent`. Review the actual spreadsheet headers and mappings in your Zap before using the data operationally.

## Testing Checklist

- [ ] A valid company URL triggers the workflow.
- [ ] The company profile contains specific, traceable public information.
- [ ] Missing facts are left unknown rather than invented.
- [ ] Potential problems are clearly framed as hypotheses unless evidence confirms them.
- [ ] Qualification scores add up correctly and match the stated scoring rubric.
- [ ] The contact email is publicly listed and its source URL is recorded where available.
- [ ] Unknown contact details are not guessed.
- [ ] The generated email is concise, relevant, and contains one clear CTA.
- [ ] No email-sending action is enabled as part of this workflow.
- [ ] Google Sheets receives the expected fields in the correct columns.
- [ ] Errors, permissions, and missing credentials are resolved before activation.

These are recommended checks; they should only be marked complete after testing in your own configured environment.

## Security and Responsible Use

- Store API keys, OAuth tokens, passwords, and webhook secrets in Zapier's connection or secrets settings—not in this repository.
- Review exported configuration for account-specific identifiers, spreadsheet IDs, and other environment-specific metadata before sharing it publicly.
- Restrict workflow and spreadsheet access to authorized users.
- Collect only business contact information that is publicly available and relevant to legitimate outreach.
- Do not infer or fabricate email addresses. Preserve source URLs so contact details can be checked.
- Have a person verify company facts, pain-point hypotheses, qualification results, and email drafts before outreach.
- Follow applicable privacy, anti-spam, and data-protection requirements in the jurisdictions where you operate.

## Limitations

- The workflow depends on public information, which may be incomplete, inaccessible, or outdated.
- AI outputs can be inaccurate and require human verification.
- A potential pain point or automation opportunity is not proof of an actual internal company problem.
- Contact research is limited to information the workflow can access and verify on the official company website.
- The JSON export does not include your live credentials and does not prove that all integrations are connected or functioning.
- The exported workflow is disabled; successful end-to-end operation must be verified in Zapier.

## License

No license is specified in this repository. Unless a license is added, do not assume that the project may be reused, redistributed, or modified under an open-source license.

---

Built by [Optimity Logics](https://optimitylogics.com).
