# Launch: Setia Alam — Coating pics 1–10 (Broad 2), 2026-11-19

## Brief
| Item | Value |
|---|---|
| Account | G3 Setia Alam (`1097140736142240`), Page `1327702287093457` |
| Campaign | Setia alam 31/10 (`120255848421820119`), CBO RM500/day shared with Broad (tint) — user: keep budget |
| Ad set | **Broad 2** (`120256170543870119`) — created by Claude with `ads_create_ad_set` |
| Destination | Messenger + WhatsApp (`MESSAGING_MESSENGER_WHATSAPP`) |
| Location | Custom pin Setia Alam 3.1046, 101.459, radius 25 km (config/cities.json) |
| Languages | locales `[6,20,21,22,24,41]` |
| Drive folder | "coating ads" `13LuHTwYqAJN7A1EPb9WawaIAdReiNxKA` (same pics as Setapak Broad 4) |
| Copy | "Ad text" `1e4C5QzUuCRH18sUVZRGjh-vanKDITXnyTNv6bFDgKMo`, updated by user 2026-11-19 to the Setia Alam address/Waze/Maps; wa.me/+60182971838; Jarmy ☎ line removed by user |

## Media
Same files and image_hash values as `2026-11-18-setapak-coating.md`, uploaded privately into this account (rupload).

## Ads
| Ad | Ad ID |
|---|---|
| Coating Pic 1 / BM | 120256170566940119 |
| Coating Pic 2 / BM | 120256170624270119 |
| Coating Pic 3 / BM | 120256170628360119 |
| Coating Pic 4 / BM | 120256170630340119 |
| Coating Pic 5 / BM | 120256170631080119 |
| Coating Pic 6 / CN | 120256170632150119 |
| Coating Pic 7 / CN | 120256170634640119 |
| Coating Pic 8 / CN | 120256170637710119 |
| Coating Pic 9 / CN | 120256170639210119 |
| Coating Pic 10 / CN | 120256170642300119 |

Creative: `link_data` (image_hash, `https://fb.com/messenger_doc/`, MESSAGE_PAGE) + `asset_feed_spec` DOF_MESSAGING_DESTINATION + text_optimizations OPT_OUT.

## Status
Published 2026-11-19 on user "publish". Broad 2 + 10 ads ACTIVE, effective IN_PROCESS (Meta review).

## Issues / lessons
- `ads_duplicate_entity` is **not available** on the Setia Alam account.
- `ads_create_ad_set` now works for Malaysia: create with `destination_type: MESSENGER`, then `ads_update_entity` with
  `regional_regulated_categories: ["MALAYSIA_UNIVERSAL"]` + `regional_regulation_identities: {universal_beneficiary/payer: "941546658825565"}` (clears "Advertiser is missing" 3858634),
  then `destination_type: MESSAGING_MESSENGER_WHATSAPP` (not accepted at create time, accepted as an update).
- Ads Manager had an unrelated staged edit (made by the user ~10:23 MYT) on live ad Nov Pic 9 | CN (`120256142186120119`); left untouched, publish used explicit `object_ids`.
