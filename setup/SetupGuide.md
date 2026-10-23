# Setup Guide — AI SDR / Lead Research Agent

This is the **hands-on installation and configuration guide** for the Zapier workflow in [`workflow/leadresearchagent.json`](../workflow/leadresearchagent.json). It focuses on setting up your own connections, mapping fields, testing the webhook, and validating the Google Sheets output. For project purpose and feature overview, see the [README](../README.md).

> **Important:** The exported workflow is marked `off` / paused and contains account-specific IDs. Do not assume that its original Google Sheets spreadsheet, authentication references, or internal step IDs will work in your Zapier account. Keep your Zap off until all steps pass testing. This workflow prepares research and email drafts; it must not send emails automatically.

## 1. Before you start

Have these ready:

- A Zapier account with permission to create and edit Zaps.
- Access to the AI provider/model supported by the AI steps available in your Zapier account.
- A Google account that can create or edit a Google spreadsheet.
- A publicly accessible company website URL for testing.
- A way to send an HTTP POST request (for example, a terminal with `curl`, Postman, or your own form/application).

The exported AI actions use Zapier's AI completion action and the model selector `standard/auto`. Your account may show different provider/model choices. Select an available model and reconnect the account when prompted.

## 2. Get the workflow JSON

1. Open [`workflow/leadresearchagent.json`](../workflow/leadresearchagent.json) in this repository.
2. Download the raw JSON file using GitHub's **Raw** view or save the file locally.
3. Keep a copy of the original export so you can refer to the intended prompts and field mappings while configuring Zapier.

**Import note:** Use a JSON import/transfer option only if it is available and supported in your Zapier account. If Zapier does not accept this export format, build the Zap manually using the steps below. A downloaded workflow export is not the same as a fully connected, ready-to-run Zap.

## 3. Create the Google Sheets destination

1. In Google Sheets, create a spreadsheet named **AI SDR Lead Research** (or choose another name).
2. Create a worksheet named **Sheet1** (or use another worksheet and select it in the Zap action).
3. Put these headers in row 1, in this exact order, because the exported action writes to columns A–W:

| Column | Header | Value comes from |
| --- | --- | --- |
| A | Company URL | Webhook `company_url` |
| B | Company Name | Company Research |
| C | Industry | Company Research |
| D | Company Description | Company Research |
| E | Products / Services | Company Research |
| F | Target Customers | Company Research |
| G | Business Model | Company Research |
| H | Geographic Focus | Company Research |
| I | Company Size | Company Research |
| J | Business Operations | Company Research |
| K | Potential Operational Challenges | Company Research |
| L | Potential AI / Automation Opportunities | Company Research |
| M | Primary Pain Point | Problem Identification |
| N | Primary Pain Point Reason | Problem Identification |
| O | Total Score | Lead Qualification |
| P | Qualification | Lead Qualification |
| Q | Qualification Reason | Lead Qualification |
| R | Opening Observation | Personalization |
| S | Email Subject | Email Generator |
| T | Email Body | Email Generator |
| U | Review Status | Fixed value: `Pending` |
| V | Send Status | Fixed value: `Not Sent` |
| W | Contact Email | Contact Research |

If you add, remove, or reorder columns, update the Google Sheets field mapping to match. The spreadsheet ID in the JSON belongs to the original environment; select your own spreadsheet in your Zap.

## 4. Build or configure the trigger

The first step is **Webhooks by Zapier → Catch Hook**. It expects the field `company_url`.

1. In the Zap editor, create a new Zap and choose **Webhooks by Zapier** as the trigger app.
2. Select **Catch Hook** and continue.
3. Copy the unique webhook URL shown in the trigger's test/setup screen. Treat this URL like a secret; do not publish it in client-side code or commit it to GitHub.
4. Send a test request to the URL with a JSON body containing the exact key `company_url`.
5. Return to Zapier and test the trigger. Confirm that Zapier detected `company_url` as a field.

Example request (replace `YOUR_ZAPIER_WEBHOOK_URL` with the URL generated in your account):

```bash
curl -X POST "YOUR_ZAPIER_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"company_url":"https://example.com"}'
```

Use a real public company website for the end-to-end test; `example.com` only checks that the trigger receives a payload. If your sender uses form-encoded data instead of JSON, confirm the received field name is still exactly `company_url`.

