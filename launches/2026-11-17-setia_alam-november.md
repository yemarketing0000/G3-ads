# Launch: G3 Setia Alam, "november" pics, 2026-11-17

| Item | Value |
|---|---|
| Account / Page | G3 Setia alam 1097140736142240 / G3 PRO TINT Setia Alam 1327702287093457 |
| Campaign | Setia alam 31/10 `120255848421820119` (existing, ACTIVE, CBO RM500/day, kept as is) |
| Ad set | Broad `120255848421800119` (existing, Messenger + WhatsApp, MY, locales 6/20/21/22/24/41, Advantage+ audience; kept as is) |
| Drive folder | G3—Joel / november `14WQM0txLdypT9KnSdtRRkvHP5JRmwHko` + doc "ad text" `1p6Usx3Rknztn1jqDlKaGA67QGnjkRDXuydvU-4_hW4I` |
| Copy | Pic 1–5 = BM, Pic 6–10 = CN, exactly as in the doc. Text optimisations OPT_OUT |
| AI disclosure | User said "No". The inline creative doesn't accept `self_ai_disclosure`, so it was left unset |

## Media (Drive → Meta, private: Drive download → rupload.facebook.com, no Drive sharing)
| Pic | Drive file | Meta image_hash | Draft ad ID |
|---|---|---|---|
| 1 | 1CABVNIZErq6wuZdTiqk17_H4Edup8Kou | db06aa9a5e0c1a7ddfaf34389f6b3408 | 120256142137660119 |
| 2 | 1WN_E2XKXMz7sGb5Z3aVOjse9ENfFH6VW | 3675d96bf286c44cd4e526378f3959d2 | 120256142164810119 |
| 3 | 10_ANeRgKEYmT42plBvNmlmPaRfZe3GkM | 657520adcd669c21f42d6815cc60e507 | 120256142167500119 |
| 4 | 1HcvdAn3UfFdsMMnxeXP7Lf10dglwp55j | 9f693a2fccfbb49a8ae77d7c5da9de89 | 120256142170270119 |
| 5 | 1ySrHdj__5DxKKriQVrO8d6Z9pWsdBJ7C | c6379b5d6b21cd8e07fc91337f7429a4 | 120256142173590119 |
| 6 | 1hDfK_dGqJ-NZAzD1mYAjy3dpSTjl4qp2 | 47726e4bc3f8e66a25c200a69ca7a390 | 120256142177510119 |
| 7 | 1B1Eg-nMKj1_pBV_My1kV6V4eNtQMqEoa | 74f1b58a497ae34fc2786da639bd8b8c | 120256142179730119 |
| 8 | 1bcSYbi9vjCPyU_-RNcETqhB2zODjLIUa | 4e0c83935cd42de11f05ea37fb5b76ed | 120256142183040119 |
| 9 | 1j4k6yOfhAErDUefCE-QVjEfY32DmQOKu | 67fc3a43c9f82c3699aba2a2c4e351a8 | 120256142186120119 |
| 10 | 1QfbqQeOUzeH6o1XXjHW3iNN2Ai7wc7Rw | dcb49af8123f4bfecc0bd7a462387c80 | 120256142193650119 |

Status: all 10 are DRAFT, validation_status VALIDATED, active_errors []. **Waiting for the user's "publish".**

## Learned this launch
- Private media path works: `Google_Drive download_file_content` saves the file to disk → `ads_create_upload_url` + curl to rupload.facebook.com (host added to the "G3 ads" environment) → `ads_finalize_upload`. No Drive link-sharing needed.
- Picture ads: inline `link_data` with `link: https://fb.com/messenger_doc/` + `MESSAGE_PAGE/MESSENGER` CTA + the `asset_feed_spec` DOF_MESSAGING_DESTINATION works for Messenger + WhatsApp ad sets.
- `self_ai_disclosure` is rejected inside an inline creative.
- The account now creates DRAFTS. Publishing = `ads_activate_entity` with `object_ids` of the drafts.
