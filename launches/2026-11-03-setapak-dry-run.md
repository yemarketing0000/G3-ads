# Dry run: G3 Setapak, 2026-11-03

Goal: test the workflow end-to-end without publishing.

| Item | Value |
|---|---|
| Account | G3 PRO TINT Setapak (889018630434588) |
| Page | G3 Pro tint - Setapak (1277296285476730) |
| Media | Video 1 RM99 → `1818189619335603` (ready, 58s), Video 2 RM99 → `4316492318662458` (ready, 68s). Both were already in the Meta library, so no Drive sharing was needed |
| Campaign | `120253825355480526`, "TEST DRY RUN - DO NOT PUBLISH | G3 | Setapak | Sept Ads | MSG | 2026-11-03", OUTCOME_ENGAGEMENT, CBO RM5/day, **PAUSED** (no ad set, no ads, cannot spend) |

## Results
1. **Drive link-sharing**: blocked by Claude Code's permission check. Runbook changed to "Meta library first; ask the user before sharing anything".
2. **Campaign**: created OK, as a real PAUSED campaign (not a draft).
3. **Ad set "Broad"**: blocked.
   - `destination_type=MESSAGING_MESSENGER_WHATSAPP` (also tried copying from live "Broad 1 - pic" via `source_adset_id`): the tool rejects it with "CONVERSATIONS requires a messaging destination_type (INSTAGRAM_DIRECT, MESSENGER, WHATSAPP)". Workaround: create as WHATSAPP, then the user switches it to "Messenger and WhatsApp" in Ads Manager.
   - `destination_type=MESSENGER`: Meta error `3858634`, "Advertiser is missing: Provide a verified advertiser so ads in this ad set can be delivered to audiences in the selected locations". Malaysia needs a default advertiser/payer set in Advertising settings. **User action needed.**
4. Ads were not created (blocked at the ad set step).

## Next
- User: set the default advertiser and payer for Malaysia on both G3 ad accounts.
- Then re-run from Step 5 on campaign `120253825355480526`, or delete it.

## Retry after the user set the default advertiser/payer (same day)
- Ad set create with `WHATSAPP` → still `3858634` "Advertiser is missing".
- Same call with `dsa_beneficiary` and `dsa_payor` = "G3 Pro Tint X CC" → still `3858634`.
- Conclusion: the Ads Manager default does not reach ad sets made through the Meta MCP tool, and the tool cannot send Malaysia's verified-advertiser field. **New split:** the user creates the campaign and the "Broad" ad set in Ads Manager (which also allows Messenger + WhatsApp). Claude does everything else: media, creatives, ads, previews, publish.
