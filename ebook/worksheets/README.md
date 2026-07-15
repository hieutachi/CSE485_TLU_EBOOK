# Phần 2 — 12 Phiếu học tập (chuỗi xuyên suốt MiniShop)

Mỗi phiếu gắn **1 buổi** (~45–60 phút). Bài **trên lớp** và **về nhà** nối tiếp.

> **Đích cuối (Phiếu 12):** Laravel + `belongsTo` / `hasMany` + **CRUD đủ cả 2 bảng** trên Admin Dashboard.

## Tài liệu khoá số liệu & chấm bài

| File | Vai trò |
|------|---------|
| **[CANONICAL-DATA.md](./CANONICAL-DATA.md)** | Single source of truth: 8 SP, tổng kho **41380000**, stockLevel, credential |
| **[GRADING-CONTRACT.md](./GRADING-CONTRACT.md)** | CLO + EXPECT ổn định từng phiếu — sẵn cho Autograder Online |

```
P01–03  PHP: catalog → hàm → OOP + Session
   ↓
P04–05  PDO + MVC mini (categories)
   ↓
P06–08  Laravel: route → Blade → Migration 2 bảng + Seeder (3 + 28)
   ↓
P09–10  Eloquent CRUD + Relationship + chống N+1
   ↓
P11–12  Dashboard Create → ★ CRUD đủ 2 bảng
```

| Phiếu | Buổi | Repo | Kết quả mang sang |
|-------|------|------|-------------------|
| [01](./phieu-01-php-co-ban.md) | 1 | `cse485-ms-01` | 8 SP + tổng 41380000 |
| [02](./phieu-02-cau-truc-ham.md) | 2 | `cse485-ms-02` | helpers + báo cáo / filter |
| [03](./phieu-03-oop-session.md) | 3 | `cse485-ms-03` | Class + login Session |
| [04](./phieu-04-pdo-categories.md) | 4 | `cse485-ms-04` | CRUD PDO categories |
| [05](./phieu-05-mvc-mini.md) | 5 | `cse485-ms-05` | MVC mini categories |
| [06](./phieu-06-laravel-route.md) | 6 | `cse485-ms-06` | 4 route Admin + stats giả |
| [07](./phieu-07-blade-layout.md) | 7 | `cse485-ms-07` | Layout Admin |
| [08](./phieu-08-migration-seeder.md) | 8 | `cse485-ms-08` | DB 2 bảng, seed 3/28 |
| [09](./phieu-09-eloquent.md) | 9 | `cse485-ms-09` | Eloquent lab CRUD |
| [10](./phieu-10-relationship.md) | 10 | `cse485-ms-10` | belongsTo + with() |
| [11](./phieu-11-dashboard-create.md) | 11 | `cse485-ms-11` | List + Create 2 bảng |
| [12](./phieu-12-dashboard-crud.md) | 12 | `cse485-ms-12` | **CRUD đủ 2 bảng** |

## Quy định nộp bài (mọi phiếu)

1. **Mã nguồn:** GitHub Repository riêng (`cse485-ms-{NN}`).  
2. **Video OBS:** camera rõ mặt + chạy đúng kịch bản EXPECT của phiếu.  
3. Bám [CANONICAL-DATA](./CANONICAL-DATA.md) — không tự đổi giá/SKU core.

## Ghi chú nhất quán quan trọng

| Chủ đề | Quy ước |
|--------|---------|
| Tổng kho 8 SP CORE | **41380000** (không phải 41170000) |
| Sau seed Faker (28 SP) | Không chấm cứng tổng kho; chấm count + 8 SKU gốc |
| Xóa category (P09/P12) | **Chặn** nếu còn product |
| Stats giả P06 | `3 / 8 / 41380000` |
| Stats thật P11+ | Query DB sống |
