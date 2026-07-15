# Phiếu học tập 09 — Migration & Seeder

> **Gắn với:** Buổi 8  
> **Repo gợi ý:** `cse485-phieu-09-departments-employees`

---

## 1. Tóm tắt lý thuyết

- `.env` cấu hình DB.
- Migration = version control cho schema.
- `foreignId()->constrained()` tạo FK.
- Seeder + Faker sinh dữ liệu demo hàng loạt.
- `php artisan migrate:fresh --seed`.

---

## 2. Bài tập thực hành

### Schema bắt buộc

**`departments`**

| Cột | Kiểu |
|-----|------|
| id | bigint PK |
| name | string |
| timestamps | yes |

**`employees`**

| Cột | Kiểu |
|-----|------|
| id | bigint PK |
| department_id | FK → departments.id |
| name | string |
| email | string unique |
| salary | unsignedInteger |
| timestamps | yes |

### Seeder

1. Tạo **ít nhất 5** departments (tên cụ thể trong seeder).
2. Random **đúng 50** employees (Faker), mỗi employee thuộc 1 department ngẫu nhiên.

### Output kỳ vọng

Trong tinker hoặc SQL:

```
departments count >= 5
employees count = 50
```

Video: chạy `migrate:fresh --seed` + chứng minh đếm bản ghi.

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 09                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG (migrations + seeders + Models)        ║
║  2. README: tên DB, lệnh migrate --seed                      ║
║  3. Video OBS + camera mặt: fresh --seed + đếm 50 employees  ║
║  4. Nộp link Repo + Drive                                    ║
╚══════════════════════════════════════════════════════════════╝
```
