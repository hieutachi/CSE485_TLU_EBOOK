# BUỔI 2: Cấu trúc điều khiển & Hàm (Functions)

> **Thời lượng gợi ý:** 3–4 giờ tự học  
> **Tiên quyết:** Buổi 1 (biến, mảng, toán tử)  
> **Kết quả đầu ra:** Viết được if/switch/vòng lặp, hàm tái sử dụng, tách file bằng require/include  
> **Phiếu học tập liên quan:** Phiếu 2

---

## 1. Mục tiêu buổi học

1. Dùng If/Else, Switch để ra quyết định theo điều kiện.
2. Duyệt dữ liệu bằng For, While, Foreach.
3. Khai báo hàm, hiểu phạm vi biến (local / global).
4. Tách mã nguồn bằng `require` / `include` (và bản `*_once`).

---

## 2. Vì sao cần cấu trúc điều khiển?

Backend luôn phải **quyết định**: điểm ≥ 5 thì Đạt; sai mật khẩu thì từ chối; hết hàng thì không cho đặt…

```
Input (dữ liệu)
      │
      ▼
┌─────────────┐
│  Điều kiện? │──── true ──► Nhánh A (xử lý / hiển thị)
└─────────────┘
      │ false
      ▼
   Nhánh B
```

---

## 3. If / Else / Elseif

```php
<?php

$diem = 7.5;

if ($diem >= 8.5) {
    echo "Xuat sac";
} elseif ($diem >= 7.0) {
    echo "Kha";
} elseif ($diem >= 5.0) {
    echo "Trung binh";
} else {
    echo "Chua dat";
}
```

### Toán tử ba ngôi (ternary) — viết ngắn

```php
<?php

$tuoi = 17;
$trangThai = ($tuoi >= 18) ? "Du tuoi" : "Chua du tuoi";
echo $trangThai;
```

### Null coalescing `??` (rất hay khi nhận form)

```php
<?php

// Nếu $_GET['q'] không tồn tại → dùng chuỗi rỗng
$tuKhoa = $_GET['q'] ?? '';
echo "Tim: " . htmlspecialchars($tuKhoa);
```

---

## 4. Switch / Case

Dùng khi so sánh **một biến** với nhiều giá trị rời rạc:

```php
<?php

$vaiTro = "editor";

switch ($vaiTro) {
    case "admin":
        echo "Toan quyen";
        break;
    case "editor":
        echo "Sua bai viet";
        break;
    case "viewer":
        echo "Chi xem";
        break;
    default:
        echo "Vai tro khong hop le";
}
```

> Nhớ `break` — thiếu `break` sẽ “rơi” xuống case tiếp theo (fall-through).

---

## 5. Vòng lặp

### 5.1. For — biết trước số lần lặp

```php
<?php

for ($i = 1; $i <= 5; $i++) {
    echo "Lan lap thu {$i}<br>";
}
```

```
for (khởi tạo; điều kiện; bước nhảy)
         │         │          │
         $i=1    $i<=5      $i++
```

### 5.2. While — lặp khi điều kiện còn đúng

```php
<?php

$n = 3;
while ($n > 0) {
    echo "Dem nguoc: {$n}<br>";
    $n--;
}
```

### 5.3. Do…While — chạy ít nhất 1 lần

```php
<?php

$x = 0;
do {
    echo "Chay it nhat 1 lan, x = {$x}<br>";
    $x++;
} while ($x < 0);
```

### 5.4. Foreach — vua của mảng (dùng nhiều nhất trong Backend)

```php
<?php

$sanPham = ["Ban phim", "Chuot", "Man hinh"];

// Chỉ lấy giá trị
foreach ($sanPham as $ten) {
    echo "<li>{$ten}</li>";
}

// Lấy cả khóa và giá trị
$sv = ["ma" => "SV01", "ten" => "An", "diem" => 8];
foreach ($sv as $khoa => $giaTri) {
    echo "{$khoa}: {$giaTri}<br>";
}
```

### 5.5. break & continue

```php
<?php

for ($i = 1; $i <= 10; $i++) {
    if ($i === 3) {
        continue; // bỏ lần này, sang i=4
    }
    if ($i === 8) {
        break;    // thoát vòng lặp
    }
    echo $i . " ";
}
// Output: 1 2 4 5 6 7
```

---

## 6. Hàm (Functions)

### 6.1. Khai báo và gọi hàm

```php
<?php

/**
 * Tinh diem tong ket
 */
function tinhDiemTongKet(float $giuaKy, float $cuoiKy): float
{
    return ($giuaKy * 0.4) + ($cuoiKy * 0.6);
}

$ketQua = tinhDiemTongKet(7.5, 8.0);
echo $ketQua; // 7.8
```

### 6.2. Tham số mặc định

```php
<?php

function chao(string $ten = "ban"): string
{
    return "Xin chao, {$ten}!";
}

echo chao();        // Xin chao, ban!
echo chao("Hieu");  // Xin chao, Hieu!
```

### 6.3. Phạm vi biến (Scope)

```
┌─ global ─────────────────────────────┐
│  $tyLe = 0.1;                        │
│                                      │
│   ┌─ function ─────────────┐         │
│   │  $tyLe local (khác)    │         │
│   │  muốn dùng global →    │         │
│   │  global $tyLe;  hoặc   │         │
│   │  $GLOBALS['tyLe']      │         │
│   └────────────────────────┘         │
└──────────────────────────────────────┘
```