Official instructions: [Trigger Zaps from webhooks](https://help.zapier.com/hc/en-us/articles/8496288690317-Trigger-Zaps-from-webhooks).

## 5. Add and configure the AI steps

Create the following AI steps using the prompts and output fields from the JSON export. For each step, choose the available AI completion action, connect your account/provider, select an available model, paste or preserve the step's instructions, map the inputs from prior steps, and run a test. Field labels in the Zap editor can differ slightly from the exported names, so map by meaning and verify with test data.

### Step 1 — Company Research

- **Input:** webhook `company_url`.
- **Configure:** Map the trigger's `company_url` field into the prompt/input field.
- **Expected information:** company name, website, industry, description, products/services, target customers, business model, geographic focus, public company size, business operations, customer-facing processes, potential operational challenges, potential AI/automation opportunities, key facts, and research observations.
- **Verify:** facts are based on public sources, and missing information is not invented.

### Step 2 — Company Analysis

- **Input:** output from Company Research.
- **Configure:** Map the Company Research fields into the exported `Company Research Output` input. Preserve the prompt's request for a business summary, activities, manual/repetitive processes, potential problems, AI/automation opportunities, highest-value opportunity, rationale, and supporting evidence.
- **Verify:** distinguish confirmed facts from inferences. Do not claim to know a company's internal processes without evidence.

### Step 3 — Problem Identification

- **Inputs:** Company Research and Company Analysis outputs.
- **Configure:** Map both upstream outputs into the matching input fields.
- **Expected information:** potential pain points, why they matter, business impact, supporting evidence/reasoning, confidence, and a primary pain point with rationale/evidence.
- **Verify:** treat the result as a hypothesis unless publicly available evidence confirms it.

### Step 4 — Lead Qualification

- **Inputs:** Company Research, Company Analysis, and Problem Identification outputs.
- **Configure:** Map all three outputs. Preserve the four scoring categories from the workflow prompt:

| Criterion | Maximum score |
| --- | ---: |
| Business relevance | 25 |
| Problem relevance | 25 |
| Potential business value | 25 |
| AI/SaaS opportunity | 25 |
| **Total** | **100** |

Qualification bands used by the workflow:

- **80–100:** Strong Fit
- **60–79:** Moderate Fit
- **0–59:** Low Fit

- **Verify:** individual scores are within their limits, the total is consistent, and the label matches the total. The score is a prioritization aid, not proof of buying intent.

### Step 5 — Personalization

- **Inputs:** Company Research, Company Analysis, Problem Identification, and Lead Qualification outputs.
- **Configure:** Map the four outputs to the corresponding fields.
- **Expected information:** opening observation, problem hypothesis, why it matters, solution connection/value proposition, reason for contacting the company, and a call to action.
- **Verify:** the opening is company-specific, evidence-based, and not based on unsupported claims.

### Step 6 — Email Generator

- **Inputs:** Company Research, Problem Identification, Lead Qualification, and Personalization outputs.
- **Configure:** Map the four outputs and keep the output fields for `subject`, `email_body`, and `cta`.
- **Verify:** the message is a draft, uses accurate details, and has a clear but low-pressure call to action.

**Do not add an email-sending action.** The workflow is intended to save a draft for human review only.

### Step 7 — Contact Research

The export includes a separate AI step titled **Contact Research**. It takes the webhook's `company_url` and the Company Research `company_name`.

- Map those two inputs into the matching fields.
- Preserve the prompt's restriction to the company's official website (such as Contact, About, Team, Leadership, Sales, or Business Development pages).
- Expected fields include `contact_name`, `contact_role`, `contact_email`, and `contact_source_url`.
- Do not guess email addresses or generate an address from a naming pattern. If an email is not publicly verified, leave it blank/unknown.
- In the Zap editor, confirm that Contact Research completes before the Google Sheets action uses its email output. If the editor's step order/dependencies differ from the export, adjust the order and test again.

## 6. Configure the Google Sheets action

1. Add or open the **Google Sheets → Create Spreadsheet Row** action (the export's action is `add_row`).
2. Connect the Google account that owns or can edit your destination spreadsheet.
3. Select your spreadsheet and worksheet.
4. Map the fields to columns A–W using the table in Section 3. In particular, verify that:
   - A uses the webhook's `company_url`.
   - B–L use Company Research outputs.
   - M–N use Problem Identification outputs.
   - O–Q use Lead Qualification outputs.
   - R uses Personalization's `opening_observation`.
   - S–T use Email Generator's `subject` and `email_body`.
   - U is the literal value `Pending`.
   - V is the literal value `Not Sent`.
   - W uses Contact Research's `contact_email`.
5. Test the action and inspect the newly created row in the sheet.
6. Confirm that long text/arrays are stored in a readable way. If an array or structured output appears as raw JSON, choose the appropriate output field or add a formatting/parsing step.

The export's spreadsheet and worksheet references are account-specific. Re-select them in your own Zap; do not copy the original spreadsheet ID.

## 7. Test the workflow from beginning to end

Keep the Zap off while configuring it. Use the editor's test functions to test each step before moving forward.

1. Send a webhook payload with a real public company URL.
2. Test Company Research and confirm the returned company name and other facts.
3. Test Company Analysis and Problem Identification; check evidence and confidence.
4. Test Lead Qualification; confirm the scores and qualification band.
5. Test Personalization and Email Generator; review the draft for accuracy.
6. Test Contact Research; verify any contact email against the cited official page.
7. Test Google Sheets; confirm exactly one correctly mapped row is created.
8. Repeat with a second company and with a company that does not publish a contact email.
9. Inspect the Zap's test results/task history for failed steps, empty fields, or malformed AI output.

| Test case | Expected result |
| --- | --- |
| Valid public company URL | Trigger captures `company_url` |
| Research output | Public facts are relevant; unknown details remain unknown |
| Qualification | Score total and label are consistent |
| Contact not published | Email remains blank/unknown, not guessed |
| Email generation | Draft only; no send action |
| Google Sheets | Values appear under the intended headers |
| Invalid URL or incomplete output | Failure is visible and reviewed before activation |

## 8. Troubleshooting

- **Webhook test receives no data:** confirm you used the correct Catch Hook URL, sent a non-empty POST body, used a supported content type, and spelled the key `company_url` exactly.
- **AI action cannot run:** reconnect the AI provider, choose a model available to your account, and inspect the test input/output.
- **Downstream input is empty:** retest the upstream step first, then re-map the correct output field in the next step.
- **AI output is malformed or missing fields:** review the prompt and output-field definitions. If the selected action does not expose structured fields, add an appropriate parsing step or map the returned output carefully.
- **Qualification score/label conflicts:** verify the four component scores, total, and thresholds.
- **Contact email is blank:** the official site may not publish one. Keep it blank and review manually rather than guessing.
- **Google Sheets row has misplaced data:** verify worksheet, row-1 headers, A–W order, and each field mapping.
- **Copied workflow is paused or broken:** reconnect your own integrations, replace account-specific resources, test every step, and only then consider turning it on.

For webhook-specific troubleshooting, see [Zapier's webhook troubleshooting guide](https://help.zapier.com/hc/en-us/articles/8496215655437-Zap-is-not-receiving-webhooks).

## 9. Final checklist before activation

- [ ] Your own AI provider/model and Google account are connected.
- [ ] The trigger receives a valid `company_url`.
- [ ] Each AI step has correct upstream inputs and tested output.
- [ ] Qualification scoring and labels are consistent.
- [ ] Contact details are verified against official public pages; unknown emails are not guessed.
- [ ] Google Sheets spreadsheet, worksheet, headers, and mappings are correct.
- [ ] A full test creates the expected row.
- [ ] Review status is `Pending` and send status is `Not Sent`.
- [ ] No email-sending action is configured.
- [ ] API keys, OAuth tokens, and webhook URLs/secrets are not committed to the repository.
- [ ] Spreadsheet access is limited to appropriate people.

After every check passes, you may enable the Zap in your own Zapier account. Submit one controlled test after activation and monitor the Zap history and destination sheet.

## Security and responsible use

- Never commit API keys, passwords, OAuth tokens, or private webhook URLs.
- Restrict spreadsheet access and use the minimum permissions needed.
- Treat website content as untrusted input; review AI output before using it.
- Use relevant public business contact information responsibly and follow applicable privacy and anti-spam requirements.
- Respect website terms and access restrictions.
- Verify facts, inferred pain points, qualification scores, and outreach drafts before contacting a prospect.
