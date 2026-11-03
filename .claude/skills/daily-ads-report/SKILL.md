---
name: daily-ads-report
description: Build and send the G3 Pro Tint daily Meta ads report for yesterday (both G3 ad accounts). It covers the ads with the most clicks, the most expensive and cheapest ads by cost per message, and ads that spent with no messages. Sent by email to lauweiming98@gmail.com, plus a short phone alert. Used by the 8:44am MYT scheduled task, or when the user asks for "today's report" or "yesterday's ads report".
---

# G3 daily ads report

Read-only. Never change, pause or activate anything from this report. Only describe and suggest.
Read `config/accounts.json` for the two ad accounts. Every Meta call takes `advertiser_job: "report_measure_or_look_up"`, the same `client_conversation_id` throughout the run, and `scheduled_task_run: true` when started by the schedule.

## 1. Pull yesterday's numbers (per ad)
For **each** account (`setapak`, then `setia_alam`), call `mcp__META__ads_get_ad_entities` with:
- `level: "ad"`, `date_preset: "yesterday"` (the account time zone is Malaysia)
- `fields: ["id","name","adset_name","campaign_name","amount_spent","impressions","reach","clicks","link_click","ctr","cpc","results","cost_per_result"]`
- `filtering: [{"field":"amount_spent","operator":"GREATER_THAN","value":["0"]}]`
- `sort: "amount_spent_descending"`, `limit: 100`, `reporting_purpose: ["metric_lookup"]`, `include_additional_context: false`
- If there's a `pagination.next_cursor`, fetch the next page with the same args plus `cursor`.

Field notes:
- `results` is **messaging conversations started**, for Messenger + WhatsApp. Read `results.values[0].value`. "Not available" means 0.
- `cost_per_result` is RM per message. "Not available" means no messages.
- Use `clicks` (Clicks (all)) for "most clicks". Also show `link_click` and CTR.

## 2. Work out the highlights (per account, and overall)
- **Totals:** spend, messages, cost per message (spend ÷ messages), Clicks (all), impressions.
- **Most clicks:** top 3 ads by `clicks`. Show the ad name, which is the pic/video name, and its campaign.
- **Cheapest ads:** the 3 lowest cost per message, among ads with ≥ 1 message **and** spend ≥ RM10. The minimum spend avoids flukes from tiny budgets.
- **Most expensive ads:** the 3 highest cost per message, using the same rule.
- **Spent but no messages:** every ad with spend ≥ RM10 and 0 messages. Flag these first.
- Keep **recruitment ads** (campaign or ad set name contains 招聘, "hiring" or "recruit") in their own small section. Leave them out of the customer rankings, because they chase job applicants, not tint customers.
- If an account spent RM0 yesterday, say "No spend yesterday" for it.

Do the maths with a short Python script (`python3 -I`) on the JSON numbers. Don't do it in your head.

## 3. Send it
**Email** with `mcp__Gmail__send_message`:
- `to: ["lauweiming98@gmail.com"]` (the user asked for reports to go here on 2026-11-03)
- `subject: "G3 Ads Report – <D Mon YYYY> (yesterday)"`
- `htmlBody`: simple inline-styled HTML, readable on a phone, no external images or scripts:
  1. A summary line per account plus an overall line: spend · messages · RM/message · clicks.
  2. ⭐ Most clicks: top 3.
  3. ✅ Cheapest per message: top 3.
  4. ⚠️ Most expensive per message: top 3.
  5. 🚫 Spent ≥ RM10 with 0 messages.
  6. A full table per account with every ad that spent: Ad, Campaign, Spend, Messages, RM/msg, Clicks, Link clicks, CTR. Sort by spend.
  7. Recruitment ads: a short table.
  8. Footer: "Data: Meta Ads, yesterday in MYT. Messages = Messenger + WhatsApp conversations started. Numbers can shift slightly as Meta finalises."
- `body`: a plain-text version of points 1–5.

**WhatsApp** (via the Bird connector), once a workspace sender number is connected and the `g3_daily_ads_report` template is approved:
- `mcp__Bird__whatsapp_send` with `to: "+60123828281"`, `from: <connected Bird number>`, `template: {slug: "g3_daily_ads_report", language: "en", components: [...]}`. Fill the template's body parameters with the summary values. Plain text only, so no tables.
- Status 2026-11-03: **not active yet**. The Bird workspace has no connected WhatsApp number or business account. Until it does, skip WhatsApp and say so in the run summary.

**Phone alert**, if the `PushNotification` tool is available: one line under 200 characters, e.g.
`G3 2 Nov: RM1,342 spent, 91 msgs, RM14.75/msg. Best: SA Pic 1 RM10.46. Worst: SA Pic 5 RM62.22. Report in email.`

## 4. Don'ts
- Only send to the address(es) in this file. Change them only when the user asks in chat.
- Don't include the Meta access IDs of anything other than ads.
- If a Meta call fails, still send the email for whatever did load, and say clearly which account failed and why.
