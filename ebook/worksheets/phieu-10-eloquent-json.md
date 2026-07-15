# Phiếu học tập 10 — Eloquent ORM (JSON)

> **Gắn với:** Buổi 9–10  
> **Repo gợi ý:** `cse485-phieu-10-eloquent-json`  
> **Dùng tiếp schema:** `departments` / `employees` (Phiếu 9)

---

## 1. Tóm tắt lý thuyết

- Eloquent map Model ↔ bảng; `$fillable` chống mass assignment.
- CRUD: `all/find/create/update/delete`.
- Quan hệ: `Department hasMany Employee`, `Employee belongsTo Department`.
- Eager load: `Employee::with('department')` tránh N+1.

---

## 2. Bài tập thực hành

### Yêu cầu

Không cần giao diện đẹp. Viết routes trả **JSON**:

| Method | URL | Việc |
|--------|-----|------|
| GET | `/api/employees` | List + `with('department')`, paginate 10 |
| GET | `/api/employees/{id}` | Chi tiết 1 employee + department |
| POST | `/api/employees` | Validate + create |
| PUT | `/api/employees/{id}` | Validate + update |
| DELETE | `/api/employees/{id}` | Xóa |

Validate tối thiểu khi create/update: `department_id` exists, `name` required, `email` required\|email\|unique, `salary` integer min 0.

### Input mẫu (POST JSON hoặc form)

```json
{
  "department_id": 1,
  "name": "Nguyen Van Test",
  "email": "van.test@example.com",
  "salary": 12000000
}
```

### Output kỳ vọng

- GET list: JSON có `data` / cấu trúc paginate; mỗi item có object `department`.
- POST thành công: HTTP 201 + bản ghi mới.
- Video demo đủ GET list, POST, PUT, DELETE (Postman/Insomnia hoặc trình duyệt cho GET).

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 10                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG                                        ║
║  2. Video OBS + camera mặt: demo 4 thao tác CRUD JSON        ║
║  3. Thuyết trình: vì sao phải with('department')?            ║
║  4. Nộp link Repo + Drive                                    ║
╚══════════════════════════════════════════════════════════════╝
```
