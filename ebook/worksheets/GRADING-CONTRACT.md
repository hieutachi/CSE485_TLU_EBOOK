# Hợp đồng chấm bài (chuẩn bị Autograder Online)

Tài liệu này khóa **chuẩn đầu ra + OUTPUT ổn định** cho từng phiếu.  
Dataset: [`CANONICAL-DATA.md`](./CANONICAL-DATA.md).

## Quy ước chung (mọi phiếu)

| Rule ID | Quy ước |
|---------|---------|
| `R-REPO` | Repo GitHub riêng; tên khuyến nghị `cse485-ms-{NN}` (`01`…`12`) |
| `R-VIDEO` | Video OBS + camera mặt (chấm thủ công / bán tự động) |
| `R-NO-SOLUTION` | Không chấp nhận nộp nguyên solution mẫu |
| `R-CORE-DATA` | P01–P06 + phần CORE của P08: đúng 8 SKU + `inventory_value_core=41380000` |
| `R-ESCAPE` | Output HTML: tên/SKU qua `htmlspecialchars` (P01+) |
| `R-PREPARE` | SQL động: bắt buộc PDO prepare (P04+) |

Gợi ý artifact cho máy chấm (sinh viên / CI sau này):

- PHP thuần: file `checks/expected.json` hoặc trang có comment HTML  
  `<!-- MS_EXPECT inventory_value=41380000 product_count=8 -->`
- Laravel: endpoint health `/up` + seed đếm `Category::count()`, `Product::whereIn('sku', [...])->count()`

---

## P01 — Catalog PHP

### CLO (chuẩn đầu ra)

1. Tách `data.php` / `index.php`.  
2. In bảng đủ **8** SP sau bài về nhà.  
3. Map `category_id` → tên chữ.  
4. Tính `inventory_value_core` đúng.

### EXPECT ổn định

| Key | Value |
|-----|------:|
| `product_count` | 8 |
| `inventory_value` | 41380000 |
| `title_contains` | `MiniShop` |
| `skus` | KB-01,KB-02,KB-03,MS-01,MS-02,MS-03,MN-01,MN-02 |

### Phân lớp / về nhà

| | File / hành vi |
|--|----------------|
| Trên lớp | ≥4 SKU đầu (KB-01…MS-02), 4 cột cơ bản |
| Về nhà (chấm) | Đủ 8 SKU + cột Danh muc + Thanh tien + tổng |

---

## P02 — Hàm & báo cáo

### CLO

1. 5 hàm bắt buộc trong `helpers.php`.  
2. `stockLevel` exact string.  
3. Báo cáo 3 DM đúng số.  
4. Filter `?category_id=2` → đúng 3 SKU chuột.

### EXPECT

| Key | Value |
|-----|-------|
| `inventory_value` | 41380000 |
| `report.Ban phim` | count=3, value=17620000 |
| `report.Chuot` | count=3, value=8860000 |
| `report.Man hinh` | count=2, value=14900000 |
| `filter.category_id=2.skus` | MS-01,MS-02,MS-03 |
| `rankInventory(41380000)` | `Lon` |
| `findProductBySku(MN-02).name` | `LG UltraFine` |
| `stock.MN-02` | `Can nhap` |
| `stock.KB-02` | `Du` |

---

## P03 — OOP + Session

### CLO

1. Class `Category`, `Product` + method `lineTotal`/`stockLevel`.  
2. Login POST thành công với credential chuẩn.  
3. Chặn dashboard khi chưa auth.  
4. Session `orders` tồn tại sau refresh.

### EXPECT

| Key | Value |
|-----|-------|
| `login.ok` | admin / MiniShop@03 → 302 dashboard |
| `login.fail` | sai pass → ở login + lỗi |
| `guard` | GET dashboard no session → redirect login |
| `inventory_value` | 41380000 trên dashboard |
| `product_rows` | 8 |

---

## P04 — PDO categories

### CLO

1. DB `minishop_cse485`, bảng `categories` đúng schema.  
2. CRUD đủ + prepare.  
3. Trùng `name` không trắng trang.

### EXPECT (kịch bản)

| Step | Result |
|------|--------|
| Seed ban đầu | ≥3 rows: Ban phim, Chuot, Man hinh |
| INSERT `Tai nghe` | +1 row |
| UPDATE description | persisted |
| INSERT trùng `Chuot` | error message (không 500 trắng) |
| DELETE `Tai nghe` | row gone |

---

## P05 — MVC mini

### CLO

1. Cấu trúc thư mục đúng contract.  
2. Whitelist controller/action.  
3. Model không HTML; View không PDO.  
4. Flash session 1 lần.

### EXPECT

