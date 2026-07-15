# MiniShop — Dữ liệu chuẩn (Single Source of Truth)

> Mọi phiếu **P01–P08 (phần 8 SP gốc)** phải dùng đúng dataset này.  
> Autograder / giảng viên lấy EXPECT từ đây — **không đổi số liệu tùy tiện**.

## 1. Categories (3)

| id | name | description |
|----|------|-------------|
| 1 | Ban phim | Danh muc ban phim co / membrane |
| 2 | Chuot | Danh muc chuot may tinh |
| 3 | Man hinh | Danh muc man hinh |

> Sau khi migrate Laravel, `id` có thể khác nếu seed không ép id — khi đó **map theo `name`**, không hard-code id trong seeder product nếu không chắc.

## 2. Products gốc (8) — bắt buộc đủ SKU

| sku | name | category name | price | qty | line_total |
|-----|------|---------------|------:|----:|----------:|
| KB-01 | Keychron K2 | Ban phim | 1890000 | 3 | 5670000 |
| KB-02 | Akko 3087 | Ban phim | 1290000 | 5 | 6450000 |
| KB-03 | Leopold FC660M | Ban phim | 2750000 | 2 | 5500000 |
| MS-01 | Logitech M331 | Chuot | 290000 | 10 | 2900000 |
| MS-02 | Razer Viper | Chuot | 990000 | 4 | 3960000 |
| MS-03 | Xiaomi Silent | Chuot | 250000 | 8 | 2000000 |
| MN-01 | Dell 24 inch | Man hinh | 3200000 | 2 | 6400000 |
| MN-02 | LG UltraFine | Man hinh | 8500000 | 1 | 8500000 |

## 3. Hằng số chấm ổn định (CORE_8)

| Key | Value |
|-----|------:|
| `product_count_core` | **8** |
| `category_count_core` | **3** |
| `inventory_value_core` | **41380000** |
| Ban phim — count / value | **3** / **17620000** |
| Chuot — count / value | **3** / **8860000** |
| Man hinh — count / value | **2** / **14900000** |

### Quy tắc `stockLevel(qty)`

| Điều kiện | Output đúng (chuỗi exact) |
|-----------|---------------------------|
| `qty >= 5` | `Du` |
| `qty >= 2` | `Sap het` |
| else | `Can nhap` |

| sku | qty | stockLevel |
|-----|----:|------------|
| KB-01 | 3 | Sap het |
| KB-02 | 5 | Du |
| KB-03 | 2 | Sap het |
| MS-01 | 10 | Du |
| MS-02 | 4 | Sap het |
| MS-03 | 8 | Du |
| MN-01 | 2 | Sap het |
| MN-02 | 1 | Can nhap |

### Quy tắc `rankInventory(total)`

| Điều kiện | Output |
|-----------|--------|
| `< 15000000` | `Nho` |
| `< 35000000` | `Trung binh` |
| else | `Lon` |

Với `41380000` → **`Lon`**.

## 4. PHP array mẫu (copy vào `data.php`)

```php
<?php
$categories = [
    ['id' => 1, 'name' => 'Ban phim', 'description' => 'Danh muc ban phim co / membrane'],
    ['id' => 2, 'name' => 'Chuot', 'description' => 'Danh muc chuot may tinh'],
    ['id' => 3, 'name' => 'Man hinh', 'description' => 'Danh muc man hinh'],
];

$products = [
    ['sku' => 'KB-01', 'name' => 'Keychron K2', 'category_id' => 1, 'price' => 1890000, 'qty' => 3],
    ['sku' => 'KB-02', 'name' => 'Akko 3087', 'category_id' => 1, 'price' => 1290000, 'qty' => 5],
    ['sku' => 'KB-03', 'name' => 'Leopold FC660M', 'category_id' => 1, 'price' => 2750000, 'qty' => 2],
    ['sku' => 'MS-01', 'name' => 'Logitech M331', 'category_id' => 2, 'price' => 290000, 'qty' => 10],
    ['sku' => 'MS-02', 'name' => 'Razer Viper', 'category_id' => 2, 'price' => 990000, 'qty' => 4],
    ['sku' => 'MS-03', 'name' => 'Xiaomi Silent', 'category_id' => 2, 'price' => 250000, 'qty' => 8],
    ['sku' => 'MN-01', 'name' => 'Dell 24 inch', 'category_id' => 3, 'price' => 3200000, 'qty' => 2],
    ['sku' => 'MN-02', 'name' => 'LG UltraFine', 'category_id' => 3, 'price' => 8500000, 'qty' => 1],
];
```

## 5. Sau Phiếu 08 (Seeder + Faker)

| Key | Value | Ghi chú |
|-----|------:|---------|
| `category_count_seed` | 3 | vẫn 3 DM cố định |
| `product_count_seed` | **28** | 8 gốc + 20 Faker |
| `inventory_value_seed` | **không cố định** | phụ thuộc Faker — **không** chấm bằng 41380000 |
| `core_skus_present` | 8 SKU ở mục 2 | bắt buộc vẫn tồn tại sau seed |

Khi cần số inventory ổn định để chấm (P01–P06, P03): chỉ tính trên **CORE_8**.

## 6. Auth demo (Phiếu 03)

| Field | Value |
|-------|-------|
| username | `admin` |
| password | `MiniShop@03` |
| session keys | `auth=true`, `username=admin` |

## 7. DB names

| Phiếu | Database |
|-------|----------|
| P04–P05 (PHP PDO) | `minishop_cse485` |
| P08–P12 (Laravel) | `minishop_laravel` |
