# Phiếu 09 — Eloquent CRUD cơ bản (2 Model)

| | |
|---|---|
| **Buổi** | 9 — đọc kèm [Buổi 09](../09-buoi-09-eloquent.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-09` |
| **Nhận từ Phiếu 08** | 3 categories + 28 products |
| **Mang sang Phiếu 10** | Thêm `belongsTo` / `hasMany` + `with()` |

### Chuẩn đầu ra (CLO)

1. Giải thích Eloquent map Class ↔ bảng; vai trò `$fillable`.  
2. Lab HTTP CRUD Product (+ Category update/delete).  
3. Validate → **422** khi thiếu field / unique.  
4. DELETE category còn product → **422** (tầng app chặn).  
5. Có `docs/lab-test.md` ≥ 6 request mẫu.

---

## 1. Lý thuyết — Eloquent là gì?

### 1.1. So với PDO Buổi 4

| PDO thuần | Eloquent |
|-----------|----------|
| Viết SQL gần như đầy đủ | `Product::create([...])` |
| `fetchAll()` mảng | Model object |
| Dễ sai placeholder | Query builder + Active Record |

**Vẫn cần hiểu SQL** — Eloquent sinh SQL giúp bạn.

```
Product::create([...])  ≈  INSERT INTO products …
Product::find(1)        ≈  SELECT * WHERE id=1
$p->update([...])       ≈  UPDATE …
$p->delete()            ≈  DELETE …
```

### 1.2. Mass assignment

Request gửi thừa field nguy hiểm → chỉ cột trong `$fillable` được gán khi `create/update` mảng.

### 1.3. Validate trước khi ghi

```php
$request->validate([
    'category_id' => ['required', 'exists:categories,id'],
    'sku' => ['required', 'string', 'max:30', 'unique:products,sku'],
    // …
]);
```

Fail → 422 (JSON) hoặc redirect back kèm `$errors` (web).

### 1.4. Quy tắc xóa Category (khóa P09 & P12)

```
Nếu Product::where('category_id', $id)->exists()
    → return 422 + message
Ngược lại
    → cho xóa
```

Đồng bộ Cách A ở Phiếu 12 (không im lặng cascade ở tầng app).

### 1.5. Lab routes vs Admin UI

P09 dùng `/lab/...` để chấm HTTP dễ.  
P11–P12 mới gắn Blade Admin. Có thể giữ lab song song.

---

## 2. Bài trên lớp (~20') — Category lab đọc/tạo

```text
GET  /lab/categories
POST /lab/categories
GET  /lab/categories/{id}
```

Controller riêng `Lab/CategoryLabController`.  
**Checkpoint:** tạo `Test Cat` bằng POST rồi GET thấy nó.

---

## 3. Bài về nhà (~30') — Product CRUD + chặn xóa DM

### Product routes

```text
GET|POST /lab/products
GET|PUT|DELETE /lab/products/{id}
```

Validate: `category_id` exists; `sku` unique (update phải ignore id hiện tại); `name`, `price`, `qty`…

### Category PUT + DELETE

DELETE còn SP → **422**.

### `docs/lab-test.md`

Ghi 6 case: create cat, create product, update price, delete product, delete cat còn SP (422), delete cat trống (200/204).

### Trước demo

`migrate:fresh --seed` — đừng phá 3 DM gốc; tạo DM tạm để test xóa.

---

## 4. EXPECT status

| Request | Status |
|---------|-------:|
| POST product hợp lệ | 201 |
| POST thiếu category_id | 422 |
| DELETE category còn SP | 422 |
| DELETE product | 200 hoặc 204 |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 09                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo + docs/lab-test.md                                  ║
║  2. Video create/update/delete product + 422 category        ║
║  3. Thuyết trình: $fillable chống gì? Eloquent khác PDO?     ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 10:** `$product->category->name` đúng cách + **`with('category')` chống N+1**.
