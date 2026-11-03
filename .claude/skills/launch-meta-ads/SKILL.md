---
name: launch-meta-ads
description: Launch a G3 Pro Tint Meta (Facebook/Instagram) messages campaign (WhatsApp + Messenger) from a Google Drive folder of pic 1-10 or video 1-10. One CBO campaign, one ad set named "Broad" (Advantage+ audience and placements, the Andromeda setup), one ad per creative, copy in Malay/English/Chinese. Use when the user says "launch", "upload ads", "new campaign" or names a Drive folder for G3 Setapak or Setia Alam.
---

# Launch G3 Meta ads from Drive

Config lives in `config/accounts.json`, `config/defaults.json` and `config/cities.json`. Read all three first.
Every Meta tool call needs `advertiser_job: "create_and_launch_new_ads"`, the same `client_conversation_id` for the whole conversation, and the user's own words in `advertiser_request`.

**Hard rules**
- Never publish without an explicit "go", "publish" or "approve" from the user at **Step 6**. New objects are created either as drafts or as real **PAUSED** objects (G3 Setapak gave PAUSED on 2026-11-03). Neither spends until `ads_activate_entity`.
- Never guess the budget. Ask for the budget **and** the location on every launch, even if one was used last time.
- Only use the two G3 accounts in `config/accounts.json`.
- Set Drive "anyone with link" only on the files picked for this launch.

## Step 1: Brief
Collect the following, and ask for anything that's missing in one message:

| Item | Default / note |
|---|---|
| Account | `setapak` or `setia_alam` |
| Drive folder | Folder link, ID or name. Search with `search_files` `title contains '<name>' and mimeType = 'application/vnd.google-apps.folder'` |
| Campaign name | The campaign the user built (or duplicated) in Ads Manager, switched off, with the Broad ad set and one first ad (see Step 5) |
| Daily budget (RM) | **Required, ask.** The user sets it on the campaign in Ads Manager. Claude reads it back (`daily_budget` at campaign level) and confirms it matches. |
| Location | Malaysia-wide **or** a city + radius. Always ask. The user sets it on the Broad ad set. Claude checks `targeting.geo_locations` matches. |
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

## Step 4: Get media into Meta (library first)
**4a. Reuse what Meta already has.** Call `ads_get_ad_videos` (title filter, e.g. `"Video 1"`) and `ads_get_ad_images` (name filter) on the ad account. If an item's title equals the Drive file title, or contains it, reuse its `video_id` / `image_hash`. Do not upload it again. Show matches in the Step 3 table as "already in Meta".

**4b. Only for files still missing,** ask the user once, listing the files: "OK to set these to *anyone with link* in Drive so Meta can download them? Or upload them to Ads Manager → Media Library yourself." Claude Code's safety check blocks link-sharing unless the user approves the prompt. If it is denied, do not retry or work around it. Use the manual-upload route.

For each approved missing file:
1. `mcp__Google_Drive__share_file` with `fileId`, `role: "reader"`, `permissionId: "anyoneWithLink"`.
2. `mcp__META__ads_creative_upload_media` with `upload_source: "URL"`, `media_type: IMAGE|VIDEO`, `media_url: https://drive.usercontent.google.com/download?id=<fileId>&export=download&confirm=t`, and `name: "<campaign> - <file title>"`.
   - IMAGE: keep the returned `image_hash`.
   - VIDEO: keep the `video_id`, then poll `ads_get_ad_videos` with `video_ids` and `fields:["status","picture"]` until the status is ready. Big files take minutes. Use `Monitor`/waits, not a tight loop.
3. If the URL upload fails (HTML instead of media, or a size error), fall back: ask the user to upload that file in **Ads Manager → Media Library** for this ad account, then find it with `ads_get_ad_videos` / `ads_get_ad_images` (`title` / `name` filter).
4. After all uploads, list the shared Drive files and tell the user they can turn link-sharing off (Share → General access → Restricted). The Drive tools here cannot remove a permission. Meta keeps its own copy, so turning sharing off is safe.

## Step 5: Find the user's Broad ad set, then add the ads
**Who does what (verified 2026-11-03):** the Meta MCP tool cannot create ad sets that target Malaysia (Meta error 3858634, "Advertiser is missing: Provide a verified advertiser"), and it cannot set Messenger + WhatsApp. So the **user** creates the campaign, the Broad ad set and **one first ad** in Ads Manager, then publishes it with the campaign switched **off**. An ad set with no ads stays an unpublished draft, which the tools cannot see. Duplicating last launch's campaign in Ads Manager is the fastest way to do this. Claude adds every other ad.

