# Phiếu học tập 11 — CRUD Dashboard (Employees)

> **Gắn với:** Buổi 11 & 12 (tổng hợp)  
> **Repo gợi ý:** `cse485-phieu-11-employees-dashboard`  
> **Schema:** `departments` + `employees` (từ Phiếu 9)

---

## 1. Tóm tắt lý thuyết

- Controller lấy dữ liệu Eloquent → Blade table.
- Form Create/Edit: `@csrf`, `@method`, `old()`, `$errors`.
- Validation server-side bắt buộc.
- Delete bằng `DELETE` + confirm, không dùng GET.
- Hiển thị quan hệ: tên `department` (eager `with`).
- Layout Admin `@extends`.

---

## 2. Bài tập thực hành

### Yêu cầu hoàn thiện trang quản lý Employees

1. Layout admin (có thể tái sử dụng Phiếu 8).
2. Routes resource (hoặc khai báo đủ):
   - index, create, store, edit, update, destroy
3. **Index:** bảng cột `ID | Name | Email | Salary | Department | Actions`
4. **Create / Edit:** select danh sách departments; validate đầy đủ.
5. **Delete:** form DELETE + confirm.
6. Flash `session('success')` sau store/update/destroy.

### Input / Output

| Hành động | Kết quả nhìn thấy |
|-----------|-------------------|
| Mở index | Danh sách employees, cột Department là **tên** không phải id |
| Thêm employee hợp lệ | Redirect index + thông báo thành công + có dòng mới |
| Bỏ trống name | Báo lỗi validation, giữ lại input (`old`) |
| Sửa salary | Cập nhật đúng trên bảng |
| Xóa | Hết dòng đó sau confirm |

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 11 (TỔNG HỢP BUỔI 11–12)                      ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG — README đủ lệnh cài + migrate --seed  ║
║  2. Video OBS + camera mặt (dài hơn các phiếu trước):        ║
║     • Show layout + list có tên department                   ║
║     • Demo Thêm / Sửa / Xóa / lỗi validation                 ║
║     • Mở Controller chỉ with('department') + validate        ║
║  3. Thuyết trình 1 phút: luồng DB → Model → Controller →     ║
║     Blade → Browser                                          ║
║  4. Nộp link Repo + Google Drive cho Giảng viên              ║
╚══════════════════════════════════════════════════════════════╝
```

---

**Chúc mừng:** Hoàn thành 11 phiếu = bạn đã sẵn sàng cho Project cuối môn (Buổi 13–15).
