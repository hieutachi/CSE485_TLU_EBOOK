# BUỔI 7: Giao diện với Blade Template Engine

> **Thời lượng gợi ý:** 3–4 giờ  
> **Tiên quyết:** Buổi 6  
> **Kết quả đầu ra:** Master layout Admin Dashboard; `@extends` / `@section` / `@yield`; truyền biến ra View  
> **Phiếu học tập liên quan:** Phiếu 8

---

## 1. Mục tiêu buổi học

1. Nắm cú pháp Blade cơ bản (`{{ }}`, chỉ thị điều kiện/lặp).
2. Xây layout master cho Admin Dashboard.
3. Tạo các trang con kế thừa layout.
4. Truyền dữ liệu từ Controller → Blade an toàn (auto-escape).

---

## 2. Blade là gì?

Blade = template engine của Laravel. File `.blade.php` được biên dịch thành PHP thuần (cache trong `storage/framework/views`).

```
Controller return view('products.index', compact('products'))
        │
        ▼
resources/views/products/index.blade.php
        │  @extends layout
        ▼
HTML hoàn chỉnh → Browser
```

---

## 3. Cú pháp Blade cơ bản

### 3.1. In dữ liệu (đã escape XSS)

```blade
{{ $title }}                 {{-- htmlspecialchars tự động --}}
{!! $htmlTinCay !!}          {{-- KHÔNG escape — chỉ dùng khi chắc chắn an toàn --}}
{{ $name ?? 'Khach' }}       {{-- mặc định nếu null --}}
```

### 3.2. Điều kiện & vòng lặp

```blade
@if($count > 0)
    <p>Co {{ $count }} san pham</p>
@elseif($count === 0)
    <p>Trong</p>
@else
    <p>Khong hop le</p>
@endif

@foreach($products as $product)
    <li>{{ $product->name }}</li>
@endforeach

@forelse($products as $product)
    <li>{{ $product->name }}</li>
@empty
    <p>Chua co san pham</p>
@endforelse
```

### 3.3. CSRF (bắt buộc với form POST)

```blade
<form method="POST" action="/products">
    @csrf
    <input name="name">
    <button>Luu</button>
</form>
```

---

## 4. Layout Master — trái tim Admin Dashboard

```
layouts/admin.blade.php          ← khung (sidebar + topbar + @yield)
        ▲
        │ @extends
        │
pages kế thừa:
  dashboard.blade.php
  products/index.blade.php
  ...
```

### 4.1. `resources/views/layouts/admin.blade.php`

```blade
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>@yield('title', 'Admin') - CSE485</title>
    <style>
        :root {
            --bg: #f3f4f6;
            --sidebar: #111827;
            --text: #111827;
            --muted: #6b7280;
            --accent: #2563eb;
        }
        * { box-sizing: border-box; }
        body {
            margin: 0;
            font-family: "Segoe UI", Tahoma, sans-serif;
            background: var(--bg);
            color: var(--text);
        }
        .app { display: flex; min-height: 100vh; }
        .sidebar {
            width: 240px;
            background: var(--sidebar);
            color: #fff;
            padding: 1.25rem;
        }
        .sidebar a {
            display: block;
            color: #d1d5db;
            text-decoration: none;
            padding: .5rem 0;
        }
        .sidebar a:hover { color: #fff; }
        .main { flex: 1; display: flex; flex-direction: column; }
        .topbar {
            background: #fff;
            border-bottom: 1px solid #e5e7eb;
            padding: 1rem 1.5rem;
            font-weight: 600;
        }
        .content { padding: 1.5rem; }
        .card {
            background: #fff;
            border: 1px solid #e5e7eb;
            border-radius: 8px;
            padding: 1rem;
        }
        table { width: 100%; border-collapse: collapse; }
        th, td { border-bottom: 1px solid #e5e7eb; padding: .75rem; text-align: left; }
        .btn {
            display: inline-block;
            background: var(--accent);
            color: #fff;
            padding: .5rem .9rem;
            border-radius: 6px;
            text-decoration: none;
            border: 0;
            cursor: pointer;
        }
        @media (max-width: 768px) {
            .app { flex-direction: column; }
            .sidebar { width: 100%; }
        }
    </style>
    @stack('styles')
</head>
<body>
<div class="app">
    <aside class="sidebar">
        <h2>CSE485 Admin</h2>
        <a href="{{ route('admin.dashboard') }}">Dashboard</a>
        <a href="{{ route('admin.products.index') }}">San pham</a>
        <a href="{{ route('admin.categories.index') }}">Danh muc</a>
    </aside>
    <div class="main">
        <header class="topbar">@yield('page_heading', 'Dashboard')</header>
        <main class="content">
            @if(session('success'))
                <p style="color: green;">{{ session('success') }}</p>
            @endif
            @yield('content')
        </main>
    </div>
</div>
@stack('scripts')
</body>
</html>
```

