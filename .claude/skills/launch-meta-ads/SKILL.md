---
name: launch-meta-ads
description: Launch a G3 Pro Tint Meta (Facebook/Instagram) messages campaign (WhatsApp + Messenger) from a Google Drive folder of pic 1-10 or video 1-10. One CBO campaign, one ad set named "Broad" (Advantage+ audience and placements, the Andromeda setup), one ad per creative, copy in Malay/English/Chinese. Use when the user says "launch", "upload ads", "new campaign" or names a Drive folder for G3 Setapak or Setia Alam.
---

# Launch G3 Meta ads from Drive

Config lives in `config/accounts.json`, `config/defaults.json` and `config/cities.json`. Read all three first.
Every Meta tool call needs `advertiser_job: "create_and_launch_new_ads"`, the same `client_conversation_id` for the whole conversation, and the user's own words in `advertiser_request`.

**Hard rules**
- Never publish without an explicit "go", "publish" or "approve" from the user at **Step 6**. Draft mode keeps everything unpublished until `ads_activate_entity`.
- Never guess the budget. Ask for the budget **and** the location on every launch, even if one was used last time.
- Only use the two G3 accounts in `config/accounts.json`.
- Set Drive "anyone with link" only on the files picked for this launch.

## Step 1: Brief
Collect the following, and ask for anything that's missing in one message:

| Item | Default / note |
|---|---|
| Account | `setapak` or `setia_alam` |
| Drive folder | Folder link, ID or name. Search with `search_files` `title contains '<name>' and mimeType = 'application/vnd.google-apps.folder'` |
| Campaign name | Folder name |
| Daily budget (RM) | **Required, ask.** Must be ≥ `min_daily_budget_rm`. Pass to Meta in sen (RM × 100). |
| Location | Malaysia-wide (`countries:["MY"]`) **or** a city from `config/cities.json` (+ optional radius override). Always ask. |
| Languages | BM + EN + CN (all locale IDs in `defaults.languages`) unless the user narrows it |
| Pics or videos | Whatever the folder holds. If it has both, ask: pics only, videos only, or mixed (max 10 total) |

## Step 2: Scan Drive
1. `mcp__Google_Drive__search_files` with `parentId = '<folderId>'`, `excludeContentSnippets: true`, pageSize 100.
2. Match files with `defaults.creative.file_pattern_pic` / `file_pattern_video` (case-insensitive). Examples: "pic1.jpg", "Pic 3 promo.png", "Video 2 RM99.mp4". Sort by number and keep 1–10. Covers matching `cover_pattern` (e.g. "video1-cover.jpg", "Video 1 封面.jpg") become the thumbnail for that video number.
3. Find the copy file. It is a Google Doc or .txt whose title contains "copy", "caption" or "text". Read it with `read_file_content` and parse it as in `templates/copy-template.md`:
   - `[BM]`, `[EN]` and `[CN]` sections, each with `primary_text:` (can be multi-line until the next key or section) and `headline:`.
   - Optional `assign:` line (`pic1=BM, pic2=CN, video3=EN`).
   - Unassigned creatives rotate BM → EN → CN in number order.
4. Stop and tell the user if any of these is true: no copy file, a language section is missing for a language that is in use, zero matching creatives, more than 10 creatives, or a file type that isn't jpg/png/gif/mp4/mov.

## Step 3: Verification check (STOP #1)
Show this and wait for OK or edits:

```
Account:    G3 PRO TINT Setapak (889018630434588) | Page: G3 Pro tint - Setapak
Campaign:   G3 | Setapak | <name> | MSG | <date>   (CBO, RM<x>/day, lowest cost)
Objective:  Engagement → Messages: WhatsApp + Messenger (Conversations)
Ad set:     Broad | <Malaysia | City name + radius km> | Age 18+ | Lang: Malay, English, Chinese
            Advantage+ audience ON, Advantage+ placements ON
Ads (n):
  # | File                | Lang | Headline
  1 | Video 1 RM99.mp4    | BM   | ...
Drive: these n files will be set to "Anyone with the link can view" so Meta can download them.
```

## Step 4: Upload media to Meta
For each creative, plus each video cover:
1. `mcp__Google_Drive__share_file` with `fileId`, `role: "reader"`, `permissionId: "anyoneWithLink"`.
2. `mcp__META__ads_creative_upload_media` with `upload_source: "URL"`, `media_type: IMAGE|VIDEO`, `media_url: https://drive.usercontent.google.com/download?id=<fileId>&export=download&confirm=t`, and `name: "<campaign> - <file title>"`.
   - IMAGE: keep the returned `image_hash`.
   - VIDEO: keep the `video_id`, then poll `ads_get_ad_videos` with `video_ids` and `fields:["status","picture"]` until the status is ready. Big files take minutes. Use `Monitor`/waits, not a tight loop.
3. If the URL upload fails (HTML instead of media, or a size error), fall back: ask the user to upload that file in **Ads Manager → Media Library** for this ad account, then find it with `ads_get_ad_videos` / `ads_get_ad_images` (`title` / `name` filter).
4. After all uploads, list the shared Drive files and tell the user they can turn link-sharing off (Share → General access → Restricted). The Drive tools here cannot remove a permission. Meta keeps its own copy, so turning sharing off is safe.

