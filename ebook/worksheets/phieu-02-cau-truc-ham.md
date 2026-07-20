# Phiếu 02 — Cấu trúc điều khiển, Hàm & báo cáo kho

| | |
|---|---|
| **Buổi** | 2 — đọc kèm [Buổi 02](../02-buoi-02-cau-truc-ham.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-02` |
| **Nhận từ Phiếu 01** | Dataset 8 SP ([CANONICAL-DATA](./CANONICAL-DATA.md)) — đã xong phần về nhà P01 |
| **Mang sang Phiếu 03** | Logic `lineTotal` / `stockLevel` sẽ thành **method** trong class |
| **Tổng kho CORE** | **41380000** |

### Chuẩn đầu ra (CLO)

1. Viết đủ 5 hàm bắt buộc trong `helpers.php` (đúng trách nhiệm từng hàm).  
2. Giải thích được vì sao tách hàm (tái sử dụng, tránh copy-paste).  
3. `inventoryValue` = **41380000**; báo cáo 3 DM đúng count/value.  
4. Filter `?category_id=2` → đúng 3 SKU chuột.  
5. `stockLevel` / `rankInventory` trả **chuỗi exact** theo bảng chuẩn.

---

## 1. Lý thuyết — vì sao cần hàm & cấu trúc điều khiển?

### 1.1. Vị trí trong lộ trình

```
P01: dữ liệu (mảng)     →  P02: quyết định + lặp + hàm  →  P03: đóng vào class
     "có gì trong kho"       "tính / lọc / xếp hạng thế nào"
```

Đây vẫn là **lập trình hướng cấu trúc**: dữ liệu ở `data.php`, xử lý ở `helpers.php`.

### 1.2. Hàm là gì? (giải thích kỹ)

**Hàm** = khối lệnh có tên, nhận **input (tham số)**, trả **output (`return`)** — hoặc chỉ làm việc phụ (in HTML).

```
Input $product ──► [ function lineTotal ] ──► Output int (thành tiền)
```

| Không dùng hàm | Dùng hàm |
|----------------|----------|
| Copy `price*qty` 10 chỗ | Sửa 1 chỗ trong `lineTotal` |
| Dễ tính sai mỗi chỗ một kiểu | Một quy tắc nghiệp vụ thống nhất |
| Khó test | Test từng hàm |

**Quy tắc vàng P02:** view (`index.php`) **không** tự viết lại công thức `price*qty` / if tồn kho — phải gọi hàm.

### 1.3. Cấu trúc điều khiển dùng trong phiếu

| Cấu trúc | Dùng để |
|----------|---------|
| `if / elseif / else` | `stockLevel`, `rankInventory` |
| `foreach` | Duyệt SP, xây báo cáo |
| `$_GET[...] ??` | Lọc danh mục (có thể không có query) |

### 1.4. Năm hàm bắt buộc — hợp đồng chữ ký

```text
lineTotal(array $product): int
inventoryValue(array $products): int
findProductBySku(array $products, string $sku): ?array
countByCategory(array $products, int $categoryId): int
stockLevel(array $product): string
  // qty >= 5 → "Du"; qty >= 2 → "Sap het"; else → "Can nhap"
```

**Gợi ý cài đặt (không copy nguyên làm bài nộp nếu GV cấm — đây là hướng dẫn thuật toán):**

- `lineTotal`: `return $product['price'] * $product['qty'];`  
- `inventoryValue`: `$sum = 0; foreach ... $sum += lineTotal($p); return $sum;`  
- `findProductBySku`: foreach, nếu `$p['sku'] === $sku` thì `return $p`; hết vòng `return null;`  
- `countByCategory`: đếm khi `$p['category_id'] === $categoryId`  
- `stockLevel`: đúng thứ tự if `>= 5` trước, rồi `>= 2`

### 1.5. Hàm mở rộng về nhà

```text
filterByCategory(array $products, ?int $categoryId): array
rankInventory(int $totalValue): string
  // < 15_000_000 → Nho; < 35_000_000 → Trung binh; else → Lon
renderProductRows(array $products, array $categoryMap): void
```

Với tổng **41380000** → `rankInventory` = **`Lon`**.

### 1.6. Báo cáo theo danh mục — đáp án khóa

| Danh muc | So SP | Tong gia tri |
|----------|------:|-------------:|
| Ban phim | 3 | 17620000 |
| Chuot | 3 | 8860000 |
| Man hinh | 2 | 14900000 |

`17620000 + 8860000 + 14900000 = 41380000`.

---

## 2. Bài trên lớp (~20') — Xây `helpers.php`

### Cấu trúc

```
minishop-02/
├── data.php       ← copy 8 SP từ P01 / CANONICAL
├── helpers.php    ← CHỈ hàm, không HTML dài
└── index.php
```

### Việc trên lớp

1. Implement 5 hàm bắt buộc.  
2. Bảng sản phẩm có cột **Muc ton** = `stockLevel($p)`.  
3. In `inventoryValue(...)` — phải **41380000**.  
4. Checkpoint: `findProductBySku($products, 'MN-02')['name'] === 'LG UltraFine'`.

**Cách kiểm tra nhanh trên lớp:**

```php
$p = findProductBySku($products, 'MN-02');
var_dump($p['name'] ?? 'NOT FOUND');
```

---

## 3. Bài về nhà (~30') — Lọc + báo cáo + rank + render

### A — Filter GET

- Không có `category_id` → 8 dòng.  
- `?category_id=2` → đúng **MS-01, MS-02, MS-03**.  
- Link HTML: `Tat ca` | `Ban phim` | `Chuot` | `Man hinh`.

### B — Bảng báo cáo 3 DM

Tự tính bằng vòng lặp + hàm đã có. Khớp bảng đáp án mục 1.6.

### C — `Quy mo kho: Lon`

### D — `renderProductRows`

Chỉ `echo` các `<tr>…</tr>`. `index.php` không rải HTML ô từng biến lung tung ngoài hàm này (trừ `<thead>`).

### E — Comment EXPECT

```html
<!-- MS_EXPECT inventory_value=41380000 rank=Lon -->
```

---

## 4. OUTPUT chuẩn — `stockLevel` từng SKU

| sku | qty | Muc ton |
|-----|----:|---------|
| KB-01 | 3 | Sap het |
| KB-02 | 5 | Du |
| KB-03 | 2 | Sap het |
| MS-01 | 10 | Du |
| MS-02 | 4 | Sap het |
| MS-03 | 8 | Du |
| MN-01 | 2 | Sap het |
| MN-02 | 1 | Can nhap |

---

## 5. Lỗi thường gặp

| Lỗi | Nguyên nhân | Cách xử |
|-----|-------------|---------|
| `Cannot redeclare function` | Require helpers 2 lần | `require_once` |
| `stockLevel` sai MN-02 | Đảo thứ tự if (>=2 trước >=5) | Viết >=5 trước |
| Báo cáo Ban phim sai | Cộng nhầm KB | Kiểm tra category_id = 1 |
| Filter không chạy | Đọc sai `$_GET` | `(int) ($_GET['category_id'] ?? 0)` + phân nhánh null |

---

## 6. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 02                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo cse485-ms-02: helpers.php đủ hàm + index + data     ║
║  2. Video: filter cat=2 (3 dòng) + báo cáo 3 DM + Lon        ║
║  3. Thuyết trình: vì sao tách helpers.php? Hàm khác gì lệnh  ║
║     viết thẳng trong index?                                  ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 03:** cùng quy tắc `lineTotal`/`stockLevel` — chuyển thành **method** trong `class Product` (OOP).
