# Phiếu 06 — Laravel khởi động MiniShop (Route & Controller)

| | |
|---|---|
| **Buổi** | 6 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-06` |
| **Nhận từ Phiếu 05** | Map MVC mini ↔ Laravel |
| **Mang sang Phiếu 07** | Các route Admin sẽ gắn layout Blade |

### Chuẩn đầu ra (CLO)

1. 4 named routes: `admin.dashboard|categories.index|products.index|about`.  
2. Dashboard in đúng stats giả CORE: `3 / 8 / 41380000`.  
3. README cài đặt + không commit `.env`.

---

## 1. Tóm tắt lý thuyết

- `composer create-project`, `php artisan serve`, `route:list`.
- `routes/web.php` → Controller → `view()`.
- `public/` là document root; vòng đời Request Laravel.

---

## 2. Bài trên lớp (~20') — Project + 4 trang khung

### Tạo project

```bash
composer create-project laravel/laravel minishop-laravel
cd minishop-laravel
php artisan serve
```

### Controller

```bash
php artisan make:controller Admin/DashboardController
php artisan make:controller Admin/CategoryController
php artisan make:controller Admin/ProductController
```

### Route bắt buộc (đặt tên)

| Method | URI | name | Action |
|--------|-----|------|--------|
| GET | `/admin` | `admin.dashboard` | DashboardController@index |
| GET | `/admin/categories` | `admin.categories.index` | CategoryController@index |
| GET | `/admin/products` | `admin.products.index` | ProductController@index |
| GET | `/admin/about` | `admin.about` | DashboardController@about |

### View tạm (trên lớp)

Mỗi trang hiện tiêu đề rõ:

- Dashboard: `MiniShop Admin — Dashboard`
- Categories: `MiniShop Admin — Categories (sap xay dung)`
- Products: `MiniShop Admin — Products (sap xay dung)`
- About: đoạn 3–4 câu mô tả chuỗi MiniShop (bạn đang xây gì tới buổi 12).

Menu link dùng `route('...')` (không hardcode path trong mọi chỗ).

**Checkpoint:** `php artisan route:list` thấy đủ 4 route name.

---

## 3. Bài về nhà (~25–30') — “Contract” dự án & truyền dữ liệu giả

### Nhiệm vụ A — Truyền thống kê giả xuống Dashboard

Trong `DashboardController@index` tạo mảng:

```php
$stats = [
    'categories' => 3,
    'products' => 8,
    'inventory_value' => 41380000, // nhớ chuỗi Phiếu 01
];
```

View dashboard in 3 số này trong các thẻ/đoạn văn (chưa cần CSS đẹp).

### Nhiệm vụ B — README vận hành (chấm)

```text
## Cai dat
- PHP version ...
- composer install
- cp .env.example .env && php artisan key:generate
- php artisan serve
## Tai khoan
(chua co Auth — ghi ro)
## Tien do MiniShop
- [x] Route admin
- [ ] Blade layout (Phieu 07)
- [ ] Migration 2 bang (Phieu 08)
- [ ] CRUD 2 bang (Phieu 12)
```

### Nhiệm vụ C — Sơ đồ tự vẽ trong `docs/request-lifecycle.md`

ASCII: Browser → `public/index.php` → Route → Controller → View cho URL `/admin/products`.

### Nhiệm vụ D — Không commit secrets

- Có `.gitignore` chuẩn Laravel.
- **Không** commit `.env`.
- README nhắc tạo DB sẽ làm ở Phiếu 08.

---

## 4. Output nghiệm thu

| Hạng mục | OK khi |
|----------|--------|
| 4 URL mở được | 200, đúng tiêu đề |
| `route:list` | Có 4 name admin.* |
| Dashboard | Hiện stats 3 / 8 / 41380000 |
| Repo sạch | Không `.env`, có README |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 06                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo Laravel (vendor co the ignore — README ghi install) ║
║  2. Video: serve + mở 4 URL + route:list + zoom stats         ║
║  3. Chỉ docs/request-lifecycle.md 45s                        ║
╚══════════════════════════════════════════════════════════════╝
```
