# BUỔI 10: Eloquent Relationship & Tối ưu truy vấn

> **Thời lượng gợi ý:** 3 giờ  
> **Tiên quyết:** Buổi 9  
> **Kết quả đầu ra:** Định nghĩa `hasMany` / `belongsTo`; dùng `with()` chống N+1  
> **Phiếu học tập liên quan:** [Phiếu 10](./worksheets/phieu-10-relationship.md) — chuỗi MiniShop

---

## 1. Mục tiêu buổi học

1. Hiểu quan hệ 1–n giữa Category và Product.
2. Gọi `$category->products` và `$product->category`.
3. Nhận diện bài toán **N+1 queries**.
4. Sửa bằng Eager Loading: `Product::with('category')->get()`.

---

## 2. Quan hệ trong code

Đã khai báo ở Buổi 9 — nhắc lại:

```php
// Category.php
public function products(): HasMany
{
    return $this->hasMany(Product::class);
    // mặc định FK: products.category_id
}

// Product.php
public function category(): BelongsTo
{
    return $this->belongsTo(Category::class);
}
```

```
Category (1) ──────< Product (n)
   id                    category_id
```

---

## 3. Lazy Loading — tiện nhưng dễ chậm

```php
$products = Product::all(); // 1 query

foreach ($products as $product) {
    // Mỗi vòng lặp có thể phát sinh 1 query category!
    echo $product->category->name;
}
```

### Bài toán N+1 (ASCII)

```
Query 1: SELECT * FROM products;          ← 1 lần
Query 2: SELECT * FROM categories WHERE id = 1;
Query 3: SELECT * FROM categories WHERE id = 3;
Query 4: SELECT * FROM categories WHERE id = 1;
...
Nếu 50 products → có thể tới 1 + 50 = 51 queries (N+1)
```

---

## 4. Eager Loading với `with()` — giải pháp chuẩn

```php
$products = Product::with('category')->get();
// Query 1: SELECT * FROM products
// Query 2: SELECT * FROM categories WHERE id IN (1,2,3,...)
// Tổng ≈ 2 queries dù có 50 products
```

```
Lazy (N+1)                      Eager (with)
───────────                     ────────────
products ──► cat ──► cat ...    products
                                categories (IN ids)
```

Lồng nhiều quan hệ (preview):

```php
Product::with(['category', 'category.products'])->get(); // ít dùng mẫu này
Category::with('products')->get();
```

---

## 5. Các truy vấn quan hệ hay dùng

```php
// Lấy products của 1 category
$category = Category::with('products')->findOrFail(1);
$category->products;

// Lọc theo quan hệ
Product::whereHas('category', function ($q) {
    $q->where('name', 'Laptop');
})->get();

// Đếm số product mỗi category
Category::withCount('products')->get();
// → mỗi category có thuộc tính products_count

// Tạo product thông qua quan hệ
$category = Category::findOrFail(1);
$category->products()->create([
    'name' => 'MacBook Air',
    'price' => 28000000,
    'description' => 'M3',
]);
```

---

## 6. Debug số query — bắt buộc khi học

Cách 1: Laravel Debugbar (tùy chọn cài package).

Cách 2: Log query tạm thời trong `AppServiceProvider::boot()`:

```php
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Log;

DB::listen(function ($query) {
    Log::info($query->sql, $query->bindings);
});
```

Xem `storage/logs/laravel.log`.

Cách 3: Tinker đếm nhanh cảm nhận:

```php
\DB::enableQueryLog();
Product::with('category')->limit(20)->get()->each(fn ($p) => $p->category->name);
count(\DB::getQueryLog());
```

---

## 7. Controller demo — danh sách có tên category

```php
public function index()
{
    $products = Product::with('category')
        ->orderByDesc('id')
        ->paginate(10);

    return view('admin.products.index', compact('products'));
}
```

Blade (xem trước Buổi 11):

```blade
@foreach($products as $product)
    <tr>
        <td>{{ $product->name }}</td>
        <td>{{ $product->category?->name }}</td>
        <td>{{ number_format($product->price) }}</td>
    </tr>
@endforeach
```

`?->` (nullsafe): nếu thiếu category (dữ liệu bẩn) không làm trắng trang.

---

## 8. Lazy vs Eager — khi nào dùng gì?

| Tình huống | Nên dùng |
|------------|----------|
| List nhiều bản ghi + quan hệ | `with()` Eager |
| Chỉ cần 1 model + quan hệ | Lazy chấp nhận được |
| API lớn, nhiều quan hệ | `with([...])` chọn lọc cột `with('category:id,name')` |

```php
Product::with('category:id,name')->get();
// Lưu ý: phải select khóa id của quan hệ khi giới hạn cột
```

---

## 9. Checklist

- [ ] Gọi được `$product->category->name`
- [ ] Gọi được `$category->products`
- [ ] Giải thích N+1 trên giấy
- [ ] List products luôn dùng `with('category')`

## 10. Bài tập trên lớp & về nhà (chuỗi MiniShop)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp** | Khai báo quan hệ + JSON `with` / `withCount` | [Phiếu 10 — mục 2](./worksheets/phieu-10-relationship.md) |
| **Về nhà** | Đo N+1 + Admin products thật (paginate) | [Phiếu 10 — mục 3](./worksheets/phieu-10-relationship.md) |

**Cầu nối Buổi 11:** Form **Create** Categories & Products trên Dashboard (validate + select DM).

→ [Buổi 11](./11-buoi-11-dashboard-create.md) · [Phiếu 11](./worksheets/phieu-11-dashboard-create.md)

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 10/12*
