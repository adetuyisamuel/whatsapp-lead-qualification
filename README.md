# WhatsApp Lead Qualification — Make + GPT-4o

An AI-powered lead qualification system built in Make (Integromat). Every inbound WhatsApp message is automatically scored by GPT-4o, routed based on intent, logged in Airtable, and responded to instantly — 24 hours a day with no manual involvement.

---

## The Problem

Most businesses respond to leads hours after first contact. By then the lead has already talked to a competitor. And without qualification, the sales team wastes time on low-intent enquiries while high-value leads don't get the attention they deserve.

---

## The Solution

Every WhatsApp message triggers the scenario instantly. GPT-4o scores the lead 1–10, identifies intent and industry, and routes them automatically — qualified leads get a fast response and a Slack alert to the team, unqualified leads enter a nurture sequence.

---

## What Happens Automatically

```
WhatsApp message received
        ↓
Parse sender name, phone, message
        ↓
GPT-4o scores lead 1-10
Returns: score, intent, industry, reason, next action
        ↓
Router splits on score threshold
        ↓
SCORE 7+                    SCORE below 7
   ↓                              ↓
Add to Airtable CRM        Add to Airtable CRM
Status: Qualified           Status: Nurture
   ↓                              ↓
Slack alert to team        Send nurture WhatsApp reply
   ↓
Send instant WhatsApp reply
```

---

## Results

- Lead response time reduced from **hours to under 30 seconds**
- Sales team only contacts pre-qualified leads — no wasted calls
- 100% of leads captured and logged automatically
- Zero manual work after initial setup

---

## Tools and Stack

| Tool | Purpose |
|---|---|
| Make (Integromat) | Scenario automation engine |
| WhatsApp Business API / Twilio | Inbound message trigger |
| GPT-4o (OpenAI) | Lead scoring and qualification |
| Airtable | CRM — all leads logged with scores |
| Slack | Instant team alert for qualified leads |
| Twilio SMS | Auto-reply to leads |
| Google Sheets | Error logging |

---

## Scenario Modules

1. **Webhook** — Receives inbound WhatsApp message
2. **Set Variables** — Extracts sender name, phone, message body
3. **OpenAI GPT-4o** — Scores lead with structured JSON prompt at temperature 0
4. **Function** — Parses and validates GPT response safely
5. **Router** — Three paths: Qualified / Not Qualified / Error
6. **Airtable (Qualified)** — Creates lead record, Status: Qualified
7. **Slack** — Posts lead summary to #qualified-leads channel
8. **Twilio** — Sends instant reply to qualified lead
9. **Airtable (Nurture)** — Creates lead record, Status: Nurture
10. **Twilio** — Sends warm nurture reply to unqualified lead
11. **Google Sheets** — Logs parse errors with full details
12. **Slack** — Alerts team on any error

---

## The GPT Scoring Prompt

```
Analyze this WhatsApp message and return ONLY a JSON object:

Sender name: [name]
Phone: [phone]
Message: [message]

Return exactly this structure:
{
  "score": (integer 1-10),
  "intent": ("high", "medium", or "low"),
  "industry": (best guess or "unknown"),
  "reason": (one sentence max),
  "next_action": ("book_call", "send_info", or "nurture"),
  "qualified": (true if score >= 7, false otherwise)
}
```

Temperature is set to 0 for deterministic, consistent output every time.

---

## Airtable CRM Structure

Create a `Leads` table with these fields:

| Field | Type |
|---|---|
| Lead Name | Text |
| Phone | Phone |
| Message | Long text |
| GPT Score | Number |
| Intent | Single select |
| Industry | Single select |
| Reason | Text |
| Next Action | Single select |
| Status | Single select |
| Date Received | Date |

Status options: `Qualified`, `Nurture`, `Contacted`, `Converted`, `Dead`

---

## How to Import Into Make

1. Open Make → click **Scenarios** → click **Create a new scenario**
2. Click the **three dots** menu → **Import Blueprint**
3. Upload `whatsapp_lead_qualification_blueprint.json`
4. The full scenario loads with all modules connected

---

## 4 Things to Update Before Testing

**1. WhatsApp / Twilio connection**
- Connect your Twilio account in Make credentials
- Set up a Twilio WhatsApp sandbox or production number
- Point the webhook URL to your Make scenario

**2. OpenAI credentials**
- Add your OpenAI API key in Make connections
- Assign to the GPT-4o module

**3. Airtable Base ID**
- Replace `YOUR_AIRTABLE_BASE_ID` in both Airtable modules
- Create the `Leads` table with the fields above

**4. Google Sheet ID**
- Replace `YOUR_GOOGLE_SHEET_ID` in the error log module
- Create a sheet called `Error Log`

---

## How to Test

Send a POST request to your Make webhook URL with:

```json
{
  "from_name": "James Thornton",
  "from": "+2348012345678",
  "body": "Hi, I run a 20-person accounting firm and I'm looking for someone to automate our client onboarding. We currently do everything manually and it's taking up about 3 hours per client. What would this cost and how long would it take?"
}
```

Expected result:
- GPT score: 8–9 (high intent, clear pain point, specific ask)
- Status: Qualified
- Airtable record created
- Slack alert posted
- Auto-reply sent

---

## Customisation

- **Swap Twilio for WhatsApp Business API directly** — replace the Twilio modules with HTTP Request modules pointing to the Meta WhatsApp API
- **Swap Airtable for HubSpot** — replace Airtable modules with HubSpot CRM modules
- **Add a booking link** — include a Calendly link in the qualified lead auto-reply
- **Add a 7-day nurture sequence** — use Make's scheduling to follow up with unqualified leads after 7 days
- **Adjust the score threshold** — change the router filter from 7 to any value that fits your business

---

## About This Project

Built as part of a professional automation portfolio. Demonstrates AI-powered decision making inside a no-code automation scenario — GPT scoring, conditional routing, multi-tool integration, and error handling.

**Tools demonstrated:** Make · GPT-4o · WhatsApp API · Twilio · Airtable · Slack · Router · Error handling · JSON parsing
