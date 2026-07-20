# Phiếu 12 — ĐÍCH CUỐI: CRUD đủ 2 bảng + `belongsTo`

| | |
|---|---|
| **Buổi** | 12 — đọc kèm [Buổi 12](../12-buoi-12-dashboard-update-delete.md) |
| **Thời lượng** | ≥ 45–60 phút (về nhà có thể 40') |
| **Repo** | `cse485-ms-12` |
| **Nhận từ Phiếu 11** | Read + Create Categories & Products |
| **Đích chuỗi MiniShop** | Tự xử lý Laravel: 2 bảng liên kết, CRUD Admin đủ cả hai |

### Chuẩn đầu ra (CLO) — chuẩn đầu môn phần bài tập

1. Schema 2 bảng + FK; Model `hasMany` / **`belongsTo`**.  
2. Mọi list products dùng **`with('category')`**.  
3. CRUD đủ trên `/admin` cho **Categories và Products**.  
4. Validation + CSRF + flash; Delete bằng `DELETE` + confirm (không GET trần).  
5. Xóa Category: **Cách A** — chặn nếu còn product (khớp P09).  
6. Có `docs/FINAL.md` (ERD, luồng Update, checklist tự đánh giá).

---

## 1. Lý thuyết tổng kết chuỗi 12 phiếu

### 1.1. Bạn đã đi đường nào?

```
P01–02  Mảng + hàm (hướng cấu trúc)
P03     OOP + Session
P04–05  PDO + MVC mini (1 bảng)
P06–08  Laravel route → Blade → Migration 2 bảng
P09–10  Eloquent + Relationship + chống N+1
P11–12  Dashboard Create → ★ CRUD đủ
```

### 1.2. Method spoofing (HTML chỉ GET/POST)

```blade
<form method="POST" action="{{ route('admin.products.update', $product) }}">
    @csrf
    @method('PUT')
    …
</form>

<form method="POST" action="{{ route('admin.products.destroy', $product) }}">
    @csrf
    @method('DELETE')
    <button onclick="return confirm('Xoa?')">Xoa</button>
</form>
```

### 1.3. Route model binding

```php
public function edit(Product $product) { … }
// Laravel tự Product::findOrFail($id)
```

### 1.4. Quy tắc xóa Category — BẮT BUỘC Cách A

```
Còn product thuộc category? → flash error / không xóa
Không còn? → delete
```

(DB có thể cascade — tầng app vẫn phải chặn để hành vi chấm ổn định.)

### 1.5. `old()` khi Edit

```blade
value="{{ old('name', $product->name) }}"
@selected(old('category_id', $product->category_id) == $category->id)
```

---

## 2. Bài trên lớp (~20')

1. `categories.edit` / `update` (`@method('PUT')`).  
2. Nút xóa **Product** (DELETE + confirm) chạy 1 case.  
3. Sửa tên DM → cột Category trên trang products đổi theo (quan hệ).

**Checkpoint:** Edit category phản ánh ngay trên list products.

---

## 3. Bài về nhà (~30–40') — Ma trận CRUD đủ

| | Categories | Products |
|---|------------|----------|
| Index | ✓ | ✓ (+ tên category, `with`) |
| Create + validate | ✓ (P11) | ✓ (P11) |
| Edit + update + validate | ✓ | ✓ |
| Delete an toàn | ✓ (Cách A) | ✓ DELETE+CSRF |

### Chuẩn UI

- Layout P07; Actions: Sua \| Xoa  
- Chấm chính: `/admin/*` (lab `/lab/*` có thể giữ)

### `migrate:fresh --seed` vẫn chạy được sau khi code

### `docs/FINAL.md` bắt buộc gồm

1. ERD ASCII 2 bảng  
2. Luồng 1 request **Update product**  
3. Giải thích `belongsTo` trong form Edit (select category)  
4. Liệt kê URL admin hoàn chỉnh  
5. Checklist tự ✓/✗  

---

## 4. Rubric gợi ý

| Tiêu chí | Trọng số |
|----------|---------|
| CRUD Categories | 25% |
| CRUD Products + select | 30% |
| belongsTo/hasMany + with() | 20% |
| Validation / CSRF / DELETE | 15% |
| FINAL.md + video | 10% |

---

## 5. Kịch bản video (~4–6 phút)

1. `migrate:fresh --seed` (tua nhanh được)  
2. CRUD Category: tạo → sửa → thử xóa khi còn SP (bị chặn)  
3. CRUD Product: tạo (chọn DM) → sửa giá → xóa  
4. Zoom `ProductController@index` thấy `with('category')`  
5. Đọc 4–5 câu FINAL.md về luồng Update  

---

## 6. EXPECT routes

```
admin.categories.index|create|store|edit|update|destroy
admin.products.index|create|store|edit|update|destroy
```

| Case | Result |
|------|--------|
| Update category name | Products index hiện tên mới |
| Delete category còn SP | **bị chặn** |
| Delete product | row biến mất |
| Product edit select | selected = category_id hiện tại |

---

## 7. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 12 — ĐÍCH CUỐI MINISHOP                           ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo cse485-ms-12 + README + quy tắc xóa category        ║
║  2. docs/FINAL.md                                            ║
║  3. Video OBS + camera mặt theo kịch bản mục 5               ║
║  4. Thuyết trình: cấu trúc→OOP→MVC→Laravel→CRUD 2 bảng       ║
╚══════════════════════════════════════════════════════════════╝
```

**Sau P12:** sẵn sàng [Project nhóm Buổi 13–15](../13-project-cuoi-mon.md) — cùng pattern, nhiều bảng hơn.
