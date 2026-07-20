# Phiếu 10 — `belongsTo` / `hasMany` & chống N+1

| | |
|---|---|
| **Buổi** | 10 — đọc kèm [Buổi 10](../10-buoi-10-relationship.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-10` |
| **Nhận từ Phiếu 09** | Eloquent CRUD 2 model |
| **Mang sang Phiếu 11** | List Admin hiện **tên** category bằng quan hệ |

### Chuẩn đầu ra (CLO)

1. Khai báo đúng chiều `Category hasMany Product`, `Product belongsTo Category`.  
2. JSON list dùng `with('category')`; `withCount` tổng = **28**.  
3. Đo và giải thích N+1 trong `docs/n-plus-one.md`.  
4. Admin `/admin/products` bỏ fake data P07 — paginate + tên DM.

---

## 1. Lý thuyết — quan hệ & N+1

### 1.1. Quan hệ 1–n MiniShop

```
Category (1) ────────< Product (n)
   id                   category_id
```

| Method | Nghĩa | Ví dụ gọi |
|--------|-------|-----------|
| `hasMany` | Một DM có nhiều SP | `$category->products` |
| `belongsTo` | Một SP thuộc một DM | `$product->category` |

```php
// Category.php
public function products(): HasMany
{
    return $this->hasMany(Product::class);
}

// Product.php
public function category(): BelongsTo
{
    return $this->belongsTo(Category::class);
}
```

### 1.2. Lazy loading — tiện nhưng dễ chậm

```php
foreach (Product::all() as $p) {
    echo $p->category->name; // mỗi vòng có thể +1 query
}
// 1 + N queries  →  bài toán N+1
```

```
Query 1: SELECT * FROM products          ← 1
Query 2..N+1: SELECT * FROM categories WHERE id = ?
```

### 1.3. Eager loading — cách làm chuẩn list

```php
Product::with('category')->get();
// ~2 queries: products + categories WHERE id IN (...)
```

### 1.4. `withCount`

```php
Category::withCount('products')->get();
// mỗi category có products_count
// tổng products_count = 28 (sau seed P08)
```

### 1.5. Nullsafe

```blade
{{ $product->category?->name }}
```

Tránh trắng trang nếu dữ liệu bẩn thiếu FK.

---

## 2. Bài trên lớp (~20')

1. Khai báo 2 quan hệ trong Model.  
2. `GET /lab/products-with-category` — JSON có nested `category` — **bắt buộc** `with('category')`.  
3. `GET /lab/categories-with-count` — `withCount('products')`.

**Checkpoint:** tổng `products_count` = 28.

---

## 3. Bài về nhà (~30')

### A — `docs/n-plus-one.md`

Ghi số query:

1. `Product::all()` + foreach `$p->category->name`  
2. `Product::with('category')->get()` + foreach  

Dùng Debugbar hoặc `DB::enableQueryLog()`.

### B — Admin products thật

```php
$products = Product::with('category')->orderBy('name')->paginate(10);
```

Cột Category = tên. Bỏ fake P07.

### C — `GET /admin/categories/{category}`

Tên DM + bảng SP thuộc nó (`with('products')` hoặc `$category->products`).

### D — Câu hỏi video

“Nếu quên `with()` khi list 28 SP, chuyện gì xảy ra với database?”

---

## 4. EXPECT

| Key | Value |
|-----|------:|
| nested `category.name` | present |
| sum products_count | 28 |
| Admin fake data | đã gỡ |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 10                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo + docs/n-plus-one.md                                ║
║  2. Video: JSON with category + Admin list tên DM            ║
║  3. Thuyết trình N+1 theo số liệu bạn đo                     ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 11:** Form **Create** Categories & Products trên Dashboard (validate + select DM).
