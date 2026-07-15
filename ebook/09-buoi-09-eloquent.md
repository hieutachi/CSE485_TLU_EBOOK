# BUỔI 9: Eloquent ORM — Thao tác dữ liệu cơ bản

> **Thời lượng gợi ý:** 3 giờ  
> **Tiên quyết:** Buổi 8 (đã có bảng + model)  
> **Kết quả đầu ra:** CRUD bằng Eloquent; trả JSON để kiểm tra (chưa cần UI đầy đủ)  
> **Phiếu học tập liên quan:** Phiếu 10

---

## 1. Mục tiêu buổi học

1. Hiểu Eloquent map Class ↔ Bảng.
2. Dùng `all`, `find`, `create`, `update`, `delete`.
3. Lọc / sắp xếp / phân trang cơ bản.
4. Tránh Mass Assignment bằng `$fillable`.

---

## 2. Eloquent là gì?

```
Class Product  ◄──── map ────►  bảng products
$product->name                  cột name
Product::create([...])          INSERT
Product::find(1)                SELECT ... WHERE id=1
```

So với PDO Buổi 4: bạn không viết SQL cho CRUD thường ngày; Eloquent sinh SQL giúp (vẫn nên hiểu SQL bên dưới).

---

## 3. Hoàn thiện Model

```php
<?php
// app/Models/Category.php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Category extends Model
{
    protected $fillable = ['name', 'description'];

    // Quan hệ sẽ dùng sâu ở Buổi 10
    public function products(): HasMany
    {
        return $this->hasMany(Product::class);
    }
}
```

```php
<?php
// app/Models/Product.php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Product extends Model
{
    protected $fillable = [
        'category_id',
        'name',
        'price',
        'description',
    ];

    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class);
    }
}
```

---

## 4. Các thao tác cốt lõi

Dùng Tinker để thực hành nhanh:

```bash
php artisan tinker
```

### 4.1. Read

```php
Product::all();
Product::find(1);           // null nếu không có
Product::findOrFail(1);     // 404 nếu không có
Product::where('price', '>', 1000000)->get();
Product::orderBy('price', 'desc')->limit(5)->get();
Product::paginate(10);      // phân trang
```

### 4.2. Create

```php
Product::create([
    'category_id' => 1,
    'name' => 'Logitech MX Master',
    'price' => 2500000,
    'description' => 'Chuot ergonomic',
]);

// Cách 2
$p = new Product();
$p->category_id = 1;
$p->name = 'Demo';
$p->price = 100000;
$p->save();
```

### 4.3. Update

```php
$p = Product::findOrFail(1);
$p->update(['price' => 2600000]);

// hoặc
$p->price = 2600000;
$p->save();
```

### 4.4. Delete

```php
Product::findOrFail(1)->delete();
Product::destroy([2, 3, 4]);
```

---

## 5. API JSON tạm để kiểm tra (Route + Controller)

```bash
php artisan make:controller Api/ProductApiController
```

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Product;
use Illuminate\Http\Request;

class ProductApiController extends Controller
{
    public function index()
    {
        return Product::orderByDesc('id')->paginate(10);
    }

    public function show(int $id)
    {
        return Product::findOrFail($id);
    }

    public function store(Request $request)
    {
        $data = $request->validate([
            'category_id' => ['required', 'exists:categories,id'],
            'name' => ['required', 'string', 'max:150'],
            'price' => ['required', 'integer', 'min:0'],
            'description' => ['nullable', 'string'],
        ]);

        $product = Product::create($data);
        return response()->json($product, 201);
    }

    public function update(Request $request, int $id)
    {
        $product = Product::findOrFail($id);
        $data = $request->validate([
            'category_id' => ['sometimes', 'exists:categories,id'],
            'name' => ['sometimes', 'string', 'max:150'],
            'price' => ['sometimes', 'integer', 'min:0'],
            'description' => ['nullable', 'string'],
        ]);
        $product->update($data);
        return $product;
    }

    public function destroy(int $id)
    {
        Product::findOrFail($id)->delete();
        return response()->json(['message' => 'Deleted']);
    }
}
```

```php
// routes/web.php (demo học tập; production nên để routes/api.php + CSRF/API auth)
use App\Http\Controllers\Api\ProductApiController;

Route::get('/api/products', [ProductApiController::class, 'index']);
Route::get('/api/products/{id}', [ProductApiController::class, 'show']);
Route::post('/api/products', [ProductApiController::class, 'store']);
Route::put('/api/products/{id}', [ProductApiController::class, 'update']);
Route::delete('/api/products/{id}', [ProductApiController::class, 'destroy']);
```

> Form POST từ trình duyệt cần `@csrf`. Khi test bằng Postman/Insomnia với web routes, nhớ gửi token hoặc tạm dùng `routes/api.php`.

---

## 6. Mass Assignment — vì sao có `$fillable`

```
Request gửi: name=Hack&is_admin=1
Nếu không có $fillable / $guarded → nguy cơ ghi đè cột nhạy cảm
```

Chỉ cho phép các cột trong `$fillable` khi `create()` / `update($array)`.

---

## 7. Sơ đồ CRUD Eloquent

```
HTTP Request
    │
    ▼
Controller validate
    │
    ▼
Product::create / find / update / delete
    │
    ▼
MySQL (SQL do Eloquent sinh)
    │
    ▼
Model / Collection / JSON
```

---

## 8. Checklist

- [ ] `Product::all()` / `find` chạy trong tinker
- [ ] Create / Update / Delete được ít nhất 1 product
- [ ] Hiểu `$fillable`
- [ ] Có route JSON đọc danh sách products

**Cầu nối Buổi 10:** Lấy `product->category->name` đúng cách với `belongsTo` / `hasMany` và **Eager Loading `with()`** để tránh N+1.

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 9/12*
