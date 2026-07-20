# Phiếu 01 — PHP cơ bản & Catalog MiniShop

| | |
|---|---|
| **Buổi** | 1 — đọc kèm [Buổi 01](../01-buoi-01-nhap-mon-php.md) |
| **Thời lượng** | ≥ 45–60 phút (trên lớp ~20' + về nhà ~30') |
| **Repo** | `cse485-ms-01` |
| **Chuỗi** | Bắt đầu MiniShop: dữ liệu sản phẩm dạng **mảng** (hướng cấu trúc) |
| **Mang sang Phiếu 02** | `data.php` chuẩn 8 SP — dùng lại số liệu, không đổi giá/SKU |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — `inventory_value_core = **41380000**` |

### Chuẩn đầu ra (CLO)

1. Giải thích được Backend chạy ở đâu; phân biệt biến / mảng indexed / mảng kết hợp.  
2. Tách `data.php` (chỉ dữ liệu) và `index.php` (hiển thị).  
3. In bảng HTML đủ **8 SKU** sau bài về nhà; cột Danh mục là **chữ**, không phải số.  
4. Tính đúng `Tong gia tri kho = 41380000`.  
5. Escape HTML khi echo tên/SKU.

---

## 1. Lý thuyết cần nắm trước khi gõ (giải thích kỹ)

### 1.1. Vì sao phiếu này bắt đầu bằng mảng?

MiniShop cần lưu nhiều sản phẩm. Nếu khai báo 8 biến rời `$ten1`, `$gia1`… sẽ rối.  
**Mảng** = một biến chứa nhiều giá trị — đây là nền của Backend (kết quả SQL sau này cũng giống “mảng các dòng”).

```
Trình duyệt ──request──► PHP (đọc mảng, tính toán) ──HTML──► Trình duyệt
```

Phiếu 01–02 = **lập trình hướng cấu trúc** (dữ liệu mảng + lệnh/vòng lặp).  
Phiếu 03 mới chuyển sang **OOP**. Đừng nhảy cóc.

### 1.2. Biến & kiểu — nhớ 30 giây

| Kiểu | Ví dụ MiniShop | Ghi chú |
|------|----------------|---------|
| `string` | `'KB-01'`, `'Ban phim'` | Nháy đơn/kép |
| `int` | `1890000`, `3` | Số nguyên (giá VND coi như int) |
| `array` | `$products` | Tập hợp |
| `null` | (chưa dùng) | “không có giá trị” |

```php
$sku = 'KB-01';           // string
$price = 1890000;         // int
$line = $price * 3;       // 5670000
```

So sánh: ưu tiên `===` (cùng giá trị **và** kiểu) khi học sâu hơn; phiếu này chủ yếu tính `*` và cộng dồn.

### 1.3. Hai loại mảng bạn phải phân biệt

**Indexed (chỉ số số):**

```php
$skus = ['KB-01', 'KB-02', 'MS-01'];
echo $skus[0]; // KB-01 — bắt đầu từ 0
```

**Associative (khóa chữ) — quan trọng hơn cho Backend:**

```php
$product = [
    'sku' => 'KB-01',
    'name' => 'Keychron K2',
    'price' => 1890000,
    'qty' => 3,
];
echo $product['price']; // 1890000
```

**Mảng nhiều chiều** = danh sách các “bản ghi”:

```
$products
├── [0] → sku, name, category_id, price, qty
├── [1] → …
└── [7] → …
```

### 1.4. `require` tách file — thói quen nghề

| File | Trách nhiệm |
|------|-------------|
| `data.php` | Chỉ khai báo `$categories`, `$products` |
| `index.php` | `require` data → tính → echo HTML |

```php
// index.php
require_once __DIR__ . '/data.php';
```

`__DIR__` = thư mục chứa file hiện tại — đường dẫn ổn định hơn `./data.php`.

### 1.5. In ra HTML an toàn

```php
echo htmlspecialchars($name, ENT_QUOTES, 'UTF-8');
```

Nếu không escape, tên chứa `<script>` có thể thành XSS. Phiếu 01 đã yêu cầu thói quen này.

### 1.6. Map `category_id` → tên danh mục (ý tưởng)

```php
$categoryMap = [];
foreach ($categories as $c) {
    $categoryMap[$c['id']] = $c['name'];
}
// $categoryMap[1] === 'Ban phim'

$tenDm = $categoryMap[$product['category_id']] ?? '—';
```

---

## 2. Bài trên lớp (~20 phút) — Catalog 4 SP

### Bước 1 — Tạo thư mục

```
htdocs/minishop-01/
├── data.php
└── index.php
```

Mở: `http://localhost/minishop-01/` (hoặc cổng XAMPP của bạn).

### Bước 2 — `data.php` (đúng số liệu, không đổi)

