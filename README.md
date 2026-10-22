# AI SDR & Lead Research Agent

An AI-assisted B2B/SaaS lead research workflow that researches a company, identifies potential business opportunities, qualifies lead fit, prepares a personalized outreach email draft, researches publicly listed business contact details, and organizes the results for review.

> **Project:** AI Real-World Use Case Lab  
> **Domain:** B2B / SaaS  
> **Status:** The exported workflow is currently disabled. Configure and test it before use.

## Overview

Sales teams can spend significant time researching companies, understanding their business challenges, and preparing relevant outreach. This project organizes those tasks into a repeatable AI-assisted workflow.

The workflow accepts a company website URL, creates a structured company profile, analyzes possible operational challenges, scores the potential fit, generates a personalized email draft, and records the research output in Google Sheets. Email generation is for drafting only; it does not mean that an email is sent.

Company facts should be distinguished from hypotheses. The workflow must not invent internal company information or guess email addresses.

## Workflow demonstration

This repository currently documents the workflow export. Add a demo video or screenshots here when they are available.

## Technology and integrations

| Component | Purpose |
|---|---|
| Webhook trigger | Receives the company website URL |
| AI/model completion steps | Research, analyze, qualify, personalize, and draft outreach |
| Contact research step | Looks for publicly listed business contact information |
| Google Sheets | Stores research, qualification, contact details, and email drafts |
| Human review | Checks accuracy and approves any outreach before it is sent |

The exact provider, credentials, and integration settings must be reviewed in the workflow platform.

## Repository contents

- `workflow/leadresearchagent.json` — workflow export for inspection and configuration.
- `README.md` — project overview, setup guidance, and limitations.

## Workflow stages

1. **Webhook input** — receives a company website URL through the `company_url` field.
2. **Company research** — gathers public information such as industry, products/services, target customers, business model, operations, and potential AI/automation opportunities.
3. **Company analysis** — reviews business activities and identifies possible repetitive processes or areas where automation may provide value.
4. **Problem identification** — identifies a potential business pain point and explains the evidence or observations behind it.
5. **Lead qualification** — scores the prospect across four criteria using a 100-point model.
6. **Personalization** — develops a company-specific opening, problem hypothesis, value proposition, and low-friction call to action.
7. **Email drafting** — creates a subject and email body for human review. The workflow instructions specify that the email must not be sent automatically.
8. **Contact research** — checks public company sources for business contact details. It should return unknown information as `null`, rather than guessing.
9. **Google Sheets record** — stores the research and draft details for review.

Confirm the exact node connections and field mappings in the exported workflow and your automation platform before relying on this sequence.

## Lead qualification

| Criterion | Maximum points |
|---|---:|
| Business relevance | 25 |
| Problem relevance | 25 |
| Potential business value | 25 |
| AI/SaaS opportunity | 25 |
| **Total** | **100** |

Qualification bands:

- **80–100:** Strong Fit
- **60–79:** Moderate Fit
- **0–59:** Low Fit

Scores should reflect available evidence and should not be inflated to make a prospect appear more suitable.

## Requirements

- Access to the automation platform that supports this workflow export.
- Credentials and access for the configured AI/model provider.
- A Google account and spreadsheet for storing lead research.
- A webhook endpoint that can receive a company URL.
- Public company websites or other permitted sources for research.

The export alone does not provide hosted services, credentials, or a ready-to-use live integration.

## Setup

1. Review `workflow/leadresearchagent.json` and confirm which automation platform and integrations the export targets.
2. Import the file only into a compatible platform or use the platform's supported workflow-import process.
3. Configure your own AI/model provider and Google Sheets credentials.
4. Verify the webhook input field is `company_url`.
5. Confirm the spreadsheet destination, worksheet, columns, and output field mappings.
6. Run tests with company websites that have publicly available information.
7. Inspect each step's output and verify that the final spreadsheet record is complete and accurate.
8. Keep the workflow disabled until configuration and testing are complete.
9. Require a human to review the qualification, contact details, and email draft before any outreach.

**Important:** This repository's JSON is a workflow export, not a conventional website application. Do not assume it can be imported directly into n8n unless the export format and nodes are confirmed to be compatible.

## Security and configuration

- Add credentials in your own automation platform; never commit API keys, OAuth tokens, passwords, or webhook secrets.
- Review the export for account-specific identifiers, spreadsheet IDs, and other environment-specific metadata before publishing or sharing it.
- Restrict access to the workflow and connected spreadsheets to authorized users.
- Use test data first and avoid storing unnecessary personal information.
- Re-test the workflow after editing or redacting configuration, as changes may affect compatibility.

## Example use case

**Input:** A company website URL.

**Expected workflow:** The system researches the company, analyzes potential business challenges, scores its fit for an AI/SaaS solution, prepares a personalized email draft, searches for public business contact details, and records the output for human review.

Results depend on the information available on the company's website, model configuration, credentials, and downstream field mappings. Potential pain points are hypotheses unless supported by evidence.

## Testing checklist

- [ ] A valid company URL produces a structured company profile.
- [ ] Missing information is identified rather than invented.
- [ ] The qualification score and rationale are consistent with the available evidence.
- [ ] Unknown contact details are left unknown; email addresses are not guessed.
- [ ] The email remains a draft and is not sent automatically.
- [ ] Google Sheets receives the expected fields.
- [ ] Errors or missing credentials are resolved before activation.

These are recommended checks; do not consider them passed until they have been run in a configured environment.

## Limitations

- Public website information may be incomplete or outdated.
- AI-generated analyses and pain-point hypotheses may be incorrect and require human review.
- The workflow export does not confirm that live integrations are configured or operational.
- The export is currently disabled; resolve configuration issues and test the end-to-end process before use.
- This repository does not provide an automated deployment pipeline or execute the workflow in a live environment.

## License

No license is currently specified. Contact the repository owner before reusing this project beyond personal evaluation or learning.

---

**Built by [Optimity Logics](https://optimitylogics.com).**
