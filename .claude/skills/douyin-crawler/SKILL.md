---
name: douyin-crawler
description: >
  Crawl một trang Douyin bất kỳ (jingxuan, user profile, hoặc URL detail có modal_id)
  và trả về JSON list các video với aweme_id, detail_url, author, title.
  Dùng skill này khi người dùng cung cấp link Douyin và muốn lấy danh sách video,
  lấy link clip, hoặc muốn biết có bao nhiêu video trên một trang/profile.
  Cũng dùng khi người dùng paste link https://www.douyin.com/... và muốn crawl,
  extract, hoặc liệt kê video — ngay cả khi họ không dùng từ "crawl".
---

# Douyin Crawler Skill

Crawl trang Douyin → JSON list video.

## Cấu trúc project

```
douyin-downloader/
├── .claude/skills/douyin-crawler/SKILL.md
├── douyin/
│   ├── .venv/           ← virtualenv
│   ├── __init__.py
│   ├── __main__.py      ← entry point
│   ├── cli.py           ← CLI commands
│   ├── crawler.py       ← Playwright crawler
│   ├── downloader.py    ← async HTTP downloader
│   ├── store.py         ← index.csv read/write
│   ├── transcribe.py    ← faster-whisper
│   ├── pyproject.toml
│   └── requirements.txt
├── downloads/
└── index.csv
```

## Cách chạy

```bash
douyin/.venv/bin/python3 -m douyin <command> [options]
```

### 1. Feed page (jingxuan, channel, category)

```bash
douyin/.venv/bin/python3 -m douyin jingxuan --url "<URL>" --dry-run --max 100 --no-headless
```

### 2. User profile

```bash
douyin/.venv/bin/python3 -m douyin user "<URL>" --dry-run --max 200
```

### 3. Video detail / link trực tiếp

```bash
# Chỉ xem info (dry-run):
douyin/.venv/bin/python3 -m douyin dl "<URL>" --dry-run

# Tải luôn:
douyin/.venv/bin/python3 -m douyin dl "<URL>"
```

## Output format

Sau khi lấy được danh sách, trình bày cho người dùng dưới dạng JSON:

```json
[
  {
    "aweme_id": "7548734306391215395",
    "detail_url": "https://www.douyin.com/video/7548734306391215395",
    "author": "秋芝2046",
    "title": "教你10分钟打造专属AI客服..."
  }
]
```

`detail_url` luôn có dạng `https://www.douyin.com/video/{aweme_id}`.

## Lưu ý

- Nếu output là `0 video`, thêm `--no-headless` để browser hiện ra — có thể cần login hoặc vượt captcha.
- Feed page Douyin thường giới hạn ~50 video/trang dù đặt `--max` cao hơn.
- `index.csv` ở root track trạng thái (pending/success/failed) và caption whisper của từng video.
