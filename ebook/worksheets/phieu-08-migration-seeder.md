# Phiếu 08 — Migration & Seeder: 2 bảng MiniShop

| | |
|---|---|
| **Buổi** | 8 — đọc kèm [Buổi 08](../08-buoi-08-migration-seeder.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-08` |
| **Nhận từ Phiếu 07** | Layout Admin |
| **Mang sang Phiếu 09** | Schema + seed để Eloquent CRUD |
| **DB** | `minishop_laravel` |

### Chuẩn đầu ra (CLO)

1. Giải thích Migration = “schema có version trong Git”.  
2. Tạo `categories` + `products` có FK `cascadeOnDelete` + `sku` unique.  
3. Seed: **3** DM cố định + **8** SKU CORE + **20** Faker = **28** products.  
4. Model `$fillable` đủ cột; README lệnh `migrate:fresh --seed`.

---

## 1. Lý thuyết — vì sao không tạo bảng bằng tay mãi?

### 1.1. phpMyAdmin vs Migration

| Cách | Nhược điểm / ưu điểm |
|------|----------------------|
| Click phpMyAdmin | Khó đồng bộ máy bạn bè / CI |
| **Migration** | File trong Git → `php artisan migrate` giống nhau mọi máy |

```
database/migrations/xxxx_create_categories_table.php
database/migrations/xxxx_create_products_table.php  ← sau categories (FK)
```

### 1.2. Seeder vs dữ liệu tay

Seeder + Faker → demo không phụ thuộc gõ 28 dòng tay.  
**CORE 8 SKU** vẫn cố định theo CANONICAL-DATA (để chấm ổn định).

### 1.3. Khóa ngoại — hiểu bằng hình

```
categories.id  ◄──── products.category_id
     1                    nhiều
```

`cascadeOnDelete`: xóa DM → xóa SP thuộc DM (cẩn thận khi demo).

### 1.4. Autograder — chấm gì / không chấm gì

| Chấm | Không chấm cứng |
|------|-----------------|
| `Category::count()=3` | `SUM(price*qty)` cả 28 (Faker) |
| `Product::count()=28` | |
| Đủ 8 SKU CORE | |
| `inventory_value` CORE 41380000 chỉ khi lọc 8 SKU | |

### 1.5. `$fillable`

Chống mass assignment: chỉ cột trong `$fillable` mới gán hàng loạt qua `create([...])`.

---

## 2. Bài trên lớp (~20') — Schema & migrate

### `.env`

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=minishop_laravel
DB_USERNAME=root
DB_PASSWORD=
```

Tạo DB trống `minishop_laravel` trước.

### Bảng `categories`

`id`, `name` string 100 **unique**, `description` text nullable, `timestamps`.

### Bảng `products`

`id`, `category_id` FK cascade, `sku` string 30 **unique**, `name`, `price` unsignedInt, `qty` unsignedInt default 0, `description` nullable, `timestamps`.

```bash
php artisan migrate
```

**Checkpoint:** thấy FK trong DB / `db:show`.

---

## 3. Bài về nhà (~30') — Seeder

### A — CategorySeeder

Đúng 3 tên: Ban phim, Chuot, Man hinh (+ description CANONICAL). **Không** random name DM.

### B — ProductSeeder

1. Insert đúng 8 SP CORE (map `category_id` **theo name**).  
2. Thêm 20 Faker: sku unique, price 100000–9000000, qty 1–15.  
→ Tổng **28**.

### C — `DatabaseSeeder` gọi Category rồi Product

### D — Nghiệm thu

```bash
php artisan migrate:fresh --seed
php artisan tinker --execute="echo App\Models\Category::count().' '.App\Models\Product::count();"
# 3 28
```

### E — `$fillable` Category & Product (gồm `sku`, `qty`)

---

## 4. Lỗi thường gặp

| Lỗi | Cách xử |
|-----|---------|
| Cannot add foreign key | Migration products chạy trước categories |
| MassAssignmentException | Thiếu `$fillable` |
| Duplicate sku Faker | Dùng `$faker->unique()` |
| Sai DB name | `config:clear` sau sửa `.env` |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 08                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo: migrations + seeders + Models                      ║
║  2. Video: fresh --seed + đếm 3 và 28 + chứng minh 8 SKU     ║
║  3. Thuyết trình: Migration khác phpMyAdmin thế nào? FK?     ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 09:** CRUD bằng **Eloquent** (lab HTTP/JSON) trên 2 model.
