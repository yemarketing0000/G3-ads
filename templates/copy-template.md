# Copy file template

Put a Google Doc or `.txt` named **`copy`** (or anything with "copy", "caption" or "text" in the title) inside each campaign folder in Drive.
Write it exactly like the example below. Claude reads it and uses the text **as written**. It does not rewrite your copy.

```
[BM]
headline: Tint Kereta Premium dari RM99
primary_text:
🚗 Panas terik? Pasang tint G3 Pro sekarang!
✅ Tolak haba sehingga 80%
✅ Waranti 7 tahun
📍 G3 Pro Tint Setapak
WhatsApp kami untuk sebut harga percuma 👇

[EN]
headline: Premium Car Tint from RM99
primary_text:
🚗 Beat the heat with G3 Pro Tint!
✅ Up to 80% heat rejection
✅ 7-year warranty
📍 G3 Pro Tint Setapak
WhatsApp us for a free quote 👇

[CN]
headline: 高级汽车隔热膜 RM99起
primary_text:
🚗 天气太热？马上来 G3 Pro Tint 贴膜！
✅ 高达80%隔热
✅ 7年保修
📍 G3 Pro Tint Setapak
WhatsApp 我们免费报价 👇

assign: video1=BM, video2=CN, video3=EN
```

Rules:
- `[BM]`, `[EN]` and `[CN]` sections. Leave a language out only if no creative uses it.
- `headline:` is one line. Keep it short (about 40 characters or less).
- `primary_text:` can run over several lines, up to the next `headline:`, `[SECTION]` or `assign:`.
- `assign:` is optional. Any creative not listed gets a language in rotation BM → EN → CN by number.
- File names in the folder: `pic1.jpg … pic10.jpg` or `video1.mp4 … video10.mp4`. Names like `Video 1 RM99.mp4` also work.
- Optional video thumbnail: `video1-cover.jpg` or `Video 1 封面.jpg`.
