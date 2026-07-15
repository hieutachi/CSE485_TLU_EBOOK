# Phiếu 08 — Migration & Seeder: đúng 2 bảng MiniShop

| | |
|---|---|
| **Buổi** | 8 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-08` (tiếp Laravel) |
| **Nhận từ Phiếu 07** | Layout Admin |
| **Mang sang Phiếu 09** | Schema + dữ liệu seed sẵn để CRUD Eloquent |

---

## 1. Tóm tắt lý thuyết

- `.env` MySQL; Migration = schema có version.
- FK `foreignId()->constrained()`.
- Seeder + Faker; thứ tự categories trước products.

---

## 2. Bài trên lớp (~20') — Schema & migrate

### Cấu hình

DB name gợi ý: `minishop_laravel`.  
Chạy được `php artisan migrate` không lỗi.

### Bảng `categories`

| cột | quy ước |
|-----|---------|
| id | bigIncrements |
| name | string 100, unique |
| description | text nullable |
| timestamps | yes |

### Bảng `products`

| cột | quy ước |
|-----|---------|
| id | bigIncrements |
| category_id | FK → categories.id, cascadeOnDelete |
| sku | string 30, unique |
| name | string 150 |
| price | unsignedInteger |
| qty | unsignedInteger default 0 |
| description | text nullable |
| timestamps | yes |

**Checkpoint trên lớp:** `migrate` OK; phpMyAdmin / `db:table` thấy FK.

---

## 3. Bài về nhà (~30') — Seeder “đúng chuỗi MiniShop” + random

### Nhiệm vụ A — CategorySeeder (cố định)

Seed đúng 3 danh mục Phiếu 01 (name + description hợp lý).  
**Không** random name category ở bước này (để đồng bộ đề tài).

### Nhiệm vụ B — ProductSeeder

1. Seed **đúng 8 sản phẩm** khớp SKU/price/qty/category từ [CANONICAL-DATA](./CANONICAL-DATA.md) (map `category_id` **theo name**, không hard-code id nếu không ép id).  
2. **Thêm** 20 product random bằng Faker (sku unique, price 100000–9000000, qty 1–15, thuộc 1 trong 3 category).  
→ Tổng products sau seed = **28**.

> **Autograder:** chấm `count=3/28` + đủ 8 SKU gốc.  
> **Không** chấm `SUM(price*qty)` của cả 28 (Faker làm số này không ổn định).  
> Giá trị **41380000** chỉ đúng khi tính trên 8 SKU CORE.

### Nhiệm vụ C — DatabaseSeeder gọi đúng thứ tự

`CategorySeeder` rồi `ProductSeeder`.

### Nhiệm vụ D — Lệnh nghiệm thu ghi README

```bash
php artisan migrate:fresh --seed
php artisan tinker --execute="echo App\Models\Category::count().' '.App\Models\Product::count();"
# Kỳ vọng: 3 28
```

### Nhiệm vụ E — Model `$fillable` sẵn

`Category`, `Product` có `$fillable` đủ cột (quan hệ viết signature ở Phiếu 10 cũng được, nhưng khuyến nghị khai báo sớm).

---

## 4. Output bắt buộc

| Kiểm tra | Giá trị |
|----------|---------|
| categories | 3 |
| products | 28 |
| Tồn tại SKU | `KB-01`, `MN-02`, … đủ 8 SKU gốc |
| Xóa 1 category (thử local) | products thuộc category đó mất theo cascade (có thể thử rồi seed lại) |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 08                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo: migrations + seeders + Models fillable             ║
║  2. Video: migrate:fresh --seed + đếm 3 và 28                ║
║  3. Mở migration products chỉ dòng foreignId                 ║
╚══════════════════════════════════════════════════════════════╝
```