```php
<?php

$thue = 0.1;

function tinhGiaSauThue(float $gia): float
{
    global $thue; // lấy biến toàn cục (cẩn thận khi lạm dụng)
    return $gia * (1 + $thue);
}

echo tinhGiaSauThue(100000); // 110000
```

> **Thói quen tốt:** Truyền dữ liệu qua **tham số**, hạn chế `global`. Code dễ test, dễ đọc hơn.

### 6.4. Return sớm (early return) — code sạch

```php
<?php

function xepLoai(?float $diem): string
{
    if ($diem === null) {
        return "Khong co diem";
    }
    if ($diem < 0 || $diem > 10) {
        return "Diem khong hop le";
    }
    if ($diem >= 5) {
        return "Dat";
    }
    return "Truot";
}
```

---

## 7. Require / Include — tách file dự án

### 7.1. So sánh nhanh

| Lệnh | Khi file không tồn tại |
|------|-------------------------|
| `include` | Cảnh báo (Warning), script tiếp tục |
| `require` | Lỗi nghiêm trọng (Fatal), dừng |
| `include_once` / `require_once` | Chỉ nạp **một lần** (tránh khai báo hàm trùng)

```
project/
├── config.php      ← hằng số, cấu hình
├── helpers.php     ← các hàm dùng chung
└── index.php       ← trang chính (require các file trên)
```

### 7.2. Ví dụ thực tế

**helpers.php**

```php
<?php

function formatVnd(int $soTien): string
{
    return number_format($soTien, 0, ',', '.') . ' đ';
}

function h(string $text): string
{
    // Escape HTML — thói quen chống XSS từ sớm
    return htmlspecialchars($text, ENT_QUOTES, 'UTF-8');
}
```

**index.php**

```php
<?php

require_once __DIR__ . '/helpers.php';

$tenSp = "Ban phim co";
$gia   = 450000;

echo "<h1>" . h($tenSp) . "</h1>";
echo "<p>Gia: " . formatVnd($gia) . "</p>";
```

```
index.php
   │
   │ require_once
   ▼
helpers.php ──► nạp hàm formatVnd(), h()
   │
   ▼
Thực thi tiếp phần còn lại của index.php
```

> `__DIR__` = thư mục chứa file hiện tại — đường dẫn ổn định hơn `./` tương đối.

---

## 8. Bài thực hành tổng hợp Buổi 2

Tạo `htdocs/cse485-buoi2/`:

**functions.php**

```php
<?php

function tinhThanhTien(int $gia, int $soLuong): int
{
    return $gia * $soLuong;
}

function inDongSanPham(array $sp): void
{
    $thanhTien = tinhThanhTien($sp['gia'], $sp['so_luong']);
    echo "<tr>";
    echo "<td>" . htmlspecialchars($sp['ten']) . "</td>";
    echo "<td>" . number_format($sp['gia']) . "</td>";
    echo "<td>" . $sp['so_luong'] . "</td>";
    echo "<td>" . number_format($thanhTien) . "</td>";
    echo "</tr>";
}
```

**index.php**

```php
<?php

require_once __DIR__ . '/functions.php';

$gioHang = [
    ['ten' => 'Chuot Logitech', 'gia' => 250000, 'so_luong' => 2],
    ['ten' => 'Ban phim Keychron', 'gia' => 1800000, 'so_luong' => 1],
    ['ten' => 'USB-C Hub', 'gia' => 390000, 'so_luong' => 3],
];

$tong = 0;
?>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Buoi 2 - Gio hang</title>
</head>
<body>
    <h1>Gio hang</h1>
    <table border="1" cellpadding="8">
        <tr>
            <th>Ten</th><th>Gia</th><th>SL</th><th>Thanh tien</th>
        </tr>
        <?php foreach ($gioHang as $sp): ?>
            <?php
                inDongSanPham($sp);
                $tong += tinhThanhTien($sp['gia'], $sp['so_luong']);
            ?>
        <?php endforeach; ?>
    </table>
    <p><strong>Tong: <?php echo number_format($tong); ?> đ</strong></p>
</body>
</html>
```

---

## 9. Lỗi thường gặp

| Lỗi | Nguyên nhân | Cách xử |
|-----|-------------|---------|
| `Cannot redeclare function` | Require file hàm 2 lần | Dùng `require_once` |
| Vòng lặp vô hạn | Quên tăng biến đếm | Kiểm tra điều kiện while/for |
| `Undefined variable` trong hàm | Dùng biến ngoài mà không truyền vào | Truyền tham số |
| Switch chạy nhiều case | Thiếu `break` | Thêm `break` |

---

## 10. Tóm tắt & Checklist

```
Điều khiển: if / switch
Lặp:        for / while / foreach
Tái sử dụng: function + return
Tách file:   require_once / include_once
```

- [ ] Viết được xếp loại điểm bằng if/elseif
- [ ] Duyệt mảng bằng foreach và in HTML
- [ ] Tách hàm ra file riêng, `require_once` thành công
- [ ] Hoàn thành bài giỏ hàng mục 8

**Cầu nối Buổi 3:** Gom dữ liệu + hành vi thành **Class/Object**, nhận dữ liệu từ trình duyệt (GET/POST) và lưu trạng thái bằng Session/Cookie.

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 2/12*
