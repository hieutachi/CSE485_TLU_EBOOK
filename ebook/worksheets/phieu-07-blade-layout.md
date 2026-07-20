# Phiếu 07 — Blade Layout Admin MiniShop

| | |
|---|---|
| **Buổi** | 7 — đọc kèm [Buổi 07](../07-buoi-07-blade.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-07` (tiếp `cse485-ms-06`) |
| **Nhận từ Phiếu 06** | 4 route Admin + stats giả |
| **Mang sang Phiếu 08–12** | Cùng layout cho Migration & CRUD thật |

### Chuẩn đầu ra (CLO)

1. Giải thích được Blade: `{{ }}` escape, `@extends` / `@section` / `@yield`.  
2. Có `layouts/admin.blade.php` + sidebar MiniShop.  
3. 4 trang con kế thừa layout; active menu phân biệt.  
4. Flash demo hoạt động (`@csrf` sẵn sàng cho form sau này).

---

## 1. Lý thuyết — vì sao cần Layout?

### 1.1. Đau ở P06

Bốn view copy-paste cùng nav → sửa menu phải sửa 4 file.

```
P06: home/about/… mỗi file một nav
P07: 1 layout + @yield('content') ← trang con chỉ viết phần giữa
```

### 1.2. Ba chỉ thị phải thuộc

| Chỉ thị | Vai trò |
|---------|---------|
| `@extends('layouts.admin')` | Trang con kế thừa khung |
| `@section('content') … @endsection` | Nhồi nội dung vào chỗ trống |
| `@yield('content')` | Chỗ trống trong layout |

```
layouts/admin.blade.php
   sidebar cố định
   topbar @yield('page_heading')
   main: @include flash + @yield('content')
```

### 1.3. `{{ $x }}` vs `{!! $x !!}`

| Cú pháp | Ý nghĩa |
|---------|---------|
| `{{ $x }}` | Escape XSS — **mặc định dùng cái này** |
| `{!! $x !!}` | Raw HTML — chỉ khi chắc chắn an toàn |

### 1.4. `@csrf`

Mọi form POST Laravel sau này (P11–P12) **bắt buộc** `@csrf`. P07 chuẩn bị chỗ flash + thói quen.

### 1.5. Active menu (ý tưởng)

```blade
<a href="{{ route('admin.products.index') }}"
   class="{{ request()->routeIs('admin.products.*') ? 'active' : '' }}">
   San pham
</a>
```

Video nộp phải thấy Categories và Products highlight **khác nhau**.

---

## 2. Bài trên lớp (~20') — Cắt layout

### File bắt buộc

```
resources/views/layouts/admin.blade.php
resources/views/admin/dashboard.blade.php
resources/views/admin/categories/index.blade.php
resources/views/admin/products/index.blade.php
resources/views/admin/about.blade.php
resources/views/partials/flash.blade.php
```

Layout có:

1. Brand **MiniShop** + link 4 route bằng `route()`  
2. `@yield('title')`, `@yield('page_heading')`, `@yield('content')`  
3. `@include('partials.flash')`  
4. CSS đủ đọc (sidebar + content); mobile không vỡ hoàn toàn  

**Checkpoint:** đổi URL chỉ đổi content; sidebar giống nhau.

---

## 3. Bài về nhà (~30')

### A — Active menu

### B — Bảng skeleton

**Categories:** `ID | Name | Description | Actions`  
**Products:** `ID | Name | Category | Price | Actions`  

2–3 hàng **fake** + HTML comment: `<!-- fake data P07; thay Eloquent P09–11 -->`

### C — Nút “Them moi” → trang create placeholder `@extends` layout

### D — Flash demo

Route GET `/admin/flash-demo` → redirect dashboard `with('success', 'Layout OK — san sang Migration')`.

### E — README tiến độ: `[x] Blade layout`

---

## 4. Lỗi thường gặp

| Lỗi | Cách xử |
|-----|---------|
| Section không hiện | Quên `@endsection` / sai tên section |
| Menu 404 | Sai `route()` name so với `web.php` |
| CSS mất | Để style trong layout, không chỉ trang con |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 07                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo: layout + 4 trang + flash partial                   ║
║  2. Video: 4 menu (active rõ) + flash-demo                   ║
║  3. Zoom @yield/@section — giải thích vì sao không copy nav  ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 08:** layout đã có — tạo **2 bảng thật** `categories` / `products` bằng Migration + Seeder.