```php
<?php
$categories = [
    ['id' => 1, 'name' => 'Ban phim'],
    ['id' => 2, 'name' => 'Chuot'],
    ['id' => 3, 'name' => 'Man hinh'],
];

$products = [
    ['sku' => 'KB-01', 'name' => 'Keychron K2', 'category_id' => 1, 'price' => 1890000, 'qty' => 3],
    ['sku' => 'KB-02', 'name' => 'Akko 3087', 'category_id' => 1, 'price' => 1290000, 'qty' => 5],
    ['sku' => 'MS-01', 'name' => 'Logitech M331', 'category_id' => 2, 'price' => 290000, 'qty' => 10],
    ['sku' => 'MS-02', 'name' => 'Razer Viper', 'category_id' => 2, 'price' => 990000, 'qty' => 4],
];
```

### Bước 3 — `index.php` tối thiểu

- `require_once` data  
- Tiêu đề trang: **`MiniShop — Catalog (Buoi 1)`**  
- Bảng cột: `SKU | Ten | Gia | So luong`  
- `foreach ($products as $p)` in từng dòng  
- Escape `sku`, `name`

**Checkpoint GV:** thấy đúng 4 SKU `KB-01`, `KB-02`, `MS-01`, `MS-02`.

---

## 3. Bài về nhà (~30 phút) — Đủ 8 SP + tính toán + map DM

Tiếp tục **cùng repo** `cse485-ms-01`.

### Nhiệm vụ A — Đủ 8 sản phẩm

Thêm 4 dòng (hoặc dán block đầy đủ từ [CANONICAL-DATA §4](./CANONICAL-DATA.md)):

| sku | name | category_id | price | qty | line_total |
|-----|------|------------:|------:|----:|----------:|
| MS-03 | Xiaomi Silent | 2 | 250000 | 8 | 2000000 |
| MN-01 | Dell 24 inch | 3 | 3200000 | 2 | 6400000 |
| MN-02 | LG UltraFine | 3 | 8500000 | 1 | 8500000 |
| KB-03 | Leopold FC660M | 1 | 2750000 | 2 | 5500000 |

### Nhiệm vụ B — Thành tiền & tổng

```php
$thanhTien = $p['price'] * $p['qty'];
// cộng dồn vào $tong
```

In:

- Cột **Thanh tien**
- `Tong gia tri kho = 41380000` (đúng từng số, không format dấu phẩy trong EXPECT)
- `So san pham = 8`

### Nhiệm vụ C — Cột Danh muc (chữ)

Dùng `$categoryMap` như mục 1.5.  
Không chấp nhận chỉ in `1`, `2`, `3`.

### Nhiệm vụ D — Debug

Cuối trang:

```php
echo '<pre>';
var_dump($products);
echo '</pre>';
```

### Bảng kiểm tra tự chấm

| Kiểm tra | Giá trị |
|----------|---------|
| Số dòng | 8 |
| Tong gia tri kho | **41380000** |
| Danh muc | Ban phim / Chuot / Man hinh |
| Comment EXPECT | xem mục 4 |

```
KB-01: 5670000
KB-02: 6450000
MS-01: 2900000
MS-02: 3960000
MS-03: 2000000
MN-01: 6400000
MN-02: 8500000
KB-03: 5500000
TỔNG = 41380000
```

---

## 4. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử |
|-------------|-------------|---------|
| Trang trắng / hiện code PHP | Apache tắt / sai `htdocs` | Start Apache; đúng URL |
| Tổng sai (hay ra 41170000) | Nhầm số liệu cũ | Bám CANONICAL-DATA |
| `Undefined variable` | Quên require `data.php` | `require_once` đầu `index.php` |
| Chỉ thấy 4 SP sau về nhà | Chưa thêm 4 dòng | Kiểm tra `count($products)` |
| Tiếng Việt lỗi | Thiếu charset | `<meta charset="UTF-8">` |

---

## 5. OUTPUT máy chấm (EXPECT)

```html
<!-- MS_EXPECT product_count=8 inventory_value=41380000 -->
```

| Key | Value |
|-----|------:|
| `product_count` | 8 |
| `inventory_value` | 41380000 |
| `skus` | KB-01…MN-02 đủ 8 |
| `htmlspecialchars` | Có |

---

## 6. Rubric + nộp bài

| Tiêu chí | Bắt buộc |
|----------|----------|
| Tách data/index | Có |
| 8 SP + danh DM chữ | Có |
| Tổng 41380000 | Có |
| Escape + MS_EXPECT | Có |

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 01                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo cse485-ms-01 (data.php, index.php, README)   ║
║  2. Video OBS + camera mặt: bảng 8 dòng + tổng 41380000      ║
║  3. Thuyết trình: mảng nhiều chiều là gì? Vì sao tách file?  ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 02:** cùng 8 SP — đóng gói tính toán thành **hàm** trong `helpers.php`.
