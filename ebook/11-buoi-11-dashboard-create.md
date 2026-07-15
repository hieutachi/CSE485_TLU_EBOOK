# BUỔI 11: Xây dựng Admin Dashboard (Phần 1 — Hiển thị & Thêm mới)

> **Thời lượng gợi ý:** 4 giờ  
> **Tiên quyết:** Buổi 7–10  
> **Kết quả đầu ra:** Trang danh sách Products (có tên Category); Form Create + Validation  
> **Phiếu học tập liên quan:** Phiếu 11

---

## 1. Mục tiêu buổi học

1. Đổ dữ liệu Eloquent ra Blade dạng bảng.
2. Tạo form thêm Product (chọn Category).
3. Validate request; flash message thành công.
4. Giữ layout Admin đã dựng ở Buổi 7.

---

## 2. Luồng dữ liệu (nhớ thuộc)

```
MySQL products/categories
        │
        ▼
Eloquent Model (+ with category)
        │
        ▼
ProductController
        │
        ▼
Blade View (table / form)
        │
        ▼
Browser (Admin Dashboard)
```

---

## 3. Routes resource (phần index + create + store)

```php
// routes/web.php
use App\Http\Controllers\Admin\ProductController;

Route::prefix('admin')->name('admin.')->group(function () {
    Route::get('/products', [ProductController::class, 'index'])->name('products.index');
    Route::get('/products/create', [ProductController::class, 'create'])->name('products.create');
    Route::post('/products', [ProductController::class, 'store'])->name('products.store');
    // edit/update/destroy → Buổi 12
});
```

Hoặc dùng:

```bash
php artisan make:controller Admin/ProductController --resource --model=Product
```

rồi chỉ implement các method cần thiết.

---

## 4. Controller — Index / Create / Store

```php
<?php

namespace App\Http\Controllers\Admin;

use App\Http\Controllers\Controller;
use App\Models\Category;
use App\Models\Product;
use Illuminate\Http\Request;

class ProductController extends Controller
{
    public function index()
    {
        $products = Product::with('category')
            ->orderByDesc('id')
            ->paginate(10);

        return view('admin.products.index', compact('products'));
    }

    public function create()
    {
        $categories = Category::orderBy('name')->get();
        return view('admin.products.create', compact('categories'));
    }

    public function store(Request $request)
    {
        $data = $request->validate([
            'category_id' => ['required', 'exists:categories,id'],
            'name' => ['required', 'string', 'max:150'],
            'price' => ['required', 'integer', 'min:0'],
            'description' => ['nullable', 'string'],
        ], [
            'category_id.required' => 'Vui long chon danh muc.',
            'name.required' => 'Ten san pham bat buoc.',
            'price.required' => 'Gia bat buoc.',
        ]);

        Product::create($data);

        return redirect()
            ->route('admin.products.index')
            ->with('success', 'Them san pham thanh cong.');
    }
}
```

---

## 5. Views

### 5.1. `resources/views/admin/products/index.blade.php`

```blade
@extends('layouts.admin')

@section('title', 'San pham')
@section('page_heading', 'Danh sach san pham')

@section('content')
<div class="card">
    <p>
        <a class="btn" href="{{ route('admin.products.create') }}">+ Them san pham</a>
    </p>

    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Ten</th>
                <th>Danh muc</th>
                <th>Gia</th>
                <th>Thao tac</th>
            </tr>
        </thead>
        <tbody>
            @forelse($products as $product)
                <tr>
                    <td>{{ $product->id }}</td>
                    <td>{{ $product->name }}</td>
                    <td>{{ $product->category?->name ?? '—' }}</td>
                    <td>{{ number_format($product->price) }} đ</td>
                    <td>
                        {{-- Link Sua/Xoa se them Buoi 12 --}}
                        <span style="color:#6b7280">Sap co Edit/Delete</span>
                    </td>
                </tr>
            @empty
                <tr>
                    <td colspan="5">Chua co san pham.</td>
                </tr>
            @endforelse
        </tbody>
    </table>

    <div style="margin-top:1rem">
        {{ $products->links() }}
    </div>
</div>
@endsection
```

> Nếu pagination chưa có CSS đẹp, vẫn dùng được mặc định của Laravel. Có thể thêm Bootstrap sau.

### 5.2. `resources/views/admin/products/create.blade.php`

```blade
@extends('layouts.admin')

@section('title', 'Them san pham')
@section('page_heading', 'Them san pham')

@section('content')
<div class="card">
    @if($errors->any())
        <ul style="color:#b91c1c;">
            @foreach($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    @endif

    <form method="POST" action="{{ route('admin.products.store') }}">
        @csrf

        <p>
            <label>Danh muc</label><br>
            <select name="category_id" required>
                <option value="">-- Chon --</option>
                @foreach($categories as $category)
                    <option value="{{ $category->id }}" @selected(old('category_id') == $category->id)>
                        {{ $category->name }}
                    </option>
                @endforeach
            </select>
        </p>

        <p>
            <label>Ten san pham</label><br>
            <input type="text" name="name" value="{{ old('name') }}" required>
        </p>

        <p>
            <label>Gia (VND)</label><br>
            <input type="number" name="price" value="{{ old('price') }}" min="0" required>
        </p>

        <p>
            <label>Mo ta</label><br>
            <textarea name="description" rows="4">{{ old('description') }}</textarea>
        </p>

        <button class="btn" type="submit">Luu</button>
        <a href="{{ route('admin.products.index') }}">Huy</a>
    </form>
</div>
@endsection
```

---

## 6. Validation — điều cần nhớ

```
Client (HTML required)  → trải nghiệm UX
Server ($request->validate) → BẮT BUỘC (không tin Client)
```

Khi fail: Laravel redirect back kèm `$errors` + `old()` để giữ input.

---

## 7. Làm tương tự cho Categories (bài tập bắt buộc)

Tự viết `CategoryController` index/create/store + 2 view. Không có FK chọn select — form đơn giản hơn Products.

---

## 8. Checklist

- [ ] `/admin/products` hiển thị bảng có tên danh mục
- [ ] Dùng `with('category')`
- [ ] Form create + `@csrf` + validate
- [ ] Flash `success` sau khi lưu

**Cầu nối Buổi 12:** Hoàn thiện **Edit / Update / Delete** và tổng kết toàn luồng.

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 11/12*
