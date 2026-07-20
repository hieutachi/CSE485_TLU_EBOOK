# Phiếu 11 — Admin Dashboard: Read + Create (cả 2 bảng)

| | |
|---|---|
| **Buổi** | 11 — đọc kèm [Buổi 11](../11-buoi-11-dashboard-create.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-11` |
| **Nhận từ Phiếu 10** | List products có `with('category')` |
| **Mang sang Phiếu 12** | Thiếu Edit/Update/Delete → hoàn thiện CRUD |

### Chuẩn đầu ra (CLO)

1. Luồng dữ liệu: DB → Eloquent → Controller → Blade → Browser.  
2. Categories: index + create/store + validate + flash.  
3. Products: create có **select** category (`belongsTo`) + validate.  
4. Dashboard stats = **query thật** (không hardcode P06).

---

## 1. Lý thuyết — ghép các mảnh đã học

### 1.1. Bản đồ kỹ năng đến P11

```
P07 Blade layout
P08 Migration + seed
P09 Eloquent create/validate
P10 with('category')
P11 = Layout + Eloquent + Form Create trên /admin
```

### 1.2. Luồng Create Product

```
GET /admin/products/create
  Controller lấy Category::orderBy('name')->get()
  View: form select category_id + sku, name, price, qty…

POST /admin/products
  @csrf
  validate(...)
  Product::create($data)
  redirect index with('success', ...)
```

### 1.3. Validation — hai lớp

| Lớp | Vai trò |
|-----|---------|
| HTML `required` | UX — dễ bỏ qua |
| `$request->validate` | **Bắt buộc** — không tin Client |

Fail → redirect back + `$errors` + `old()` giữ input.

### 1.4. Stats Dashboard thật

```php
'categories' => Category::count(),
'products' => Product::count(),
'inventory_value' => Product::query()
    ->selectRaw('COALESCE(SUM(price * qty),0) as v')->value('v'),
```

Sau seed 28 SP, `inventory_value` **≠ 41380000** là đúng.  
Máy chấm: `stats.products === Product::count()`, không so cứng 41380000.

### 1.5. `@csrf` + flash

Thiếu CSRF → 419 Page Expired.  
Flash: `session('success')` trong `partials/flash.blade.php` (P07).

---

## 2. Bài trên lớp (~20') — Categories Create

### Routes

```text
GET  /admin/categories            index
GET  /admin/categories/create     create
POST /admin/categories            store
```

### Việc trên lớp

1. Index: `Category::withCount('products')->orderBy('name')->get()` (hoặc paginate).  
2. Form name + description; validate; flash; redirect.  
3. Không còn fake P07.

**Checkpoint:** thêm được `Tai nghe` trên UI Admin.

---

## 3. Bài về nhà (~30') — Products Create + stats

### Routes

```text
GET  /admin/products            index (chỉnh từ P10)
GET  /admin/products/create     create
POST /admin/products            store
```

### Form Create Product bắt buộc

- Select `category_id` từ DB  
- sku, name, price, qty, description  
- Validate như P09 (unique sku, exists category…)  
- `@csrf`, `$errors`, `old()`

### Index Products

- `with('category')` + paginate 10  
- Cột Category = tên  
- Nút **+ Them san pham**

### README tiến độ

```
- [x] Read + Create Categories
- [x] Read + Create Products
- [ ] Update + Delete 2 bang (P12)
```

### Kịch bản video

1. Dashboard count khớp DB  
2. Create Category  
3. Create Product chọn DM  
4. Submit thiếu name → lỗi + giữ input  
5. Zoom `store` validate + `with('category')` ở index  

---

## 4. Lỗi thường gặp

| Lỗi | Cách xử |
|-----|---------|
| 419 | Thiếu `@csrf` |
| MassAssignment | Thiếu cột trong `$fillable` |
| Select trống | Quên truyền `$categories` sang view |
| Stats vẫn 3/8/41380000 | Chưa đổi sang query thật |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 11                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo Admin Create đủ 2 bảng                              ║
║  2. Video 5 bước mục 3                                       ║
║  3. Nhấn mạnh: products.create select = quan hệ belongsTo    ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 12 (đích):** Update + Delete đủ **cả 2 bảng** + `docs/FINAL.md`.
