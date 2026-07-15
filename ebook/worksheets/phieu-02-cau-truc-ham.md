# Phiếu 02 — Cấu trúc điều khiển, Hàm & báo cáo kho

| | |
|---|---|
| **Buổi** | 2 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-02` |
| **Nhận từ Phiếu 01** | Dataset 8 SP ([CANONICAL-DATA](./CANONICAL-DATA.md)) — đã làm xong phần về nhà P01 |
| **Mang sang Phiếu 03** | Các hàm tính toán / xếp hạng sẽ “đóng” vào method của class |

### Chuẩn đầu ra (CLO)

1. Đủ 5 hàm bắt buộc trong `helpers.php` (signature đúng).  
2. `inventoryValue` = **41380000**.  
3. Báo cáo 3 danh mục đúng count/value.  
4. Filter `category_id=2` trả đúng 3 SKU chuột.  
5. `stockLevel` / `rankInventory` đúng chuỗi exact.

---

## 1. Tóm tắt lý thuyết

- `if/elseif`, `switch`, `foreach` (ưu tiên), `break/continue`.
- Hàm: tham số, `return`, type hint (khuyến nghị).
- Phạm vi biến: truyền tham số thay vì `global`.
- `require_once` tách file bắt buộc.

---

## 2. Bài trên lớp (~20') — Xây thư viện hàm

### Cấu trúc thư mục

```
minishop-02/
├── data.php          ← copy dữ liệu 8 SP từ Phiếu 01 (đúng số liệu)
├── helpers.php       ← chỉ chứa HÀM, không echo HTML dài
└── index.php         ← require + gọi hàm + in kết quả
```

### Hàm bắt buộc viết trong `helpers.php`

```text
lineTotal(array $product): int
inventoryValue(array $products): int
findProductBySku(array $products, string $sku): ?array
countByCategory(array $products, int $categoryId): int
stockLevel(array $product): string
  // qty >= 5 → "Du"; qty >= 2 → "Sap het"; else → "Can nhap"
```

### Việc trên lớp

1. Implement 5 hàm trên.
2. Trong `index.php`: in bảng có thêm cột `Muc ton` = `stockLevel(...)`.
3. Gọi `inventoryValue` in tổng (phải ra **41380000** nếu data đúng Phiếu 01).

**Checkpoint:** `findProductBySku($products, 'MN-02')` trả về đúng LG UltraFine.

---

## 3. Bài về nhà (~25–30') — Bộ lọc & báo cáo

### Nhiệm vụ A — Bộ lọc GET giả lập (chưa cần form đẹp)

Trong `index.php` đọc `$_GET['category_id']` (nếu có):

- Không có query → hiện tất cả 8 SP.
- Có `?category_id=2` → chỉ sản phẩm Chuot (đúng **3** dòng: MS-01, MS-02, MS-03).

Gợi ý: dùng `foreach` + `if`; chưa bắt buộc hàm riêng nhưng khuyến nghị:

```text
filterByCategory(array $products, ?int $categoryId): array
```

### Nhiệm vụ B — Báo cáo theo danh mục

In một bảng thứ hai **Bao cao theo danh muc**:

| Danh muc | So SP | Tong gia tri |
|----------|-------|--------------|
| Ban phim | 3 | … |
| Chuot | 3 | … |
| Man hinh | 2 | … |

Tự tính bằng vòng lặp + các hàm đã có.  
**Đáp án kiểm tra:**

| Danh muc | So SP | Tong gia tri |
|----------|-------|--------------|
| Ban phim | 3 | 17620000 |
| Chuot | 3 | 8860000 |
| Man hinh | 2 | 14900000 |

### Nhiệm vụ C — Xếp hạng giá trị kho

Viết hàm:

```text
rankInventory(int $totalValue): string
  // < 15_000_000 → "Nho"
  // < 35_000_000 → "Trung binh"
  // else → "Lon"
```

In: `Quy mo kho: Lon` (vì 41380000).

### Nhiệm vụ D — Danh sách HTML tái sử dụng

Viết `renderProductRows(array $products, array $categoryMap): void` chỉ `echo` các `<tr>…</tr>`.  
`index.php` không inject HTML từng ô rải rác ngoài hàm này (trừ header bảng).

---

## 4. OUTPUT chuẩn (EXPECT)

### 4.1. `stockLevel` từng SKU (chấm exact)

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

### 4.2. Trang phải có

1. Bảng sản phẩm (lọc GET) + cột mức tồn.  
2. Bảng báo cáo 3 danh mục (số liệu mục 3B).  
3. `Quy mo kho: Lon`.  
4. Link: `Tat ca` | `Ban phim(?category_id=1)` | `Chuot(?category_id=2)` | `Man hinh(?category_id=3)`.  
5. Comment: `<!-- MS_EXPECT inventory_value=41380000 rank=Lon -->`

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 02                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo có helpers.php với ĐỦ 5 hàm bắt buộc + hàm mở rộng  ║
║  2. Video: lọc category_id=2 (3 dòng) + bảng báo cáo 3 dòng  ║
║  3. Thuyết trình: vì sao tách helpers.php?                   ║
╚══════════════════════════════════════════════════════════════╝
```