> CSS thuần trong layout để học Blade, không phụ thuộc CDN. Sau này có thể thay bằng Bootstrap/AdminLTE.

### 4.2. Trang con — `resources/views/admin/dashboard.blade.php`

```blade
@extends('layouts.admin')

@section('title', 'Dashboard')
@section('page_heading', 'Tong quan')

@section('content')
    <div class="card">
        <h1>Chao mung den Admin Dashboard</h1>
        <p>Day la layout master bang Blade (@extends / @section / @yield).</p>
    </div>
@endsection
```

### 4.3. Route + Controller

```php
// routes/web.php
use App\Http\Controllers\Admin\DashboardController;
use App\Http\Controllers\Admin\ProductController;
use App\Http\Controllers\Admin\CategoryController;

Route::prefix('admin')->name('admin.')->group(function () {
    Route::get('/', [DashboardController::class, 'index'])->name('dashboard');
    // products/categories sẽ hoàn thiện Buổi 11–12
    Route::get('/products', [ProductController::class, 'index'])->name('products.index');
    Route::get('/categories', [CategoryController::class, 'index'])->name('categories.index');
});
```

```bash
php artisan make:controller Admin/DashboardController
php artisan make:controller Admin/ProductController
php artisan make:controller Admin/CategoryController
```

```php
<?php
namespace App\Http\Controllers\Admin;

use App\Http\Controllers\Controller;

class DashboardController extends Controller
{
    public function index()
    {
        return view('admin.dashboard');
    }
}
```

`ProductController@index` / `CategoryController@index` tạm trả view placeholder:

```blade
{{-- resources/views/admin/products/index.blade.php --}}
@extends('layouts.admin')
@section('title', 'San pham')
@section('page_heading', 'San pham')
@section('content')
<div class="card">
    <p>Bang san pham se duoc noi Eloquent o Buoi 11.</p>
</div>
@endsection
```

---

## 5. Include & Component nhỏ

```blade
{{-- views/partials/alert.blade.php --}}
@if(session('success'))
    <div class="alert">{{ session('success') }}</div>
@endif

{{-- dùng trong layout --}}
@include('partials.alert')
```

`@stack` / `@push` — thêm CSS/JS riêng từng trang:

```blade
{{-- trang con --}}
@push('scripts')
<script>console.log('products page');</script>
@endpush
```

---

## 6. Truyền biến từ Controller

```php
public function index()
{
    $stats = [
        'products' => 12,
        'categories' => 4,
    ];

    return view('admin.dashboard', compact('stats'));
    // tương đương: view('...', ['stats' => $stats])
}
```

```blade
<p>San pham: {{ $stats['products'] }}</p>
```

---

## 7. Cắt HTML tĩnh thành Blade — quy trình

```
1. Copy HTML Admin template vào layouts/admin.blade.php
2. Thay vùng nội dung chính bằng @yield('content')
3. Thay title bằng @yield('title')
4. Tách sidebar/topbar cố định trong layout
5. Mỗi trang nghiệp vụ chỉ viết @section('content')
```

---

## 8. Checklist

- [ ] Hiểu `{{ }}` vs `{!! !!}`
- [ ] Có `layouts/admin.blade.php` với sidebar
- [ ] Dashboard / Products / Categories kế thừa layout
- [ ] Form nào dùng POST đều có `@csrf` (sẵn sàng Buổi 11)

**Cầu nối Buổi 8:** Layout đã có — cần **bảng thật** trong MySQL qua Migration + Seeder (`categories`, `products`).

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 7/12*
