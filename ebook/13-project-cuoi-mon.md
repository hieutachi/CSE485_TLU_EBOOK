# HƯỚNG DẪN PROJECT CUỐI MÔN (Buổi 13, 14, 15)

> **Hình thức:** Bài tập nhóm — tối đa **04 sinh viên / nhóm**  
> **Thời gian:** 3 buổi tự làm (giảng viên mentoring)  
> **Mục tiêu:** Backend Laravel hoàn chỉnh trên Admin Dashboard

---

## 1. Mô tả tổng quát

Nhóm tự chọn một đề tài Backend (ví dụ: quản lý thư viện, cửa hàng, sự kiện CLB, phòng trọ, phòng khám…).  

Yêu cầu cốt lõi: **nhiều bảng liên kết**, **Migration + Seeder**, **CRUD trên Dashboard**.

```
Đề tài
  ├── Phân tích thực thể (bảng) & quan hệ
  ├── Migration + Seeder (Faker)
  ├── Eloquent Model + Relationship
  ├── Admin Layout (Blade)
  ├── CRUD từng module
  └── (Tuỳ chọn điểm Cộng) Auth + Middleware + bảo mật
```

---

## 2. Tổ chức nhóm gợi ý

| Vai trò | Công việc |
|---------|-----------|
| Trưởng nhóm | Chia module, merge Git, nộp repo |
| Thành viên A–D | Mỗi người phụ trách ≥ 04 bảng + CRUD tương ứng |
| Luân phiên | Review code lẫn nhau trước buổi demo |

**Git workflow tối thiểu:**

- 1 repo GitHub chung
- Nhánh `main` bảo vệ; mỗi người làm trên `feature/...`
- Pull Request nội bộ trước khi merge

---

## 3. Tiêu chí điểm B (Khá / Giỏi)

Đạt **đồng thời** các ý sau:

1. **Mỗi thành viên phụ trách tối thiểu 04 bảng** có liên kết hợp lý (không phải 4 bảng “trang trí” không quan hệ).
2. Có **đầy đủ Migration** phản ánh đúng schema.
3. Có **Seeder** sinh dữ liệu random (Faker) — demo không phụ thuộc nhập tay.
4. **CRUD đầy đủ** trên Admin Dashboard cho các bảng phụ trách (List có quan hệ hiển thị tên bảng cha; Create/Edit có validate; Delete an toàn).

### Ví dụ phân module (Shop)

```
Thành viên 1: categories, brands, products, product_images
Thành viên 2: customers, addresses, carts, cart_items
Thành viên 3: orders, order_items, payments, shipments
Thành viên 4: users, roles, coupons, reviews
```

Mỗi cụm phải có FK / quan hệ Eloquent rõ ràng.

---

## 4. Tiêu chí điểm Cộng (Xuất sắc)

Làm **thêm** (càng chắc càng tốt):

| Hạng mục | Gợi ý kỹ thuật Laravel |
|----------|-------------------------|
| Authentication | Laravel Breeze / UI login–register–reset password |
| Phân quyền Middleware | `role:admin`, Gate/Policy, hoặc middleware tự viết |
| Bảo mật | CSRF mặc định; Validation; `{{ }}` escape XSS; không raw SQL nối chuỗi; hash password (`bcrypt`); không commit `.env` |

```
Request ─► Middleware Auth ─► Middleware Role ─► Controller ─► Policy?
```

---

## 5. Lịch 3 buổi tự làm (gợi ý)

### Buổi 13 — Phân tích & Schema

- Chốt đề tài, vẽ ERD (có thể ASCII / draw.io)
- Chia 4 bảng / người
- Viết Migration + quan hệ khóa ngoại
- `migrate:fresh` chạy được trên máy mọi thành viên

**Deliverable:** ERD + migrations merged

### Buổi 14 — Seeder & CRUD nền

- Seeder / Factory
- Layout admin thống nhất
- CRUD xong ≥ 50% module mỗi người

**Deliverable:** Demo list + create chạy

### Buổi 15 — Hoàn thiện & Demo

- CRUD đủ, validation, flash message
- (Cộng) Auth + middleware
- Chuẩn bị slide 5–7 phút + tài khoản demo
- Nộp repo + (nếu GV yêu cầu) video OBS nhóm

**Deliverable:** Demo hội đồng / giảng viên

---

## 6. Rubric chấm nhanh (tham khảo)

| Tiêu chí | Trọng số gợi ý |
|----------|----------------|
| Thiết kế CSDL & quan hệ | 20% |
| Migration + Seeder | 15% |
| CRUD Dashboard đúng–đủ | 35% |
| Code rõ, MVC/Laravel đúng chuẩn | 15% |
| Điểm Cộng (Auth/Middleware/Bảo mật) | 15% |

---

## 7. Checklist nộp bài nhóm

- [ ] README.md: mô tả đề tài, cách cài (`composer install`, `.env`, `migrate --seed`, `serve`)
- [ ] Tài khoản demo (nếu có Auth)
- [ ] ERD / danh sách bảng–người phụ trách
- [ ] Không đẩy `.env`, `vendor/`, mật khẩu thật
- [ ] Mọi form POST có `@csrf`; xóa bằng DELETE

---

## 8. Lời nhắn cuối Phần 1

Các bạn đã có đủ “bản đồ” từ PHP thuần đến Laravel Dashboard. Project không phải để nhồi thêm framework — mà để **chứng minh** bạn thiết kế được dữ liệu, tách được trách nhiệm, và hoàn thiện CRUD có quan hệ.

Sang **Phần 2**, hãy làm lần lượt 11 Phiếu học tập để củng cố từng viên gạch trước khi (hoặc song song khi) làm Project.

→ [Phiếu học tập](./worksheets/README.md)

---

*CSE485 – Ebook Backend PHP & Laravel | Project cuối môn*