1. **Ask the user** (at Step 1) to do this and tell you the campaign name. Remind them to set: Messaging apps → Messenger + WhatsApp, the location you agreed, age 18+, languages Malay/English/Chinese, Advantage+ audience and placements on, verified advertiser, CBO budget, campaign OFF.
2. **Find it:** `ads_get_ad_entities` with `level: "adset"`, `filtering` name CONTAIN "Broad", `sort: "created_time_descending"`, and fields `id,name,campaign_id,effective_status,destination_type,targeting`. Then check:
   - `destination_type` is `MESSAGING_MESSENGER_WHATSAPP`.
   - The location matches what was agreed.
   - `targeting.locales` is set. If it's missing, tell the user the languages were not set. Advantage+ still works without it.
   - If it's not found, the ad set is probably still a draft. Ask the user to add one ad and publish with the campaign off.
3. **Per creative:** `ads_create_ad` with an **inline** creative. Do NOT use `ads_create_creative` for this, because a separate creative fails with "Creative is missing DOF spec" on Messenger + WhatsApp ad sets.
   - `ad_set_id`, `ad_name: "<kind><n> | <lang>"`, `hide_ui: true` when there are many.
   - `creative` (JSON string):
     ```json
     {"name":"<campaign> | <kind><n> | <lang>",
      "object_story_spec":{"page_id":"<page_id>",
        "video_data":{"video_id":"<id>","image_url":"<video picture or cover>","message":"<primary_text>","title":"<headline>",
                      "call_to_action":{"type":"MESSAGE_PAGE","value":{"app_destination":"MESSENGER"}}}},
      "asset_feed_spec": <defaults.creative.asset_feed_spec>,
      "degrees_of_freedom_spec": <defaults.creative.degrees_of_freedom_spec>}
     ```
   - For an image, use `"link_data":{"image_hash":"<hash>","message":...,"name":"<headline>","link":"https://www.facebook.com/<page_id>","call_to_action":{"type":"MESSAGE_PAGE","value":{"app_destination":"MESSENGER"}}}` instead of `video_data`. Verify this on the first pic launch and fix the runbook if Meta asks for a different shape.
   - `text_optimizations` is OPT_OUT on purpose: the copy runs exactly as written.
   - Do not set `self_ai_disclosure` yourself. If the user says media is AI-made, ask.
4. **Placeholder ad:** if the user's first ad was only a placeholder (e.g. text "test"), offer to pause it with `ads_update_entity` and `{"status":"PAUSED"}`, so only the real ads run. Never delete without asking.
5. **Check:** any error returned from `ads_create_ad`, fix it and retry once, or report it. If Meta says something about Instagram: no IG account is linked, and the user said IG linking is not needed (2026-11-03). Ask before limiting placements to Facebook only.
6. **Previews:** `ads_create_ad` already returns `previews[].preview_url`. Give 2–3 of them (Facebook Feed, Reels, Stories), or call `ads_get_ad_preview`.

## Step 6: Final approval (STOP #2)
Show the campaign, ad set and ad IDs, preview links, any warnings, and the Drive files to un-share.
Ask: "Publish now? It goes live and starts spending RM<x>/day."
- If the objects are PAUSED (not drafts): on an explicit yes, call `ads_activate_entity` top-down, one call each for the campaign, the ad set and every ad.
- If they are drafts: on an explicit yes, `ads_activate_entity` with `entity_type: "campaign"`, `entity_id: <campaign_id>`, and `object_ids` = [campaign, ad set, all ads] (from the draft read). Set `publish_as_active: true` only if they said go live.
- Verify with live `ads_get_ad_entities` (`level: campaign`, `object_ids`, fields `id,name,effective_status,daily_budget`). Report the real status. `PUBLISHING` or `IN_REVIEW` is not "live yet".
- On "no" or "later", leave it paused or in draft and say where it is in Ads Manager.

## Step 7: Log
Write `launches/<yyyy-mm-dd>-<account>-<slug>.md` containing:
- the brief table
- Drive file ID → Meta image_hash / video_id
- campaign / ad set / ad / creative IDs
- status
- any issues

Commit with message `Launch log: <campaign name>` and push to the working branch.