| Key | Value |
|-----|-------|
| `paths` | config/, models/CategoryModel.php, controllers/CategoryController.php, views/category/*, public/index.php |
| `url_pattern` | `controller=category&action=index` |
| `crud` | create/edit/delete hoạt động |
| `architecture_md` | file tồn tại |

---

## P06 — Laravel routes

### CLO

1. 4 named routes admin.*.  
2. Dashboard in stats giả CORE.  
3. Không commit `.env`.

### EXPECT

| Route name | URI |
|------------|-----|
| admin.dashboard | /admin |
| admin.categories.index | /admin/categories |
| admin.products.index | /admin/products |
| admin.about | /admin/about |

| Marker HTML / text | Value |
|--------------------|------:|
| stats.categories | 3 |
| stats.products | 8 |
| stats.inventory_value | 41380000 |

---

## P07 — Blade layout

### CLO

1. `layouts/admin.blade.php` + `@yield`.  
2. 4 trang extends.  
3. Flash demo route.  
4. Active menu phân biệt.

### EXPECT

| Key | Value |
|-----|-------|
| `layout_file` | resources/views/layouts/admin.blade.php |
| `pages` | dashboard, categories/index, products/index, about |
| `flash_demo_route` | tồn tại, set session success |

---

## P08 — Migration & Seeder

### CLO

1. Schema 2 bảng + FK cascade + `sku` unique.  
2. Seed 3 DM cố định + 8 SKU gốc + 20 Faker = 28.  
3. `$fillable` đủ.

### EXPECT ổn định

| Key | Value |
|-----|------:|
| `Category::count()` | 3 |
| `Product::count()` | 28 |
| `Product::whereIn(sku, CORE8)->count()` | 8 |
| `inventory_value_seed` | **KHÔNG chấm cố định** |

---

## P09 — Eloquent lab JSON

### CLO

1. CRUD Product qua HTTP lab.  
2. Validate 422 khi thiếu field.  
3. Không xóa category còn product → **422**.

### EXPECT (status)

| Request | Status |
|---------|-------:|
| POST product hợp lệ | 201 |
| POST thiếu category_id | 422 |
| DELETE category còn SP | 422 |
| DELETE product | 200 hoặc 204 |

---

## P10 — Relationship

### CLO

1. `hasMany` / `belongsTo` đúng chiều.  
2. List dùng `with('category')`.  
3. `withCount` tổng = 28.  
4. Có `docs/n-plus-one.md` nêu số query.

### EXPECT

| Key | Value |
|-----|------:|
| `GET /lab/products-with-category` nested `category.name` | present |
| `sum(products_count)` | 28 |
| Admin products fake data | **đã gỡ** |

---

## P11 — Dashboard Create

### CLO

1. Categories: index + create/store.  
2. Products: create có `<select>` category + validate.  
3. Dashboard stats = query thật (không hardcode 3/8/41380000).

### EXPECT

| Key | Note |
|-----|------|
| `POST /admin/categories` | redirect + flash + row mới |
| `POST /admin/products` | 302 + product thuộc category chọn |
| validation empty name | 422/redirect back + errors |
| `stats.products` | == `Product::count()` (thường 28+ sau thao tác) |

---

## P12 — CRUD đủ 2 bảng (ĐÍCH)

### CLO

1. Resource CRUD đủ Categories & Products trên `/admin/*`.  
2. `belongsTo` + `with('category')` trên index products.  
3. Delete Category: **Cách A bắt buộc** — chặn nếu còn product (flash/422), thống nhất với P09.  
4. Delete Product: method DELETE + CSRF + confirm.  
5. `docs/FINAL.md` tồn tại.

### EXPECT routes (name)

```
admin.categories.index|create|store|edit|update|destroy
admin.products.index|create|store|edit|update|destroy
```

### EXPECT hành vi

| Case | Result |
|------|--------|
| Update category name | Products index hiện tên mới |
| Delete category còn SP | **bị chặn** |
| Delete product | row biến mất |
| Product edit select | selected = category_id hiện tại |

---

## Ma trận sẵn sàng Autograder

| Phiếu | Máy chấm số liệu ổn định? | Gợi ý kỹ thuật |
|-------|---------------------------|----------------|
| P01–P03 | Cao | Parse HTML / CLI PHPUnit trên hàm |
| P04–P05 | Trung | SQL fixture + HTTP crawl |
| P06–P07 | Cao | `artisan route:list` + HTTP get |
| P08 | Cao (count/SKU), thấp (SUM) | assert count + whereIn sku |
| P09–P10 | Cao | HTTP JSON status |
| P11–P12 | Trung–Cao | Laravel Dusk / HTTP + DB assert |

**Khuyến nghị giai đoạn 1 Online grading:** P01, P02, P06, P08 (count), P09, P10 JSON — trước; video vẫn chấm tay.
