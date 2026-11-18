# Launch: Setapak — Coating pics 1–10 (Broad 4), 2026-11-18

## Brief
| Item | Value |
|---|---|
| Account | G3 PRO TINT Setapak (`889018630434588`), Page `1277296285476730` |
| Campaign | Setapak 30/9 (`120253343450560526`), CBO RM500/day, shared with Broad 1 + Broad 3 (user: keep budget) |
| Ad set | **Broad 4** (`120254069841300526`) — made by Claude: duplicate of Broad 3, renamed |
| Destination | Messenger only (Setapak Page WhatsApp still disconnected) |
| Location | Setapak place key `281386432021532`, 16 mile radius (same as Broad 1 - pic) |
| Languages | locales `[6,20,21,22,24,41]` (EN, CN, BM) |
| Drive folder | "coating ads" `13LuHTwYqAJN7A1EPb9WawaIAdReiNxKA` |
| Copy | Drive doc "Ad text" `1e4C5QzUuCRH18sUVZRGjh-vanKDITXnyTNv6bFDgKMo`, Pic 1–5 BM, Pic 6–10 CN, used as written |

## Media (Drive → private download → rupload → Meta)
| Pic | Drive file | image_hash |
|---|---|---|
| 1 | 1_n-yn8JdhXO4uWQ8vQMcJqhRNOswa9M9 | e4afa4adcf05927a18c88ac2cfd2ae4d |
| 2 | 1Z2g9Sz-joXcEhfNp7aNU8Mq0Jq9RdRib | 0044bdf7b036fab85c917d36c0330f9f |
| 3 | 1XMi-e51VGjMHZKJxLMJA1G1TpteW3tTK | f662afc9bb9dd957a685409339372ace |
| 4 | 1cnbpF7AOvYM_V3D1KFlUDbPsY8BHRdRY | 9562cc00b7a4e651dbc48114d873f5e3 |
| 5 | 1cDd9ib_XsrSOt3m9OnR_kyRpMpMyzQgU | c367a7e7843d68550c1df4ddf4336cb6 |
| 6 | 1SljjCIP8HXZFI-lWbuOzk2gLex0A7Fhq | 75db6b3dff351f7ab2aaf63f1b54e465 |
| 7 | 1kf0_Fd-bGlSOOkb_sz9V0vhone5HWnqS | 57296c99f8c7aabbcbe7dd0e203cd1b0 |
| 8 | 1D5PL7WUfCcmAR_7bzdAGqH1Gqw-KnT3k | 37d0b1cc62a09a67f4f84ed8907b61ec |
| 9 | 1JImRcwwzVVNQysDbq69F2UW8FW19BcDF | c4d9a14f02c336d9a084c8d021ef8034 |
| 10 | 1Aevvkz5IWImjOPcGV61NeqzwAS2r9dwg | e2ae49338373bc3284eee03933df7bad |

## Ads
| Ad | Ad ID |
|---|---|
| Coating Pic 1 / BM | 120254069857380526 |
| Coating Pic 2 / BM | 120254069864780526 |
| Coating Pic 3 / BM | 120254069865810526 |
| Coating Pic 4 / BM | 120254069866350526 |
| Coating Pic 5 / BM | 120254069867150526 |
| Coating Pic 6 / CN | 120254069878510526 |
| Coating Pic 7 / CN | 120254069879840526 |
| Coating Pic 8 / CN | 120254069880860526 |
| Coating Pic 9 / CN | 120254069881500526 |
| Coating Pic 10 / CN | 120254069883570526 |

## Status
Published 2026-11-18 on user "published" (approval). Broad 4 + 10 ads ACTIVE, effective IN_PROCESS (Meta review). No drafts left.

## Issues / lessons
- **Ad set create blocked for Malaysia, but `ads_duplicate_entity` on an existing Broad ad set works** (account is in draft mode → staged copy). Copy inherits the verified advertiser.
- The duplicate **also copied all 11 ads** of the source ad set (Nov Pic 1–10 + "New Engagement Ad"). Removed with `discard_draft_changes` (preview → token → confirm) before publishing.
- Copied ad set kept `promoted_object.whatsapp_phone_number 60189801838` → error 4017029 "no access to the WhatsApp Business Phone Number". Fixed: `promoted_object = {page_id}` only.
- Copied `attribution_spec` 7-day click invalid for Messenger CONVERSATIONS → error 1885423. Fixed: 1-day click.
- Copy says "Klik WhatsApp … PM COATING" but the button opens Messenger (user accepted; wa.me link is in the text).
- Pic 2 and 7 show a BMW logo; Pic 5/9/10 use the "WINDOW FILM" logo variant.