## Step 5: Build the draft
1. **Campaign:** `ads_create_campaign`
   - `ad_account_id`, `campaign_name` (from `name_pattern`), `objective: "OUTCOME_ENGAGEMENT"`, `buying_type: "AUCTION"`
   - `campaign_daily_budget: <RM×100>`, `campaign_bid_strategy: "LOWEST_COST_WITHOUT_CAP"`, `special_ad_categories: "[]"`
   - Check the response's `valid_optimization_goals` for CONVERSATIONS.
2. **Ad set:** `ads_create_ad_set`
   - `ad_set_name: "Broad"`, `campaign_id`, `destination_type: "MESSAGING_MESSENGER_WHATSAPP"` (WhatsApp + Messenger), `optimization_goal: "CONVERSATIONS"`, `billing_event: "IMPRESSIONS"`
   - `promoted_object: {"page_id":"<page_id>"}`
   - `targeting` (JSON string):
     - Malaysia: `{"geo_locations":{"countries":["MY"],"location_types":["home","recent"]},"age_min":18,"locales":[41,6,24,20,21,22]}`
     - City with `meta_place`: `{"geo_locations":{"places":[{"key":"281386432021532","radius":16,"distance_unit":"mile"}],"location_types":["home","recent"]},...}`
     - Other city: `{"geo_locations":{"custom_locations":[{"latitude":3.1046,"longitude":101.459,"radius":25,"distance_unit":"kilometer"}],"location_types":["home","recent"]},...}`
     - `locales` = the union of the chosen languages' IDs.
     - Do **not** send `targeting_automation` (Advantage+ audience stays on) or any `publisher_platforms` / `*_positions` (Advantage+ placements).
   - No budget on the ad set (CBO).
3. **Per creative:** `ads_create_creative`
   - `ad_account_id`, `page_id`, `name: "<campaign> | <kind><n> | <lang>"`
   - Image: `image_hash`. Video: `video_id` + thumbnail (`image_hash` of the cover, else `image_url` = the video's `picture`).
   - `message` = primary_text, `headline` = headline for its language.
   - `call_to_action_type: "MESSAGE_PAGE"` (no `link_url`). This matches the live G3 creatives.
   - `degrees_of_freedom_spec`: the multi-destination spec from `defaults.creative.degrees_of_freedom_spec`. If that is still null, create the first creative, read the error Meta returns ("Creative is missing DOF spec…"), and build the spec it asks for (`ads_get_help_article` if unclear). Then save the working spec to `config/defaults.json`.
   - `instagram_user_id` from config if not null.
   - Do not set `self_ai_disclosure` yourself. If the user said media is AI-made, ask.

   Then `ads_create_ad` with `ad_set_id`, `ad_name: "<kind><n> | <lang>"`, `creative: {"creative_id":"<id>"}`. Use `hide_ui: true` when there are many.
4. **Validate:** `ads_get_ad_entities` with `object_state: "draft"` and `object_ids: [campaign_id]`. For every `active_errors` item:
   - WhatsApp not linked to the Page, or multi-destination rejected: tell the user. Offer Messenger only (`MESSENGER` + `MESSAGE_PAGE`, no DOF spec), or WhatsApp only (`WHATSAPP` + `WHATSAPP_MESSAGE`, after they connect WhatsApp in Meta Business Suite → Settings → WhatsApp accounts). Use whichever they pick.
   - Instagram identity missing: tell the user. Recommend linking IG. Otherwise, with their OK, restrict placements to Facebook + Messenger + WhatsApp.
   - Locale rejected: drop `locales` and say so.
   - Anything else: fix with `ads_update_entity`, or report it.
5. **Previews:** `ads_get_ad_preview` for 1–2 ads (use `creative_id` if the draft `ad_id` fails). Give the `preview_url` links.

## Step 6: Final approval (STOP #2)
Show the campaign, ad set and ad IDs, preview links, warnings, and the Drive files to un-share.
Ask: "Publish now? It goes live and starts spending RM<x>/day."
- On an explicit yes: `ads_activate_entity` with `entity_type: "campaign"`, `entity_id: <campaign_id>`, and `object_ids` = [campaign, ad set, all ads] (from the draft read). Set `publish_as_active: true` only if they said go live.
- Verify with live `ads_get_ad_entities` (`level: campaign`, `object_ids`, fields `id,name,effective_status,daily_budget`). Report the real status. `PUBLISHING` or `IN_REVIEW` is not "live yet".
- On "no" or "later", leave it in draft and say where it is (Ads Manager → Drafts).

## Step 7: Log
Write `launches/<yyyy-mm-dd>-<account>-<slug>.md` containing:
- the brief table
- Drive file ID → Meta image_hash / video_id
- campaign / ad set / ad / creative IDs
- status
- any issues

Commit with message `Launch log: <campaign name>` and push to the working branch.
