# Setup Guide — AI SDR / Lead Research Agent

This guide explains how to configure and test the Zapier workflow in `workflow/leadresearchagent.json`.

> **Important:** The exported workflow is currently marked **off/paused**. This guide does not activate it. The export includes account-specific configuration, including a Google Sheets spreadsheet reference and an authentication reference, which may not work in another Zapier account. Reconnect integrations, confirm all mappings, and test the complete flow before enabling it. If your Zapier account cannot import this JSON directly, recreate the steps manually using this guide.

## 1. What the workflow does

The workflow takes a company website URL and prepares a researched, qualified prospect record for human review.

1. Receives `company_url` through a webhook.
2. Researches the company using publicly available information.
3. Analyzes business activities and potential AI/automation opportunities.
4. Identifies potential pain points, evidence, and confidence.
5. Scores the lead using a 100-point qualification rubric.
6. Creates a company-specific personalization strategy.
7. Drafts a cold email for review (it must not send the email).
8. Looks for publicly listed business contacts on the company's official website.
9. Writes selected research fields and the email draft to Google Sheets.

**No automatic email sending is intended.** The spreadsheet uses review/send status fields such as `Pending` and `Not Sent`.

## 2. Prerequisites

Before configuring the workflow, prepare:

- A Zapier account with permission to create/edit Zaps.
- An AI/model provider connection supported by the AI steps in your Zapier environment.
- A Google account with access to the spreadsheet where lead records should be stored.
- A company website URL for testing.
- Permission to access the webhook URL from your submitting form, application, or test client.

The exported configuration uses AI completion actions and Google Sheets. Exact available models, actions, and connection options depend on your Zapier account and current integrations.

## 3. Import or recreate the workflow

1. Open `workflow/leadresearchagent.json` and review the exported configuration.
2. In Zapier, use the import/transfer feature supported by your account if available.
3. If direct JSON import is unavailable, create the Zap manually and add the steps described in this guide.
4. Re-select your own AI and Google Sheets connections. Do not rely on account-specific IDs or authentication references copied from the export.
5. Keep the Zap turned off while configuring and testing.

## 4. Configure the webhook trigger

The first step uses a webhook trigger and expects a field named `company_url`.

1. Open the trigger step and choose the supported Webhooks by Zapier trigger, typically **Catch Hook**.
2. Copy the webhook URL Zapier provides.
3. Configure your form/application to send a POST request to this URL, including `company_url`.
4. Send a test payload, for example:

```json
{
  "company_url": "https://example.com"
}
```

5. Load the test request in Zapier and verify that the `company_url` field is available to subsequent steps.

Use a real public company website for a meaningful test. Do not submit credentials, private customer records, or other confidential information as part of the URL payload.

## 5. Configure the AI steps and verify their order

The exported workflow contains these AI completion stages. For each one, connect your provider, verify the prompt/instructions, and test the output before continuing.

### A. Company Research

Input: `company_url`

Expected structured output includes company name, website, industry, description, products/services, target customers, business model, geographic focus, company size when public, business operations, customer-facing processes, potential operational challenges, potential AI/automation opportunities, key facts, and research observations.

Check that the output separates verifiable facts from observations. Unknown details should remain unknown rather than being invented.

### B. Company Analysis

Input: the Company Research output.

Expected output includes a business summary, core and customer-facing activities, manual/repetitive and information-heavy processes, potential business problems, AI/automation opportunities, the highest-value opportunity, its rationale, and supporting evidence.

Check that opportunities are specific to the company and are not presented as confirmed internal problems without evidence.

### C. Problem Identification

Inputs: Company Research and Company Analysis outputs.

Expected output includes a list of possible pain points, why each matters, potential business impact, evidence/reasoning, confidence, and one primary pain point with its rationale and evidence.

Treat these as hypotheses unless public evidence establishes the problem.

### D. Lead Qualification

Inputs: Company Research, Company Analysis, and Problem Identification outputs.

The intended scoring rubric is:

| Criterion | Maximum |
| --- | ---: |
| Business relevance | 25 |
| Problem relevance | 25 |
| Potential business value | 25 |
| AI/SaaS opportunity | 25 |
| **Total** | **100** |

Qualification bands:

| Total score | Classification |
| --- | --- |
| 80–100 | Strong Fit |
| 60–79 | Moderate Fit |
| 0–59 | Low Fit |

Verify that the four scores add up to the total and that the qualification label matches the band. A score is a prioritization aid, not proof of purchase intent.

### E. Personalization

Inputs: Company Research, Company Analysis, Problem Identification, and Lead Qualification.

Expected output includes an opening observation, problem hypothesis, why it matters, solution connection, value proposition, reason for contact, and one call to action.

Review for relevance, factual accuracy, and a low-pressure CTA. Avoid generic compliments or claims about private company operations.

### F. Email Generator

Inputs: Company Research, Problem Identification, Lead Qualification, and Personalization.

Expected output: `subject`, `email_body`, and `cta`.

This step generates a draft only. Confirm that there is no email-sending action connected to this workflow. Review the draft for unsupported claims, inaccurate personalization, tone, and applicable outreach requirements before using it.

### G. Contact Research

