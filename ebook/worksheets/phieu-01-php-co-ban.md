# Phiếu 01 — PHP cơ bản & Catalog MiniShop

| | |
|---|---|
| **Buổi** | 1 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–30') |
| **Repo** | `cse485-ms-01` |
| **Chuỗi** | Bắt đầu MiniShop: dữ liệu sản phẩm dạng mảng |
| **Mang sang Phiếu 02** | File dữ liệu / cấu trúc mảng catalog đã chuẩn hóa |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — `inventory_value_core = 41380000` |

### Chuẩn đầu ra (CLO)

1. Tách được `data.php` và `index.php`.  
2. In bảng HTML đúng 8 SKU sau bài về nhà.  
3. Map `category_id` → tên danh mục chữ.  
4. Tính đúng `Tong gia tri kho = 41380000`.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- Backend chạy trên Server; Client nhận HTML.
- Biến `$`, kiểu `string|int|float|bool|array|null`.
- So sánh chặt `===`; nối chuỗi `.`
- Mảng indexed & associative; mảng nhiều chiều = danh sách “bản ghi”.
- Debug: `var_dump` / `print_r` trong `<pre>`.

---

## 2. Bài trên lớp (~20 phút) — Catalog tối thiểu

### Yêu cầu

Tạo `htdocs/minishop-01/` với:

1. `data.php` — khai báo hằng / mảng **đúng** dữ liệu dưới đây (không đổi giá trị).
2. `index.php` — `require` data, in bảng HTML 4 cột: `SKU | Ten | Gia | So luong`.

### Input bắt buộc (trên lớp)

```php
$categories = [
    ['id' => 1, 'name' => 'Ban phim'],
    ['id' => 2, 'name' => 'Chuot'],
    ['id' => 3, 'name' => 'Man hinh'],
];

$products = [
    ['sku' => 'KB-01', 'name' => 'Keychron K2', 'category_id' => 1, 'price' => 1890000, 'qty' => 3],
    ['sku' => 'KB-02', 'name' => 'Akko 3087',   'category_id' => 1, 'price' => 1290000, 'qty' => 5],
    ['sku' => 'MS-01', 'name' => 'Logitech M331','category_id' => 2, 'price' => 290000,  'qty' => 10],
    ['sku' => 'MS-02', 'name' => 'Razer Viper',  'category_id' => 2, 'price' => 990000,  'qty' => 4],
];
```

### Output kỳ vọng (trên lớp)

Bảng 4 dòng sản phẩm; trang có tiêu đề **MiniShop — Catalog (Buoi 1)**.

**Checkpoint giảng viên:** mở `index.php` thấy đủ 4 SKU.

---

## 3. Bài về nhà (~25–30 phút) — Mở rộng & tính toán

Tiếp tục **cùng repo**, không tạo project mới.

### Nhiệm vụ A — Đủ 8 sản phẩm

Thêm vào `$products` đúng 4 dòng sau (giữ nguyên 4 dòng trên lớp).  
**Hoặc** thay toàn bộ `data.php` bằng block PHP đầy đủ trong [`CANONICAL-DATA.md` §4](./CANONICAL-DATA.md) (khuyến nghị — tránh lệch số liệu).

| sku | name | category_id | price | qty | line_total |
|-----|------|-------------|------:|----:|----------:|
| MS-03 | Xiaomi Silent | 2 | 250000 | 8 | 2000000 |
| MN-01 | Dell 24 inch | 3 | 3200000 | 2 | 6400000 |
| MN-02 | LG UltraFine | 3 | 8500000 | 1 | 8500000 |
| KB-03 | Leopold FC660M | 1 | 2750000 | 2 | 5500000 |

### Nhiệm vụ B — Cột thành tiền & tổng

1. Thêm cột **Thanh tien** = `price * qty`.
2. Dưới bảng in: `Tong gia tri kho = …` (tổng mọi thành tiền).
3. In thêm dòng: `So san pham = 8`.

### Nhiệm vụ C — Map tên danh mục (không dùng vòng lặp phức tạp cũng được)

Thêm cột **Danh muc**: với mỗi product, in `name` của category khớp `category_id`  
(gợi ý: xây mảng phụ `$categoryMap[id] = name` bằng vòng `foreach`).

### Output kỳ vọng (về nhà)

| Kiểm tra | Giá trị |
|----------|---------|
| Số dòng trong bảng | 8 |
| Tong gia tri kho | **41380000** |
| Cột Danh muc | Không còn số `1/2/3` thô — hiện chữ Ban phim / Chuot / Man hinh |
| Cuối trang | Có `<pre>` + `var_dump($products)` (debug) |

Công thức kiểm tra nhanh:

```
KB-01: 1890000*3=5670000
KB-02: 1290000*5=6450000
MS-01: 290000*10=2900000
MS-02: 990000*4=3960000
MS-03: 250000*8=2000000
MN-01: 3200000*2=6400000
MN-02: 8500000*1=8500000
KB-03: 2750000*2=5500000
TỔNG = 41380000
```

---

## 4. OUTPUT máy chấm được (EXPECT)

Thêm cuối `index.php` (comment HTML — không ảnh hưởng giao diện):

```html
<!-- MS_EXPECT product_count=8 inventory_value=41380000 -->
```

| Key | Value bắt buộc |
|-----|----------------|
| `product_count` | 8 |
| `inventory_value` | 41380000 |
| `skus` | đủ 8 SKU trong CANONICAL-DATA |
| `htmlspecialchars` | Có khi echo name/sku |

## 5. Tiêu chí đạt (rubric nhanh)

| Tiêu chí | Bắt buộc |
|----------|----------|
| Tách `data.php` + `index.php` | Có |
| Đủ 8 SP + cột danh mục chữ | Có |
| Tổng đúng 41380000 | Có |
| Escape HTML khi echo tên (`htmlspecialchars`) | Có |
| Comment `MS_EXPECT` | Có |

---

## 6. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 01                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG (data.php, index.php, README ngắn)     ║
║  2. Video OBS + camera mặt: chỉ bảng 8 dòng + tổng 41380000  ║
║  3. Thuyết trình 30–60s: mảng nhiều chiều là gì?             ║
╚══════════════════════════════════════════════════════════════╝
```
