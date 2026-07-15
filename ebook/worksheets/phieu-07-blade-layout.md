# Phiếu 07 — Blade Layout Admin MiniShop

| | |
|---|---|
| **Buổi** | 7 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-07` (tiếp nối `cse485-ms-06` được) |
| **Nhận từ Phiếu 06** | 4 route Admin + stats giả |
| **Mang sang Phiếu 08–12** | Cùng layout cho CRUD thật |

### Chuẩn đầu ra (CLO)

1. Có `layouts/admin.blade.php` với `@yield('content')` + sidebar MiniShop.  
2. 4 trang `@extends` (dashboard/categories/products/about).  
3. Flash demo hoạt động; active menu phân biệt Categories vs Products.

---

## 1. Tóm tắt lý thuyết

- `{{ }}` escape; `@csrf` cho form POST.
- `@extends` / `@section` / `@yield` / `@include`.
- Một layout master — nhiều trang con chỉ viết content.

---

## 2. Bài trên lớp (~20') — Cắt layout

### File bắt buộc

```
resources/views/layouts/admin.blade.php
resources/views/admin/dashboard.blade.php
resources/views/admin/categories/index.blade.php
resources/views/admin/products/index.blade.php
resources/views/admin/about.blade.php
resources/views/partials/flash.blade.php   ← chỗ dành sẵn flash
```

### Layout phải có

1. Sidebar: brand **MiniShop**, link Dashboard / Categories / Products / About dùng `route()`.
2. Topbar: `@yield('page_heading')`.
3. Main: `@include('partials.flash')` + `@yield('content')`.
4. `<title>@yield('title', 'MiniShop')</title>`.

CSS được phép viết trong layout (không bắt buộc CDN). Ưu tiên đọc được trên cả desktop và màn hẹp (sidebar xuống dưới hoặc scroll được).

**Checkpoint:** đổi trang chỉ đổi content; sidebar giống nhau.

---

## 3. Bài về nhà (~25–30') — Component hoá & trạng thái UI

### Nhiệm vụ A — Active menu

Tự làm highlight link đang mở (một trong các cách):

- so sánh `request()->routeIs('admin.products.*')`, hoặc  
- truyền biến `$active` từ controller.

Video phải thấy Categories và Products highlight khác nhau.

### Nhiệm vụ B — Bảng skeleton Categories & Products

Trên `categories/index` và `products/index`: tạo `<table>` với header đúng tương lai:

**Categories:** `ID | Name | Description | Actions`  
**Products:** `ID | Name | Category | Price | Actions`

Body tạm: 2–3 hàng dữ liệu **hardcode** (ghi chú HTML comment `<!-- fake data Phiếu 07; thay Eloquent ở 09–11 -->`).

### Nhiệm vụ C — Nút giả lập “Them moi”

Mỗi trang list có nút/link:

- `route` tạm trỏ `#` hoặc trang `create` trống `@extends` layout, heading “Create Category/Product (coming)”.

### Nhiệm vụ D — Flash demo

Trong DashboardController (hoặc route tạm):  
`return redirect()->route('admin.dashboard')->with('success', 'Layout OK — san sang Migration');`  
khi truy cập `/admin/flash-demo` (route GET chỉ để demo).  
`partials/flash.blade.php` hiện message success.

### Nhiệm vụ E — Checklist README tiến độ

Đánh dấu:

```
- [x] Blade layout
- [ ] Migration 2 bang
```

---

## 4. Nghiệm thu ≥ 45'

| Việc | Thời gian gợi ý |
|------|-----------------|
| Layout + 4 trang extends | 15–20' |
| Active menu + bảng skeleton | 15' |
| Flash demo + create placeholder | 10' |
| Quay video + README | 5–10' |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 07                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo: layout + 4 trang con + flash partial               ║
║  2. Video: nhảy 4 menu (active rõ) + flash-demo              ║
║  3. Zoom @yield/@section trong layout                        ║
╚══════════════════════════════════════════════════════════════╝
```
