# Caption file template

Make a Google Doc named **`caption`**. Put it inside the pic/video folder, or right next to it in the parent folder.
Claude reads it and uses the text **exactly as written**, with the same emojis, line breaks, links and phone numbers. Meta's automatic text rewriting is switched off.

## Format A: one section per content (recommended)
Each pic/video gets its own headline and caption. The section name must match the file number.

```
[pic1]
headline: Tint Kereta RM299 Satu Kereta
caption:
🔥 RM299 BOLEH TINTED SATU KERETA?!
✅ IRR 95% tahan haba
✅ 5 tahun warranty
📲 Klik button di bawah untuk BOOK SLOT!

[pic2]
headline: Car Tint from RM299
caption:
🚗 Beat the heat with G3 Pro Tint!
✅ 95% heat rejection, 5-year warranty
📲 Message us to book your slot!

[video1]
headline: 汽车隔热膜 RM299 全车
caption:
🔥 RM299 全车贴膜！
✅ 95% 隔热，5年保修
📲 点击下方按钮预约！
```

- `lang: BM` / `lang: EN` / `lang: CN` is optional. Without it, Claude works out the language from the text. It's only used for the ad name, e.g. `pic1 | BM`.
- Any pic/video without a section is flagged in the check table. Nothing gets made up.

## Format B: one section per language (shared text)
Use this when several pics/videos share the same text.

```
[BM]
headline: ...
primary_text:
...

[EN]
headline: ...
primary_text:
...

[CN]
headline: ...
primary_text:
...

assign: pic1=BM, pic2=CN, video1=EN
```
Creatives not listed in `assign:` rotate BM → EN → CN.

## File names in the folder
`pic1.jpg … pic10.jpg` and/or `video1.mp4 … video10.mp4`. Names like `Pic 1 promo.jpg` or `Video 1 RM99.mp4` also work. The number is what matters.
Optional video thumbnail: `video1-cover.jpg` or `Video 1 封面.jpg`.
