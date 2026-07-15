# Phiếu học tập 07 — Laravel Route & Controller

> **Gắn với:** Buổi 6  
> **Repo gợi ý:** `cse485-phieu-07-laravel-pages`

---

## 1. Tóm tắt lý thuyết

- Laravel = MVC framework; `routes/web.php` khai báo URL.
- `php artisan make:controller`; action trả về `view()`.
- `php artisan serve` chạy môi trường dev.
- Đặt tên route `->name()` để dùng `route('...')`.

---

## 2. Bài tập thực hành

### Yêu cầu

1. Tạo project Laravel (commit source; có thể dùng `.gitignore` chuẩn Laravel — **không** commit `vendor/` nếu README hướng dẫn `composer install`).
2. Tạo `PageController` với 3 method: `home`, `about`, `contact`.
3. Đăng ký 3 route tĩnh:
   - `GET /` → home  
   - `GET /about` → about  
   - `GET /contact` → contact  
4. Mỗi route trả view riêng: `home.blade.php`, `about.blade.php`, `contact.blade.php` (nội dung tiêu đề khác nhau, có menu link 3 trang).

### Input / Output

| URL | Output tối thiểu |
|-----|------------------|
| `/` | Chữ **Trang chu** (hoặc Home) |
| `/about` | Chữ **Gioi thieu** |
| `/contact` | Chữ **Lien he** |

Chạy `php artisan route:list` trong video (được cộng điểm thuyết trình).

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 07                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG Laravel + README cài đặt               ║
║  2. Video OBS + camera mặt: artisan serve, mở 3 URL,         ║
║     chỉ routes/web.php + PageController                      ║
║  3. Nộp link Repo + Drive                                    ║
╚══════════════════════════════════════════════════════════════╝
```
