# Phiếu 10 — `belongsTo` / `hasMany` & chống N+1

| | |
|---|---|
| **Buổi** | 10 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-10` |
| **Nhận từ Phiếu 09** | Eloquent CRUD 2 model |
| **Mang sang Phiếu 11** | List Admin phải hiện tên category bằng quan hệ |

### Chuẩn đầu ra (CLO)

1. `Category::products()` hasMany + `Product::category()` belongsTo.  
2. Endpoint JSON dùng `with('category')`; `withCount` tổng = **28**.  
3. Admin products bỏ fake data; có `docs/n-plus-one.md` ghi số query.

---

## 1. Tóm tắt lý thuyết

```
Category hasMany Product
Product belongsTo Category
Eager: Product::with('category')  → ~2 queries
Lazy trong vòng lặp 28 SP → N+1
```

---

## 2. Bài trên lớp (~20') — Khai báo quan hệ & chứng minh

### Trong Model

```php
// Category
public function products(): HasMany

// Product
public function category(): BelongsTo
```

### Lab route mới

```text
GET /lab/products-with-category
```

Trả JSON: mỗi product có object `category` (id, name). **Bắt buộc** dùng `with('category')`.

Thêm:

```text
GET /lab/categories-with-count
```

Dùng `withCount('products')` → mỗi category có `products_count`.

**Checkpoint:** với seed chuẩn, tổng `products_count` = 28.

---

## 3. Bài về nhà (~30') — Đo N+1 + trang Blade thử nghiệm

### Nhiệm vụ A — So sánh số query

Trong `docs/n-plus-one.md` ghi kết quả bạn đo được (Debugbar **hoặc** `DB::enableQueryLog()`):

1. `Product::all()` rồi foreach `$p->category->name` → bao nhiêu query?  
2. `Product::with('category')->get()` rồi foreach tương tự → bao nhiêu query?

Screenshot log hoặc paste số query + 2 dòng SQL tiêu biểu.

### Nhiệm vụ B — Blade thử (chưa cần CRUD form)

`GET /admin/products` (trang Admin Phiếu 07): **bỏ fake data**, lấy:

```php
$products = Product::with('category')->orderBy('name')->paginate(10);
```

Cột Category in `$product->category?->name`.  
Pagination links hiện được.

### Nhiệm vụ C — Trang category show

```text
GET /admin/categories/{category}
```

In tên category + bảng các product thuộc nó (`$category->products` hoặc `with('products')`).

### Nhiệm vụ D — Câu hỏi bắt buộc trong video

“Nếu quên `with()` khi list 28 sp, chuyện gì xảy ra với database?”

---

## 4. Rubric

| Tiêu chí | Bắt buộc |
|----------|----------|
| `belongsTo` / `hasMany` đúng chiều | Có |
| Endpoint JSON có nested category | Có |
| `withCount` đúng tổng 28 | Có |
| Admin products không còn hardcode | Có |
| `docs/n-plus-one.md` có số liệu | Có |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 10                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo + docs/n-plus-one.md                                ║
║  2. Video: JSON with category + Admin list tên DM + category ║
║     show list products                                       ║
║  3. Thuyết trình N+1 theo số liệu bạn đo                     ║
╚══════════════════════════════════════════════════════════════╝
```
