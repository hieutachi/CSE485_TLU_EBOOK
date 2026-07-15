# Phiếu 12 — ĐÍCH CUỐI: CRUD đủ 2 bảng + `belongsTo`

| | |
|---|---|
| **Buổi** | 12 |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-12` |
| **Nhận từ Phiếu 11** | Read + Create Categories & Products |
| **Đích chuỗi MiniShop** | Tự xử lý bài toán Laravel 2 bảng liên kết, CRUD cả hai |

---

## 1. Mục tiêu đầu ra (chuẩn đầu môn phần bài tập về nhà)

Sinh viên **tự hoàn thiện** một Admin MiniShop trên Laravel sao cho:

1. Có đúng **2 bảng** `categories` và `products` liên kết khóa ngoại.
2. Model khai báo `Category::products()` `hasMany` và `Product::category()` **`belongsTo`**.
3. Mọi list products dùng **`with('category')`** (không N+1).
4. **CRUD đầy đủ** trên Dashboard cho **cả 2 bảng** (index/create/store/edit/update/destroy).
5. Validation + CSRF + flash + confirm delete (DELETE method, không xóa bằng GET trần).  
6. Xóa Category: **chặn nếu còn product** (Cách A — xem GRADING-CONTRACT).

Đây là điểm hội tụ của 12 phiếu — không yêu cầu Auth/Middleware (để dành Project điểm Cộng).  
Chi tiết EXPECT: [`GRADING-CONTRACT.md` §P12](./GRADING-CONTRACT.md).

---

## 2. Bài trên lớp (~20') — Edit/Update Categories + Destroy Products thử

### Làm trong giờ

1. `categories.edit` / `update` hoạt động (form có `@method('PUT')`).
2. Nút xóa **Product** trên index (form DELETE + confirm) — chạy được 1 case.
3. Ôn lại binding `Product $product`.

**Checkpoint:** sửa tên category phản ánh ngay trên cột Category của trang products (nhờ quan hệ).

---

## 3. Bài về nhà (~30–40') — Hoàn thiện CRUD cả hai bảng

### Ma trận chức năng (bắt buộc đủ ✓)

| | Categories | Products |
|---|------------|----------|
| Index + paginate/get | ✓ | ✓ (+ tên category) |
| Create + validate | ✓ | ✓ (select category) |
| Edit + update + validate | ✓ | ✓ |
| Delete an toàn | ✓ (*) | ✓ |

\(*) **Quy tắc xóa Category — BẮT BUỘC Cách A (để chấm tự động ổn định):**

- Nếu category còn ít nhất 1 product → **không xóa**, flash error (hoặc 422).  
- Chỉ xóa được khi không còn product thuộc category đó.  
- (DB vẫn có thể `cascadeOnDelete` — nhưng tầng app phải chặn trước, thống nhất P09.)

### Chuẩn UI

- Layout Admin Phiếu 07.
- Actions trên mỗi hàng: Sua | Xoa.
- Không còn route `/lab/*` cũng được (có thể giữ) — **phần chấm chính là `/admin/*`**.

### Chuẩn dữ liệu demo

`migrate:fresh --seed` vẫn chạy; thêm/sửa/xóa trên UI không làm hỏng schema.

### Tài liệu cuối chuỗi `docs/FINAL.md` (bắt buộc)

Viết 1–1.5 trang markdown gồm:

1. ERD ASCII 2 bảng.  
2. Luồng 1 request **Update product** (DB←Model←Controller←Blade).  
3. Giải thích `belongsTo` trong form Edit (vì sao select category).  
4. Liệt kê URL admin hoàn chỉnh.  
5. Tự đánh giá checklist ✓/✗.

---

## 4. Rubric chấm Phiếu 12 (gợi ý giảng viên)

| Tiêu chí | Điểm tương đối |
|----------|----------------|
| CRUD Categories đủ | 25% |
| CRUD Products đủ + select category | 30% |
| `belongsTo`/`hasMany` + `with()` đúng | 20% |
| Validation / CSRF / DELETE đúng cách | 15% |
| `docs/FINAL.md` + video thuyết trình | 10% |

---

## 5. Kịch bản video bắt buộc (một take ~4–6 phút)

1. `migrate:fresh --seed` (tua nhanh được).  
2. CRUD Category: tạo → sửa → (thử xóa theo quy tắc đã chọn).  
3. CRUD Product: tạo (chọn DM) → sửa giá → xóa.  
4. Chỉ list products: cột danh mục là **tên**, mở nhanh `ProductController@index` thấy `with('category')`.  
5. Đọc to 4–5 câu trong `FINAL.md` về luồng Update.

---

## 6. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 12 — ĐÍCH CUỐI CHUỖI MINISHOP                     ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG, README cài đặt + quy tắc xóa category ║
║  2. docs/FINAL.md                                            ║
║  3. Video OBS + camera mặt theo kịch bản mục 5               ║
║  4. Link Drive + link Repo nộp hệ thống                      ║
╚══════════════════════════════════════════════════════════════╝
```

---

**Sau Phiếu 12:** sinh viên đủ năng lực làm Project nhóm (Buổi 13–15) với nhiều bảng hơn — cùng pattern Migration → Eloquent → Relationship → CRUD Dashboard.
