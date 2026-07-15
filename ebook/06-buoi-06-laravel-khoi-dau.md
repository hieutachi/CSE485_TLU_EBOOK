# BUỔI 6: Khởi đầu với Laravel Framework

> **Thời lượng gợi ý:** 3–4 giờ  
> **Tiên quyết:** Buổi 5 (MVC mini)  
> **Kết quả đầu ra:** Cài Laravel bằng Composer; hiểu cấu trúc thư mục; tạo Route + Controller cơ bản  
> **Phiếu học tập liên quan:** Phiếu 7

---

## 1. Mục tiêu buổi học

1. Giải thích Laravel giải quyết gì so với PHP thuần.
2. Cài đặt project Laravel thành công.
3. Đọc được cấu trúc thư mục quan trọng.
4. Tạo Route và Controller; hiểu vòng đời Request.

---

## 2. Laravel là gì?

Laravel = framework PHP theo MVC, cung cấp sẵn:

- Routing đẹp (`/products` thay vì `index.php?controller=...`)
- Blade template
- Eloquent ORM
- Migration / Seeder
- Validation, Middleware, Auth…

```
Bạn đã tự build (Buổi 5)     Laravel làm sẵn
─────────────────────────     ────────────────
Front controller              public/index.php + RouteServiceProvider
Controller thủ công           app/Http/Controllers
Model PDO                     Eloquent Models
View PHP                      Blade
URL ?controller&action        routes/web.php
```

---

## 3. Cài đặt

### 3.1. Yêu cầu

- PHP 8.2+ (khuyến nghị; kiểm tra `php -v`)
- Composer: https://getcomposer.org/
- MySQL (XAMPP) — cấu hình sâu ở Buổi 8
- Extension PHP: `openssl`, `pdo`, `mbstring`, `tokenizer`, `xml`, `ctype`, `json`, `bcmath`

### 3.2. Tạo project

```bash
composer create-project laravel/laravel cse485-laravel
cd cse485-laravel
php artisan serve
```

Mở: `http://127.0.0.1:8000` → thấy trang chào Laravel.

> `php artisan` = CLI của Laravel (giống “bảng điều khiển”).

---

## 4. Cấu trúc thư mục (chỉ cần nhớ phần lõi)

```
cse485-laravel/
├── app/
│   ├── Http/Controllers/     ← Controllers
│   └── Models/               ← Eloquent Models
├── bootstrap/
├── config/                   ← cấu hình framework
├── database/
│   ├── migrations/           ← schema (Buổi 8)
│   └── seeders/              ← dữ liệu mẫu (Buổi 8)
├── public/                   ← Document Root (index.php)
├── resources/views/          ← Blade (Buổi 7)
├── routes/
│   └── web.php               ← khai báo URL
├── storage/                  ← log, cache, upload
├── .env                      ← cấu hình môi trường (DB, APP_KEY…)
└── artisan
```

```
Browser
   │  http://127.0.0.1:8000/about
   ▼
public/index.php
   │
   ▼
Kernel / Middleware
   │
   ▼
routes/web.php  ──► khớp URL
   │
   ▼
Controller@method
   │
   ▼
View / JSON Response
```

---

## 5. Vòng đời Request (ASCII bắt buộc)

```
┌──────────┐   HTTP    ┌────────────────┐
│  Client  │ ────────► │ public/index.php│
└──────────┘           └────────┬───────┘
                                │
                    ┌───────────▼───────────┐
                    │  Bootstrap Laravel    │
                    │  Load .env, config    │
                    └───────────┬───────────┘
                                │
                    ┌───────────▼───────────┐
                    │  Middleware stack     │
                    │  (CSRF, session…)     │
                    └───────────┬───────────┘
                                │
                    ┌───────────▼───────────┐
                    │  Router               │
                    │  tìm Route khớp       │
                    └───────────┬───────────┘
                                │
                    ┌───────────▼───────────┐
                    │  Controller action    │
                    └───────────┬───────────┘
                                │
                    ┌───────────▼───────────┐
                    │  Response (HTML/JSON) │
                    └───────────────────────┘
```

---

## 6. Route cơ bản — `routes/web.php`

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\PageController;

// Closure — nhanh cho demo
Route::get('/', function () {
    return view('welcome');
});

Route::get('/hello', function () {
    return 'Xin chao Laravel';
});

// Tham số trên URL
Route::get('/students/{id}', function (string $id) {
    return "Student ID = {$id}";
});

// Trỏ vào Controller (khuyến nghị)
Route::get('/home', [PageController::class, 'home'])->name('home');
Route::get('/about', [PageController::class, 'about'])->name('about');
Route::get('/contact', [PageController::class, 'contact'])->name('contact');
```

| Method helper | Ý nghĩa |
|---------------|---------|
| `Route::get` | Đọc / hiển thị |
| `Route::post` | Gửi form tạo mới |
| `Route::put/patch` | Cập nhật |
| `Route::delete` | Xóa |

---

## 7. Tạo Controller

```bash
php artisan make:controller PageController
```

`app/Http/Controllers/PageController.php`:

```php
<?php

namespace App\Http\Controllers;

class PageController extends Controller
{
    public function home()
    {
        // Tạm trả chuỗi — Buổi 7 sẽ return view('home')
        return view('home');
    }

    public function about()
    {
        return view('about');
    }

    public function contact()
    {
        return view('contact');
    }
}
```

Tạo view tạm (Blade đơn giản):

`resources/views/home.blade.php`

```blade
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Home</title>
</head>
<body>
    <h1>Trang chu</h1>
    <nav>
        <a href="{{ route('home') }}">Home</a> |
        <a href="{{ route('about') }}">About</a> |
        <a href="{{ route('contact') }}">Contact</a>
    </nav>
</body>
</html>
```

Tương tự tạo `about.blade.php`, `contact.blade.php` (đổi tiêu đề).

Kiểm tra:

```
http://127.0.0.1:8000/home
http://127.0.0.1:8000/about
http://127.0.0.1:8000/contact
```

---

## 8. Artisan hữu ích Buổi 6

```bash
php artisan route:list          # liệt kê mọi route
php artisan make:controller X   # tạo controller
php artisan serve               # chạy server dev
php artisan about               # thông tin môi trường
```

---

## 9. Lỗi thường gặp

| Lỗi | Cách xử |
|-----|---------|
| `composer: command not found` | Cài Composer, mở lại terminal |
| Trang 404 | Sai path / chưa `route:list` kiểm tra |
| `View [home] not found` | Thiếu file trong `resources/views` |
| Cổng 8000 bận | `php artisan serve --port=8080` |

---

## 10. Checklist

- [ ] `composer create-project` thành công
- [ ] `php artisan serve` mở được trang welcome
- [ ] 3 route `/home` `/about` `/contact` chạy qua `PageController`
- [ ] Giải thích được vòng đời Request bằng sơ đồ

**Cầu nối Buổi 7:** Ba trang đang lặp HTML nav — Blade Layout (`@extends`, `@section`, `@yield`) sẽ gom bố cục Admin Dashboard.

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 6/12*
