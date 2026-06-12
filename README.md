# Douyin Downloader

Tải video không watermark từ Douyin bằng Playwright API interception.

## Cách hoạt động

1. Playwright mở browser, load trang Douyin
2. Intercept các HTTP response từ feed API (`/aweme/v1/web/...`)
3. Parse `aweme_list` → trích xuất `play_addr` (URL không watermark)
4. `aiohttp` + `aiofiles` tải video song song

Không cần reverse-engineer X-Bogus vì chạy browser thật — Douyin tự tạo signature.

## Cài đặt

```bash
cd douyin-downloader

# Dùng poetry
poetry install
poetry run playwright install chromium

# Hoặc pip
pip install playwright aiohttp aiofiles rich click
playwright install chromium
```

## Sử dụng

### Tải từ trang Tinh Tuyển (/jingxuan)

```bash
python main.py jingxuan
python main.py jingxuan --max 100 --out ./videos
python main.py jingxuan --dry-run          # chỉ xem danh sách, không tải
python main.py jingxuan --no-headless      # hiện browser để debug
```

### Tải từ profile người dùng

```bash
python main.py user "https://www.douyin.com/user/MS4wLjABAAAA..."
python main.py user "https://www.douyin.com/user/xxx" --max 200 --out ./user_videos
```

## Options

| Option | Mặc định | Mô tả |
|--------|---------|-------|
| `--max` | 50/100 | Số video tối đa |
| `--scroll` | 10 | Số lần scroll (jingxuan) |
| `--out` | downloads/ | Thư mục lưu |
| `--concurrency` | 4 | Download song song |
| `--cookies` | - | File cookies JSON |
| `--no-headless` | - | Hiện browser |
| `--dry-run` | - | Chỉ liệt kê |

## Nếu không tìm được video

Douyin yêu cầu đăng nhập để xem một số nội dung. Cách lấy cookies:

1. Chạy với `--no-headless` để browser hiện ra
2. Đăng nhập thủ công vào Douyin
3. Export cookies bằng extension [Cookie-Editor](https://cookie-editor.com/) → lưu thành `cookies.json`
4. Chạy lại với `--cookies cookies.json`

## Cấu trúc file tải về

```
downloads/
└── jingxuan/
    └── {author}__{title}__{aweme_id}.mp4
```

## Lưu ý

- Tool này chỉ dành cho mục đích cá nhân / nghiên cứu
- Tuân thủ ToS của Douyin khi sử dụng
- Không dùng để tải hàng loạt ở scale lớn
