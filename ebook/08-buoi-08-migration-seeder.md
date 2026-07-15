# BUỔI 8: Database Query & Thiết kế CSDL trong Laravel

> **Thời lượng gợi ý:** 3–4 giờ  
> **Tiên quyết:** Buổi 6–7  
> **Kết quả đầu ra:** Cấu hình `.env`; Migration 2 bảng quan hệ; Seeder + Faker sinh dữ liệu  
> **Phiếu học tập liên quan:** Phiếu 9  
> **Thiết kế mẫu:** `categories` (1) — (n) `products`

---

## 1. Mục tiêu buổi học

1. Kết nối Laravel với MySQL qua `.env`.
2. Viết Migration tạo `categories` và `products` (FK).
3. Viết Seeder dùng Faker để random dữ liệu.
4. Chạy `migrate` + `db:seed` thành công.

---

## 2. Quan hệ CSDL sẽ dùng xuyên suốt

```
categories                      products
┌──────────────┐               ┌─────────────────────┐
│ id (PK)      │◄─────┐        │ id (PK)             │
│ name         │      │        │ category_id (FK)    │──► categories.id
│ description  │      └────────│ name                │
│ timestamps   │               │ price               │
└──────────────┘               │ description         │
                               │ timestamps          │
                               └─────────────────────┘

Một Category có nhiều Product
Một Product thuộc một Category
```

---

## 3. Cấu hình `.env`

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=cse485_laravel
DB_USERNAME=root
DB_PASSWORD=
```

Tạo database trống `cse485_laravel` trong phpMyAdmin (hoặc SQL `CREATE DATABASE`).

Kiểm tra:

```bash
php artisan migrate:status
# hoặc
php artisan db:show
```

Nếu đổi `.env` mà config bị cache:

```bash
php artisan config:clear
```

---

## 4. Migration

### 4.1. Tạo file

```bash
php artisan make:migration create_categories_table
php artisan make:migration create_products_table
```

> Tạo **categories trước** products (vì có khóa ngoại). Timestamp trong tên file quyết định thứ tự chạy.

### 4.2. Categories

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('categories', function (Blueprint $table) {
            $table->id();
            $table->string('name', 100);
            $table->text('description')->nullable();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('categories');
    }
};
```

### 4.3. Products (có FK)

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->foreignId('category_id')
                ->constrained('categories')
                ->cascadeOnDelete(); // xóa category → xóa products thuộc nó
            $table->string('name', 150);
            $table->unsignedInteger('price'); // VND, đơn vị đồng
            $table->text('description')->nullable();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('products');
    }
};
```

Chạy:

```bash
php artisan migrate
```

Rollback khi cần:

```bash
php artisan migrate:rollback
php artisan migrate:fresh   # XÓA hết bảng rồi migrate lại — cẩn thận!
```

---

## 5. Model (tạo sẵn cho Buổi 9)

```bash
php artisan make:model Category
php artisan make:model Product
```

Tạm thời chỉ cần `$fillable` (chi tiết Eloquent ở Buổi 9–10):

```php
// app/Models/Category.php
protected $fillable = ['name', 'description'];

// app/Models/Product.php
protected $fillable = ['category_id', 'name', 'price', 'description'];
```

---

## 6. Seeder & Faker

### 6.1. Tạo seeder

```bash
php artisan make:seeder CategorySeeder
php artisan make:seeder ProductSeeder
```

### 6.2. `database/seeders/CategorySeeder.php`

```php
<?php

namespace Database\Seeders;

use App\Models\Category;
use Illuminate\Database\Seeder;

class CategorySeeder extends Seeder
{
    public function run(): void
    {
        $items = [
            ['name' => 'Laptop', 'description' => 'May tinh xach tay'],
            ['name' => 'Chuot', 'description' => 'Chuot may tinh'],
            ['name' => 'Ban phim', 'description' => 'Ban phim co / membrane'],
            ['name' => 'Man hinh', 'description' => 'Monitor'],
        ];

        foreach ($items as $item) {
            Category::create($item);
        }
    }
}
```

### 6.3. `database/seeders/ProductSeeder.php`

```php
<?php

namespace Database\Seeders;

use App\Models\Category;
use App\Models\Product;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class ProductSeeder extends Seeder
{
    public function run(): void
    {
        $faker = \Faker\Factory::create('vi_VN');
        $categoryIds = Category::pluck('id')->all();

        if ($categoryIds === []) {
            $this->command?->warn('Chua co category — chay CategorySeeder truoc.');
            return;
        }

        for ($i = 0; $i < 50; $i++) {
            Product::create([
                'category_id' => $faker->randomElement($categoryIds),
                'name' => $faker->words(3, true),
                'price' => $faker->numberBetween(100000, 30000000),
                'description' => $faker->sentence(12),
            ]);
        }
    }
}
```

### 6.4. Gọi trong `DatabaseSeeder`

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        $this->call([
            CategorySeeder::class,
            ProductSeeder::class,
        ]);
    }
}
```

Chạy:

```bash
php artisan db:seed
# hoặc làm sạch + migrate + seed một lệnh:
php artisan migrate:fresh --seed
```

Kiểm tra trong phpMyAdmin hoặc Tinker:

```bash
php artisan tinker
>>> \App\Models\Category::count()
>>> \App\Models\Product::count()
```

---

## 7. Query Builder nhanh (xem trước Eloquent)

```php
use Illuminate\Support\Facades\DB;

$rows = DB::table('products')
    ->join('categories', 'products.category_id', '=', 'categories.id')
    ->select('products.*', 'categories.name as category_name')
    ->limit(10)
    ->get();
```

Buổi 9 sẽ dùng Eloquent thay vì viết join thủ công.

---

## 8. Lỗi thường gặp

| Lỗi | Cách xử |
|-----|---------|
| `SQLSTATE[HY000] [1049]` | Sai tên DB trong `.env` |
| `Cannot add foreign key` | Thứ tự migration / bảng cha chưa có |
| Faker không có locale `vi_VN` | Đổi `'en_US'` hoặc cài đủ faker |
| `Add fillable` mass assignment | Thêm cột vào `$fillable` |

---

## 9. Checklist

- [ ] `.env` kết nối MySQL OK
- [ ] `categories` + `products` migrate thành công, có FK
- [ ] Seed ≥ 4 categories và 50 products
- [ ] Hiểu `migrate:fresh --seed` (và rủi ro xóa dữ liệu)

**Cầu nối Buổi 9:** Thao tác CRUD bằng **Eloquent ORM** (All, Find, Create, Update, Delete).

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 8/12*