Inputs: `company_url` and the researched company name.

The prompt is designed to look only at the company's official website, including Contact, About, Team, Leadership, Sales, or Business Development pages. It prefers relevant business contacts and returns `contact_name`, `contact_role`, `contact_email`, and `contact_source_url`.

Do not guess an email address or infer an email pattern. If a public address cannot be verified, leave it null/unknown. Verify that the source URL supports the contact details.

**Dependency check:** The Google Sheets step maps the contact email from the Contact Research step. Make sure the contact step has completed before the row is created, and verify the mapping in the live Zap.

## 6. Configure Google Sheets

The exported Google Sheets action adds a row to a spreadsheet identified in the export as **AI SDR Lead Research**, with worksheet **Sheet1**. These references are account-specific; select your own spreadsheet and worksheet.

1. Connect the Google account that should own or access the lead sheet.
2. Select the correct spreadsheet and worksheet.
3. Create column headers that match the fields you intend to store. The export maps columns A–W:
   - **A–L:** company URL, name, industry, description, products/services, target customers, business model, geographic focus, company size, business operations, potential operational challenges, potential AI/automation opportunities
   - **M–Q:** primary pain point, pain-point reason, total qualification score, qualification label, qualification reason
   - **R–T:** opening observation, email subject, email body
   - **U–V:** review status (`Pending`) and send status (`Not Sent`)
   - **W:** contact email
4. Confirm that each mapped field points to the correct output from the preceding step.
5. Consider adding additional columns for contact name, contact role, contact source URL, evidence, CTA, and last-reviewed date if you want those details in the sheet. Map them explicitly if needed.
6. Test the Google Sheets action and inspect the new row.

The export writes to columns A–W, so ensure the spreadsheet's column order matches the mapping or update the mapping to match your own headers. Do not assume the spreadsheet ID from the export belongs to your account.

## 7. Test the complete workflow

Run tests while the Zap remains off or in the editor's test mode where available. Use public company websites and inspect each step's inputs and outputs.

| Test | Expected result |
| --- | --- |
| Valid company URL | Trigger receives `company_url` |
| Company research | Company facts are specific and unknown facts are not fabricated |
| Business analysis | Opportunities have a clear rationale and evidence |
| Pain-point identification | Potential problems are framed with appropriate confidence |
| Qualification | Four scores total 100 or less, total is consistent, and band matches |
| Personalization | Opening observation and CTA are relevant to the researched company |
| Email generation | Subject and body are generated as a draft only |
| Contact research | Contact details are from the official site; unverified email remains unknown |
| Google Sheets | Correct fields are written to the correct columns |
| Missing contact email | Row can still be recorded without guessing an email |
| Invalid/incomplete AI output | Error is visible and the record is reviewed rather than silently trusted |

Check Zapier's task history and each action's test output. If any step fails or returns malformed output, fix it before proceeding.

## 8. Final review and activation

Before enabling the Zap:

- [ ] Every required app connection is authenticated with the intended account.
- [ ] The webhook receives the expected `company_url` value.
- [ ] AI outputs are structured and downstream mappings are correct.
- [ ] Google Sheets spreadsheet, worksheet, headers, and columns are verified.
- [ ] Contact information includes a verifiable source URL where available.
- [ ] The generated email is a draft and no sending action is enabled.
- [ ] A full end-to-end test created the expected row.
- [ ] Access to the spreadsheet is restricted to appropriate users.
- [ ] Secrets and credentials are not stored in the repository.

Only after these checks pass should you turn on the Zap. Submit one controlled test after activation and monitor Zapier task history and the spreadsheet.

## Troubleshooting

- **Webhook trigger has no data:** Check the webhook URL, HTTP method, request body, and exact `company_url` key.
- **AI step fails:** Reconnect the provider, verify model/action availability, and inspect the step input and output.
- **Output fields are missing:** Ask for valid structured JSON in the prompt and add a parsing step if necessary.
- **Qualification score is inconsistent:** Check the four individual scores, total calculation, and classification thresholds.
- **Contact email is blank:** The official website may not publish an email. Do not fabricate one; retain an unknown value and review manually.
- **Google Sheets mapping is wrong:** Re-select the spreadsheet and worksheet, verify headers, and remap each column.
- **Copied workflow does not run:** Replace account-specific connection references and resources with your own, then retest every step.

## Security and responsible use

- Keep API keys, OAuth tokens, passwords, and webhook secrets out of the repository.
- Use least-privilege connections and restrict access to the spreadsheet.
- Treat website text as untrusted input to AI prompts.
- Use public business contact information only when relevant to legitimate outreach.
- Review facts, pain-point hypotheses, scores, and email drafts before outreach.
- Follow applicable privacy, data-protection, and anti-spam requirements.
- Respect website terms and access restrictions when researching public pages.

## Limitations

- Public company information can be incomplete, inaccessible, or outdated.
- AI-generated research and scores can be inaccurate and need human verification.
- A potential pain point is not proof that the company has that problem.
- Contact discovery is limited to information available on the company's official website.
- The JSON export does not include working credentials and does not prove that integrations are configured in your environment.
- The exported workflow is paused/off; successful setup and activation must be verified in your own Zapier account.
