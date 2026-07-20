# Phiếu 06 — Laravel khởi động MiniShop (Route & Controller)

| | |
|---|---|
| **Buổi** | 6 — đọc kèm [Buổi 06](../06-buoi-06-laravel-khoi-dau.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-06` |
| **Nhận từ Phiếu 05** | Đã hiểu Front Controller + Controller + View |
| **Mang sang Phiếu 07** | 4 route Admin sẽ gắn **Blade layout** |
| **Stats giả (CORE)** | categories **3** / products **8** / inventory **41380000** |

### Chuẩn đầu ra (CLO)

1. Tạo được project Laravel; giải thích `public/` là document root.  
2. Có 4 **named routes** admin đúng URI.  
3. Dashboard in đúng stats giả CORE (chuỗi MiniShop).  
4. README cài đặt; **không** commit `.env`.

---

## 1. Lý thuyết — Laravel đứng ở đâu trong chuỗi?

### 1.1. Từ MVC mini → framework

```
P05: tự viết router ?controller=&action=
P06: routes/web.php + php artisan + Controller class
P07: Blade layout
P08: Migration = schema trong Git
…
P12: CRUD Dashboard đủ 2 bảng
```

Laravel **không thay** kiến thức MVC — nó làm sẵn phần lặp lại (routing, template, ORM…).

### 1.2. Vòng đời request (ASCII nhớ)

```
Browser GET /admin
    → public/index.php
    → bootstrap + middleware
    → routes/web.php khớp URI
    → DashboardController@index
    → return view(...)
    → HTML về browser
```

### 1.3. Named route — vì sao bắt buộc?

```php
route('admin.products.index')  // → /admin/products
```

Đổi URL một chỗ trong `web.php` — mọi link menu không gãy.  
**Cấm** hardcode `/admin/products` rải khắp view nếu đã có `name()`.

### 1.4. Stats giả vs stats thật

| Phiếu | Stats |
|-------|-------|
| **P06** | Hardcode `3 / 8 / 41380000` — nhớ chuỗi CORE |
| **P11+** | `Category::count()`, `Product::count()`, `SUM(price*qty)` sống |

Sau seed Faker (P08 = 28 SP), tổng kho **không** còn 41380000 — đó là bình thường.

### 1.5. Artisan cần thuộc P06

```bash
composer create-project laravel/laravel minishop-laravel
php artisan serve
php artisan make:controller Admin/DashboardController
php artisan route:list
php artisan key:generate
```

---

## 2. Bài trên lớp (~20') — 4 trang khung

### Tạo project + controller

```bash
composer create-project laravel/laravel minishop-laravel
cd minishop-laravel
php artisan make:controller Admin/DashboardController
php artisan make:controller Admin/CategoryController
php artisan make:controller Admin/ProductController
php artisan serve
```

### Route bắt buộc

| Method | URI | name | Action |
|--------|-----|------|--------|
| GET | `/admin` | `admin.dashboard` | DashboardController@index |
| GET | `/admin/categories` | `admin.categories.index` | CategoryController@index |
| GET | `/admin/products` | `admin.products.index` | ProductController@index |
| GET | `/admin/about` | `admin.about` | DashboardController@about |

### View tạm — tiêu đề exact

- Dashboard: `MiniShop Admin — Dashboard`
- Categories: `MiniShop Admin — Categories (sap xay dung)`
- Products: `MiniShop Admin — Products (sap xay dung)`
- About: 3–4 câu mô tả chuỗi tới P12

Menu dùng `route('...')`.

**Checkpoint:** `php artisan route:list` thấy đủ 4 name.

---

## 3. Bài về nhà (~30')

### A — Stats trên Dashboard

```php
$stats = [
    'categories' => 3,
    'products' => 8,
    'inventory_value' => 41380000,
];
return view('admin.dashboard', compact('stats'));
```

In rõ 3 số trên trang (có thể thêm `data-testid` nếu muốn sẵn autograder).

### B — README vận hành

```text
## Cai dat
- PHP version …
- composer install
- cp .env.example .env && php artisan key:generate
- php artisan serve
## Tien do MiniShop
- [x] Route admin
- [ ] Blade layout (P07)
- [ ] Migration 2 bang (P08)
- [ ] CRUD 2 bang (P12)
```

### C — `docs/request-lifecycle.md`

ASCII: Browser → `public/index.php` → Route → Controller → View cho `/admin/products`.

### D — Repo sạch

`.gitignore` chuẩn Laravel; **không** commit `.env` / `vendor` (nếu ignore vendor thì README phải có `composer install`).

---

## 4. Lỗi thường gặp

| Lỗi | Cách xử |
|-----|---------|
| `composer not found` | Cài Composer, mở lại terminal |
| 404 /admin | Sai `web.php` / chưa `route:list` |
| View not found | Thiếu file trong `resources/views` |
| Port 8000 bận | `php artisan serve --port=8080` |

---

## 5. EXPECT + nộp bài

| Key | Value |
|-----|------:|
| 4 route names | đủ bảng mục 2 |
| stats | 3 / 8 / 41380000 |

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 06                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo cse485-ms-06 + README cài đặt                       ║
║  2. Video: serve + 4 URL + route:list + zoom stats           ║
║  3. Giải thích: named route khác gì gõ URL cứng? Lifecycle?  ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 07:** gộp 4 trang vào **một layout Admin** Blade (`@extends` / `@yield`).
