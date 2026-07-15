# Phiếu học tập 02 — Cấu trúc điều khiển & Hàm

> **Gắn với:** Buổi 2  
> **Repo gợi ý:** `cse485-phieu-02-ham-mang`

---

## 1. Tóm tắt lý thuyết

- `if/elseif/else`, `switch` ra quyết định.
- `for` / `while` / `foreach` duyệt dữ liệu.
- Hàm: tham số, `return`, hạn chế `global`.
- `require_once` / `include_once` tách file; ưu tiên `require_once` cho file bắt buộc.

---

## 2. Bài tập thực hành

### Yêu cầu

Tạo project tối thiểu 2 file:

- `functions.php` — chứa các hàm
- `index.php` — `require_once` và hiển thị

**Hàm bắt buộc:**

```php
function calcLineTotal(int $price, int $qty): int
function rankByTotal(int $total): string
// < 1_000_000 → "Dong"; < 3_000_000 → "Bac"; còn lại → "Vang"
function renderProductList(array $products): void
// echo <ul><li>ten - thanh_tien</li>...</ul>
```

### Input

```php
$products = [
    ['ten' => 'A', 'gia' => 100000, 'so_luong' => 4],
    ['ten' => 'B', 'gia' => 250000, 'so_luong' => 5],
    ['ten' => 'C', 'gia' => 900000, 'so_luong' => 2],
];
```

### Output kỳ vọng

- Danh sách HTML:
  - A - 400000
  - B - 1250000
  - C - 1800000
- Tong = **3450000**
- Hang thanh vien (theo tong): **Vang**

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 02                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG — đẩy functions.php + index.php        ║
║  2. Video OBS có CAMERA MẶT — chạy localhost, chỉ output,    ║
║     giải thích hàm calcLineTotal / rankByTotal               ║
║  3. Nộp link Repo + link Google Drive cho Giảng viên         ║
╚══════════════════════════════════════════════════════════════╝
```
