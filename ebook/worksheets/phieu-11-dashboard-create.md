# Phiếu 11 — Admin Dashboard: Read + Create (cả 2 bảng)

| | |
|---|---|
| **Buổi** | 11 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-11` |
| **Nhận từ Phiếu 10** | List products có `with('category')` |
| **Mang sang Phiếu 12** | Thiếu Edit/Update/Delete → hoàn thiện CRUD |

### Chuẩn đầu ra (CLO)

1. Categories: index + create/store + validate + flash.  
2. Products: create có select category (`belongsTo`) + validate.  
3. Dashboard stats lấy từ DB (không hardcode P06).

---

## 1. Tóm tắt lý thuyết

```
DB → Eloquent (+ with) → Controller (validate) → Blade (layout) → Browser
Form POST + @csrf + old() + $errors
```

---

## 2. Bài trên lớp (~20') — Categories List + Create

### Routes

```text
GET  /admin/categories           index
GET  /admin/categories/create    create
POST /admin/categories           store
```

### Việc trên lớp

1. Index: bảng thật từ `Category::withCount('products')->orderBy('name')->get()` (hoặc paginate).
2. Create form: name, description; validate; flash success; redirect index.
3. Không còn dữ liệu fake Phiếu 07.

**Checkpoint:** thêm được `Tai nghe` trên UI Admin layout.

---

## 3. Bài về nhà (~30') — Products List + Create (có select Category)

### Routes

```text
GET  /admin/products             index  (đã có từ P10 — chỉnh cho đồng bộ)
GET  /admin/products/create      create
POST /admin/products             store
```

### Form Create Product bắt buộc có

- Select `category_id` load từ `Category::orderBy('name')->get()`
- sku, name, price, qty, description
- Validate như Phiếu 09 (unique sku, exists category, …)
- `@csrf`, hiện `$errors`, `old()`

### Index Products

- Paginate 10
- Cột Category = tên (`with('category')`)
- Nút **+ Them san pham**

### Dashboard thật hóa stats

Thay số giả Phiếu 06 bằng query thật:

```php
'categories' => Category::count(),
'products' => Product::count(),
'inventory_value' => Product::query()->selectRaw('COALESCE(SUM(price * qty),0) as v')->value('v'),
```

> Sau seed P08 (28 SP), `inventory_value` **≠ 41380000** là bình thường.  
> Máy chấm so khớp `stats.products === Product::count()`, không so cứng 41380000.

### README tiến độ

```
- [x] Read + Create Categories
- [x] Read + Create Products
- [ ] Update + Delete 2 bang (Phieu 12)
```

---

## 4. Kịch bản video

1. Dashboard hiện count khớp DB.  
2. Create Category mới.  
3. Create Product thuộc category đó (chọn select).  
4. Submit thiếu name → lỗi validation + vẫn giữ input khác.  
5. Zoom Controller `store` validate + `with('category')` ở index.

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 11                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo Admin Create đủ 2 bảng                              ║
║  2. Video kịch bản 5 bước ở mục 4                            ║
║  3. Nhấn mạnh: products.create có select belongsTo category  ║
╚══════════════════════════════════════════════════════════════╝
```
