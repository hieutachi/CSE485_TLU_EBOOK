# Phiếu học tập 08 — Blade Template (Admin Layout)

> **Gắn với:** Buổi 7  
> **Repo gợi ý:** `cse485-phieu-08-blade-admin`

---

## 1. Tóm tắt lý thuyết

- Blade: `{{ }}` escape; `@if`, `@foreach`.
- Layout master: `@yield`; trang con: `@extends` + `@section`.
- Form POST cần `@csrf`.
- Tách sidebar/topbar cố định, nội dung động trong `@yield('content')`.

---

## 2. Bài tập thực hành

### Yêu cầu

1. Tự chọn **một** HTML Admin Dashboard tĩnh (tự viết CSS đơn giản **hoặc** cắt từ template miễn phí).
2. Chuyển thành:
   - `resources/views/layouts/admin.blade.php` — có sidebar + `@yield('content')` + `@yield('title')`
   - Ít nhất **2 trang con** `@extends('layouts.admin')`:
     - `admin/dashboard.blade.php`
     - `admin/blank.blade.php` (nội dung “Noi dung module”)
3. Route:
   - `/admin` → dashboard  
   - `/admin/blank` → blank  

### Input / Output

- Cùng sidebar xuất hiện trên cả 2 trang.
- Đổi URL → chỉ vùng content đổi; video zoom rõ `@yield` / `@section`.

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 08                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG                                        ║
║  2. Video OBS + camera mặt: mở layout + 2 trang con,         ║
║     giải thích @extends / @section / @yield                  ║
║  3. Nộp link Repo + Drive                                    ║
╚══════════════════════════════════════════════════════════════╝
```
