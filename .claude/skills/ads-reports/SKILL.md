---
name: ads-reports
description: Build and email the G3 Pro Tint Meta ads reports to lauweiming98@gmail.com. Daily covers yesterday. Weekly runs on Mondays and covers last Mon–Sun. Monthly is per campaign, sent the morning a campaign completes each month from its own start date. Each report covers spend, messages (Messenger + WhatsApp), most clicks, cheapest and most expensive ads per message, and ads with no messages. Used by the 8:44am MYT scheduled task "G3 ads reports (daily, weekly, monthly)", or when the user asks for a daily, weekly or monthly report.
---

# G3 ads reports (daily · weekly · monthly)

The scheduled task (`trig_01YXACagkpJzEFbHD5FCgwR6`, 8:44am MYT every day) carries a self-contained copy of these instructions. Keep both in sync when either changes.

**Rules:**
- Read-only. Never change Meta.
- Email **lauweiming98@gmail.com** only. No WhatsApp (the user declined it on 2026-11-03).
- Every Meta call: `advertiser_job: "report_measure_or_look_up"`. Add `scheduled_task_run: true` when started by the schedule.

**Accounts:** see `config/accounts.json`: Setapak `889018630434588`, Setia Alam `1097140736142240`. Time zone is MYT.

## Which reports, when
| Report | Sent | Period | Compared with |
|---|---|---|---|
| Daily | every day, about 8:45am | yesterday (`date_preset: "yesterday"`) | – |
| Weekly | Mondays | last Mon–Sun (`"last_week_mon_sun"`) | the Mon–Sun before |
| Monthly | per campaign, on each monthly anniversary of its **start date** | anniversary N−1 → day before anniversary N | that campaign's previous month (none for month 1) |

**Monthly date rule:** A_N = start date + N calendar months. If that day doesn't exist in the month, use the month's last day. A report is due when today = A_N, and it covers A_(N−1) … A_N − 1 day. Examples:
- Start 1 Oct: reports on 1 Nov (1–31 Oct), then 1 Dec (1–30 Nov), and so on.
- Start 31 Oct: reports on 30 Nov (31 Oct–29 Nov), then 31 Dec (30 Nov–30 Dec).

Skip DELETED and ARCHIVED campaigns, and campaigns with RM0 spend in the period. If several campaigns are due on the same day, send one monthly email with a section per campaign. Get start dates from `ads_get_ad_entities` at `level: "campaign"`, `fields: ["id","name","effective_status","start_time","stop_time"]`.

## Pull (per account, per period)
Call `ads_get_ad_entities` with:
- `level: "ad"`
- fields: `id, name, adset_name, campaign_name, campaign_id, amount_spent, impressions, reach, clicks, link_click, ctr, cpc, results, cost_per_result`
- filter `amount_spent > 0`. For monthly, also filter `campaign.id IN [id]`.
- `sort: "amount_spent_descending"`, `limit: 200`, `reporting_purpose: ["metric_lookup"]`, `include_additional_context: false`
- the date_preset, or `time_range: {"since","until"}`

Follow `pagination.next_cursor`. `results` = messaging conversations started (Messenger + WhatsApp). "Not available" = 0.

## Compute (python3, never by hand)
- Totals per account (per campaign for monthly) and overall: spend, messages, RM/message, clicks (all), impressions.
- **Recruitment ads** are those whose campaign or ad set name contains 招聘, "hiring" or "recruit". Show them in their own table and leave them out of the rankings.
- **Rankings** (customer ads only). Minimum spend is **RM10 daily** and **RM30 weekly/monthly**.
  - ⭐ Most clicks: top 3.
  - ✅ Cheapest per message: top 3, among ads with spend ≥ minimum and ≥ 1 message.
  - ⚠️ Most expensive per message: top 3, same rule.
  - 🚫 Spend ≥ minimum with 0 messages: list all.
- **Weekly and monthly only:**
  - % change vs the previous equal period, for spend, messages and RM/message.
  - 2–3 plain suggestions (keep, scale or replace which pics/videos). Suggestions only.

## Email
**Subjects:**
- `G3 Daily Ads Report – 2 Nov 2026`
- `G3 Weekly Ads Report – 26 Oct–1 Nov 2026`
- `G3 Monthly Ads Report – Setapak 30/9 – Month 2 (1 Nov–30 Nov 2026)`

**htmlBody:** phone-friendly inline-styled HTML, in this order:
1. Summary lines.
2. The four highlight lists.
3. Comparison and suggestions (weekly and monthly).
4. A table per account or campaign: Ad, Campaign, Spend (RM), Msgs, RM/msg, Clicks, Link clicks, CTR. Sort by spend.
5. Recruitment table.
6. Footer: "Data: Meta Ads (MYT). Messages = Messenger + WhatsApp conversations started."

Also add a plain-text `body` with the summary and highlights.

If an account had no spend, say so. If a Meta call fails, still send what loaded and name the failure.
