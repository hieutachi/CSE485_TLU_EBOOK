# BUỔI 12: Xây dựng Admin Dashboard (Phần 2 — Sửa, Xóa & Hoàn thiện)

> **Thời lượng gợi ý:** 3–4 giờ  
> **Tiên quyết:** Buổi 11  
> **Kết quả đầu ra:** CRUD Products (và Categories) hoàn chỉnh trên Dashboard  
> **Phiếu học tập liên quan:** [Phiếu 12](./worksheets/phieu-12-dashboard-crud.md) — chuỗi MiniShop

---

## 1. Mục tiêu buổi học

1. Form Edit + Update với Validation.
2. Delete an toàn (POST/DELETE + CSRF, không xóa bằng GET thuần).
3. Tổng kết luồng DB → Model → Controller → Blade → Frontend.
4. Tự kiểm tra checklist trước Project nhóm.

---

## 2. Bổ sung Routes

```php
Route::prefix('admin')->name('admin.')->group(function () {
    Route::get('/products/{product}/edit', [ProductController::class, 'edit'])->name('products.edit');
    Route::put('/products/{product}', [ProductController::class, 'update'])->name('products.update');
    Route::delete('/products/{product}', [ProductController::class, 'destroy'])->name('products.destroy');
});
```

Laravel Route Model Binding: tham số `{product}` tự `Product::findOrFail($id)`.

---

## 3. Controller — Edit / Update / Destroy

```php
public function edit(Product $product)
{
    $categories = Category::orderBy('name')->get();
    return view('admin.products.edit', compact('product', 'categories'));
}

public function update(Request $request, Product $product)
{
    $data = $request->validate([
        'category_id' => ['required', 'exists:categories,id'],
        'name' => ['required', 'string', 'max:150'],
        'price' => ['required', 'integer', 'min:0'],
        'description' => ['nullable', 'string'],
    ]);

    $product->update($data);

    return redirect()
        ->route('admin.products.index')
        ->with('success', 'Cap nhat thanh cong.');
}

public function destroy(Product $product)
{
    $product->delete();

    return redirect()
        ->route('admin.products.index')
        ->with('success', 'Da xoa san pham.');
}
```

---

## 4. View Edit

`resources/views/admin/products/edit.blade.php`:

```blade
@extends('layouts.admin')

@section('title', 'Sua san pham')
@section('page_heading', 'Sua san pham #' . $product->id)

@section('content')
<div class="card">
    @if($errors->any())
        <ul style="color:#b91c1c;">
            @foreach($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    @endif

    <form method="POST" action="{{ route('admin.products.update', $product) }}">
        @csrf
        @method('PUT')

        <p>
            <label>Danh muc</label><br>
            <select name="category_id" required>
                @foreach($categories as $category)
                    <option value="{{ $category->id }}"
                        @selected(old('category_id', $product->category_id) == $category->id)>
                        {{ $category->name }}
                    </option>
                @endforeach
            </select>
        </p>

        <p>
            <label>Ten</label><br>
            <input type="text" name="name" value="{{ old('name', $product->name) }}" required>
        </p>

        <p>
            <label>Gia</label><br>
            <input type="number" name="price" value="{{ old('price', $product->price) }}" min="0" required>
        </p>

        <p>
            <label>Mo ta</label><br>
            <textarea name="description" rows="4">{{ old('description', $product->description) }}</textarea>
        </p>

        <button class="btn" type="submit">Cap nhat</button>
        <a href="{{ route('admin.products.index') }}">Huy</a>
    </form>
</div>
@endsection
```

> HTML form chỉ hỗ trợ GET/POST → `@method('PUT')` giả lập method PUT cho Laravel.

---

## 5. Nút Sửa / Xóa trên bảng index

Thay cột thao tác Buổi 11:

```blade
<td>
    <a href="{{ route('admin.products.edit', $product) }}">Sua</a>

    <form action="{{ route('admin.products.destroy', $product) }}"
          method="POST"
          style="display:inline"
          onsubmit="return confirm('Ban chac chan xoa?')">
        @csrf
        @method('DELETE')
        <button type="submit" style="color:#b91c1c;background:none;border:0;cursor:pointer">
            Xoa
        </button>
    </form>
</td>
```

**Không** dùng `GET /products/1/delete` — crawler/prefetch có thể xóa nhầm; thiếu CSRF.

---

## 6. Tổng kết luồng dữ liệu toàn môn

```
┌─────────┐    Migration/Seeder    ┌──────────┐
│  MySQL  │◄──────────────────────►│  Schema  │
└────▲────┘                        └──────────┘
     │
     │ Eloquent (Model + Relationship + with)
     │
┌────┴─────┐   validate / redirect   ┌────────────┐
│ Controller│◄──────────────────────►│   Route    │
└────┬─────┘                         └────────────┘
     │ compact / view()
     ▼
┌──────────┐   HTML + Blade          ┌──────────┐
│   View   │ ───────────────────────►│ Browser  │
└──────────┘                         └──────────┘
```

Bạn đã đi đủ đường:

| Giai đoạn | Công nghệ |
|-----------|-----------|
| PHP thuần | Biến, hàm, OOP, Session |
| PDO | SQL an toàn |
| MVC mini | Tách trách nhiệm |
| Laravel | Route, Blade, Migration, Eloquent, CRUD Dashboard |

---

## 7. Checklist hoàn thiện Buổi 12

- [ ] Edit hiển thị đúng dữ liệu cũ (`old(..., $model->field)`)
- [ ] Update validate + flash success
- [ ] Delete bằng DELETE + CSRF + confirm
- [ ] Categories cũng CRUD đủ (đối xứng Products)
- [ ] List products luôn `with('category')`
- [ ] Không còn SQL Injection (Eloquent/validate) và XSS cơ bản (`{{ }}`)

---

## 8. Ôn nhanh trước Project nhóm

1. Mỗi bảng → Migration + Model + (tuỳ) Seeder  
2. Quan hệ → `hasMany` / `belongsTo` + `with`  
3. Mỗi tài nguyên → Controller + Blade CRUD  
4. Layout admin thống nhất  
5. Validation server-side  

## 9. Bài tập trên lớp & về nhà — ĐÍCH CUỐI chuỗi MiniShop

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp** | Edit Category + thử Delete Product | [Phiếu 12 — mục 2](./worksheets/phieu-12-dashboard-crud.md) |
| **Về nhà (≥30–40')** | CRUD **đủ cả 2 bảng** + `belongsTo` + `with()` + `FINAL.md` | [Phiếu 12 — mục 3](./worksheets/phieu-12-dashboard-crud.md) |

Sau khi hoàn thành Phiếu 12, bạn đã **tự xử lý** bài toán Laravel: 2 bảng liên kết, CRUD Admin đầy đủ.

**Tiếp theo (điểm mở rộng):** [Project cuối môn Buổi 13–15](./13-project-cuoi-mon.md) · [Mục lục phiếu](./worksheets/README.md)

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 12/12*
