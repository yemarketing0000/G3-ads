# G3 Ads: Drive → Meta automation

Put your pics/videos and copy in Google Drive. Tell Claude which folder, budget and location.
Claude builds a Messages campaign (WhatsApp + Messenger) in Meta Ads Manager **as a draft**, shows it to you, and publishes only when you say so.

## 1. Set up a Drive folder (once per campaign)
```
G3 Ads/
  Setapak/
    2026-11 Nov Promo/
      video1.mp4 … video10.mp4   (or pic1.jpg … pic10.jpg)
      video1-cover.jpg           (optional thumbnail)
      copy                        (Google Doc, see templates/copy-template.md)
  Setia Alam/
    ...
```
Existing names like `Video 1 RM99.mp4` also work. The number after "pic" or "video" is what counts.

## 2. In Ads Manager (about 1 minute)
Meta only lets you set Malaysia's verified advertiser and the **Messenger + WhatsApp** destination in Ads Manager. So you:
1. Create (or **duplicate** the last) campaign → ad set **Broad** → **one ad** (any pic/video, text "test" is fine).
2. Turn the **campaign off** and click **Publish**.

## 3. Ask Claude
> Launch G3 Setapak, folder "2026-11 Nov Promo", campaign "Nov Promo" (already off in Ads Manager)

> Launch Setia Alam, folder "Nov Promo", campaign "SA Nov", Chinese only

## 4. What happens
| Step | What Claude does | You |
|---|---|---|
| 1 | Reads the folder and copy, then shows a **check table**: account, budget, location, languages, each ad + language + headline | Reply OK or ask for changes |
| 2 | Shares only those files by link so Meta can download them, then uploads to your ad account | – |
| 3 | Finds your **Broad** ad set, checks its settings, and adds one ad per pic/video with your copy. Button: **Send message**, and Meta picks WhatsApp or Messenger for each person | – |
| 4 | Shows draft IDs and **preview links**, plus any problem (e.g. WhatsApp not linked to the Page) | Say **"publish"** to go live, or leave it as a draft |
| 5 | Publishes, checks the status, saves a log in `launches/` | Turn Drive link-sharing back off if you like |

## Settings you may want to change
- `config/defaults.json`: naming pattern, language codes, objective.
- `config/cities.json`: add your own city pins (lat/long + km).
- `config/accounts.json`: add an Instagram account ID once one is linked to the ad accounts. None is linked today.
