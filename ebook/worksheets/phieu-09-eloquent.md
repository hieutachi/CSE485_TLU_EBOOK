# Phiếu 09 — Eloquent CRUD cơ bản cho cả 2 Model

| | |
|---|---|
| **Buổi** | 9 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-09` |
| **Nhận từ Phiếu 08** | 3 categories + 28 products |
| **Mang sang Phiếu 10** | Cùng model; thêm relationship & `with()` |

### Chuẩn đầu ra (CLO)

1. Lab HTTP CRUD cho Product (+ category update/delete).  
2. Validate trả **422** khi thiếu field / unique.  
3. DELETE category còn product → **422** (không cascade im lặng ở tầng app).  
4. Có `docs/lab-test.md` 6 request mẫu.

---

## 1. Tóm tắt lý thuyết

- Eloquent map class ↔ bảng; `$fillable`.
- `all`, `findOrFail`, `create`, `update`, `delete`, `paginate`.
- Validation trước khi ghi DB.

> Phiếu này: CRUD bằng Eloquent, **chưa** bắt buộc thuộc tính quan hệ trên Blade. Có thể trả JSON để dễ chấm.

---

## 2. Bài trên lớp (~20') — Đọc & tạo Category qua HTTP

### Routes (web hoặc api — chọn 1, ghi README)

Tối thiểu:

```text
GET    /lab/categories           → JSON paginate hoặc collection
POST   /lab/categories           → create (validate name unique)
GET    /lab/categories/{id}      → show
```

Dùng Controller riêng `Lab/CategoryLabController` (tránh phá Admin UI).

**Checkpoint:** Postman/HTTPie/Firefox tạo được category `Test Cat` rồi GET thấy nó.

---

## 3. Bài về nhà (~30') — CRUD Products + một phần Categories Update/Delete

### Nhiệm vụ A — Product Lab routes

```text
GET    /lab/products
POST   /lab/products
GET    /lab/products/{id}
PUT    /lab/products/{id}
DELETE /lab/products/{id}
```

Validate Product:

| field | rule |
|-------|------|
| category_id | required\|exists:categories,id |
| sku | required\|string\|max:30\|unique:products,sku |
| name | required\|max:150 |
| price | required\|integer\|min:0 |
| qty | required\|integer\|min:0 |
| description | nullable\|string |

Khi update: rule `unique` phải ignore id hiện tại.

### Nhiệm vụ B — Category update/delete lab

```text
PUT    /lab/categories/{id}
DELETE /lab/categories/{id}
```

**Cấm xóa** nếu category còn product (đếm `Product::where('category_id', $id)->exists()` → trả 422 JSON message).  
(Cascade DB vẫn có — bài tập này để bạn **xử lý nghiệp vụ ở tầng app**.)

### Nhiệm vụ C — Seed lại trước demo

`migrate:fresh --seed` rồi chạy kịch bản không phá 3 category gốc nếu có thể (tạo category tạm để xóa).

### Nhiệm vụ D — File `docs/lab-test.md`

Ghi lại 6 request mẫu (URL, body, expected status):  
create category, create product, update product price, delete product, delete category còn SP (422), delete category trống (200/204).

---

## 4. Output kỳ vọng

| Case | HTTP |
|------|------|
| POST product hợp lệ | 201 |
| POST product thiếu category_id | 422 |
| DELETE category đang có SP | 422 |
| DELETE product | 200/204 + GET list giảm 1 |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 09                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo + docs/lab-test.md                                  ║
║  2. Video đủ create/update/delete product + case 422 category║
║  3. Zoom $fillable và validate unique sku                    ║
╚══════════════════════════════════════════════════════════════╝
```
