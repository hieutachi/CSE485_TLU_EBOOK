# BUỔI 1: Nhập môn Backend & PHP thuần cơ bản

> **Thời lượng gợi ý:** 3–4 giờ tự học  
> **Kết quả đầu ra:** Hiểu Client–Server, cài được môi trường, viết được script PHP với biến / kiểu dữ liệu / toán tử / mảng  
> **Phiếu học tập liên quan:** Phiếu 1

---

## 1. Mục tiêu buổi học

Sau Buổi 1, bạn có thể:

1. Giải thích Backend nằm ở đâu trong một ứng dụng Web.
2. Vẽ và giải thích mô hình Client–Server bằng sơ đồ.
3. Cài đặt XAMPP và chạy được file `.php` trên `localhost`.
4. Khai báo biến, sử dụng kiểu dữ liệu, toán tử và mảng cơ bản trong PHP.
5. In kết quả ra trình duyệt bằng `echo` / `print_r` / `var_dump`.

---

## 2. Backend là gì? Vì sao phải học?

Khi bạn mở trang web bán sách, trình duyệt chỉ nhận về **HTML + CSS + JavaScript**. Nhưng giá sách, số lượng tồn kho, đơn hàng của bạn… đều nằm ở **máy chủ (Server)**. Backend chính là phần code chạy trên máy chủ đó.

### So sánh nhanh Frontend vs Backend

| | Frontend | Backend |
|---|----------|---------|
| Chạy ở đâu? | Trình duyệt (Client) | Máy chủ (Server) |
| Ngôn ngữ điển hình | HTML, CSS, JS | PHP, Node.js, Python, Java… |
| Nhiệm vụ | Hiển thị, tương tác người dùng | Xử lý logic, CSDL, bảo mật, API |
| Người dùng thấy? | Có | Không trực tiếp |

> **Lưu ý sư phạm:** Học Backend không có nghĩa là bỏ Frontend. Một Backend tốt luôn nghĩ đến dữ liệu sẽ được **hiển thị** như thế nào. Từ Buổi 7 trở đi, bạn sẽ dùng Blade để nối hai thế giới này.

---

## 3. Mô hình Client – Server (ASCII bắt buộc nhớ)

```
┌─────────────────┐                      ┌──────────────────────────┐
│   CLIENT        │                      │   SERVER                 │
│   (Trình duyệt) │                      │   (Apache + PHP + MySQL) │
│                 │   1. HTTP Request    │                          │
│  Chrome/Firefox │ ───────────────────► │  Nhận URL / Form data    │
│  Edge/Safari    │                      │  Chạy file .php          │
│                 │   2. HTTP Response   │  Truy vấn CSDL (nếu cần) │
│  Hiển thị HTML  │ ◄─────────────────── │  Trả HTML / JSON         │
└─────────────────┘                      └──────────────────────────┘
         ▲                                          ▲
         │                                          │
    Người dùng                                  Code của bạn
    nhìn thấy ở đây                             sống ở đây
```

### Luồng một lần nhấn nút (ví dụ: Đăng nhập)

```
 Người dùng                Client                 Server
     │                       │                      │
     │  Gõ user/pass         │                      │
     │  Nhấn "Đăng nhập"     │                      │
     ├──────────────────────►│  POST /login.php     │
     │                       ├─────────────────────►│
     │                       │                      │ Kiểm tra DB
     │                       │                      │ Tạo Session
     │                       │  HTML "Xin chào"     │
     │                       │◄─────────────────────┤
     │  Thấy trang chào      │                      │
     │◄──────────────────────┤                      │
```

**Câu hỏi tự kiểm tra:** Nếu tắt Apache (web server), điều gì xảy ra khi bạn mở `http://localhost/demo.php`?  
→ Trình duyệt không kết nối được Server → Backend không chạy.

---

## 4. Cài đặt môi trường làm việc

### 4.1. Cài XAMPP

1. Tải XAMPP từ trang chính thức: https://www.apachefriends.org/
2. Cài đặt theo hướng dẫn mặc định.
3. Mở **XAMPP Control Panel** → Start **Apache** (và **MySQL** — sẽ dùng từ Buổi 4).

### 4.2. Thư mục làm việc

| Hệ điều hành | Thư mục gốc của Apache |
|--------------|------------------------|
| Windows | `C:\xampp\htdocs\` |
| macOS | `/Applications/XAMPP/htdocs/` |
| Linux | `/opt/lampp/htdocs/` |

Tạo thư mục dự án Buổi 1:

```
htdocs/
└── cse485-buoi1/
    └── index.php
```

### 4.3. Editor: VS Code hoặc Cursor

Cài extension hữu ích:

- **PHP Intelephense** — gợi ý code, báo lỗi cú pháp
- **PHP Debug** (tùy chọn, dùng sau)

### 4.4. Kiểm tra PHP đã chạy

Tạo file `index.php`:

```php
<?php
// Buổi 1 - Kiểm tra môi trường
echo "Xin chào Backend! PHP đang chạy.";
phpinfo(); // Hiển thị thông tin phiên bản PHP (chỉ dùng khi học, đừng để trên production)
```

Mở trình duyệt:

```
http://localhost/cse485-buoi1/
```

Nếu thấy dòng chữ chào và bảng thông tin PHP → **môi trường OK**.

> **Mẹo:** Trên macOS, nếu cổng 80 bị chiếm, XAMPP có thể chạy Apache ở cổng `8080`:  
> `http://localhost:8080/cse485-buoi1/`

### 4.5. Chạy PHP qua Terminal (bổ trợ)

```bash
php -v          # Xem phiên bản PHP
php index.php   # Chạy file PHP trực tiếp (CLI, không qua Apache)
```

---

## 5. PHP là gì? File PHP trông như thế nào?

**PHP** (PHP: Hypertext Preprocessor) là ngôn ngữ kịch bản phía Server. Code PHP được Server thực thi, kết quả (thường là HTML) mới gửi về Client.

### Cấu trúc tối thiểu

```php
<?php
// Mọi lệnh PHP nằm giữa thẻ mở và (tùy chọn) thẻ đóng

echo "Hello World";

// Thẻ đóng ?> có thể bỏ nếu file chỉ chứa PHP thuần (khuyến nghị PSR)
```

### PHP xen HTML (rất phổ biến ở giai đoạn đầu)

```php
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Buổi 1 - PHP cơ bản</title>
</head>
<body>
    <h1>Trang chào mừng</h1>
    <?php
        $ten = "An";
        echo "<p>Xin chào, " . $ten . "!</p>";
    ?>
</body>
</html>
```

```
┌──────────────┐     PHP xử lý      ┌──────────────────┐
│  index.php   │ ───────────────►  │  HTML thuần      │
│  (có <?php>) │   trên Server     │  gửi về Client   │
└──────────────┘                   └──────────────────┘
```

---

## 6. Biến trong PHP

### 6.1. Quy tắc đặt tên biến

- Bắt đầu bằng dấu `$`
- Tên bắt đầu bằng chữ cái hoặc `_`
- Phân biệt hoa thường: `$ten` khác `$Ten`
- Không dùng khoảng trắng, không bắt đầu bằng số

```php
<?php

// ✅ Hợp lệ
$hoTen = "Nguyen Van A";
$_tuoi = 20;
$diem_toan = 8.5;

// ❌ Không hợp lệ
// $1ten = "A";      // bắt đầu bằng số
// $ho ten = "A";    // có khoảng trắng
```

### 6.2. Gán và in giá trị

```php
<?php

$monHoc = "CSE485 - Cong nghe Web";
$soTinChi = 3;

echo $monHoc;           // In chuỗi
echo "<br>";            // Xuống dòng HTML
echo "Tin chi: " . $soTinChi;  // Nối chuỗi bằng dấu chấm (.)

// Cách khác: nội suy trong chuỗi kép
echo "<br>Mon: {$monHoc}, Tin chi: {$soTinChi}";
```

### 6.3. Biến động kiểu (Dynamic typing)

PHP **không bắt buộc** khai báo kiểu khi tạo biến. Kiểu được suy ra từ giá trị:

```php
<?php

$x = 10;        // int
$x = "muoi";    // giờ thành string — hợp lệ nhưng nên tránh đổi kiểu lung tung
```

> Từ PHP 7+, bạn **có thể** khai báo kiểu chặt chẽ (type hint) — sẽ gặp lại ở OOP (Buổi 3) và Laravel.

---

## 7. Kiểu dữ liệu cơ bản

### 7.1. Bảng tổng hợp

| Kiểu | Ví dụ | Mô tả |
|------|-------|--------|
| `string` | `"Xin chào"` | Chuỗi ký tự |
| `int` | `20`, `-5` | Số nguyên |
| `float` | `3.14`, `9.0` | Số thực |
| `bool` | `true`, `false` | Logic đúng/sai |
| `array` | `[1, 2, 3]` | Tập hợp giá trị |
| `null` | `null` | Không có giá trị |
| `object` | `new SinhVien()` | Đối tượng (Buổi 3) |

### 7.2. Kiểm tra kiểu với `var_dump` và `gettype`

```php
<?php

$ten = "Lan";
$tuoi = 19;
$diemTB = 7.5;
$daTotNghiep = false;
$ghiChu = null;

echo gettype($ten);     // string
echo "<br>";
var_dump($tuoi);        // int(19)
echo "<br>";
var_dump($diemTB);      // float(7.5)
echo "<br>";
var_dump($daTotNghiep); // bool(false)
echo "<br>";
var_dump($ghiChu);      // NULL
```

**Khi nào dùng gì?**

| Hàm | Dùng khi |
|-----|----------|
| `echo` | In chuỗi/số ra HTML |
| `print_r()` | Xem cấu trúc mảng (dễ đọc) |
| `var_dump()` | Debug: xem **kiểu + giá trị** |

---

## 8. Toán tử trong PHP

### 8.1. Toán tử số học

```php
<?php

$a = 10;
$b = 3;

echo $a + $b;  // 13  Cộng
echo $a - $b;  // 7   Trừ
echo $a * $b;  // 30  Nhân
echo $a / $b;  // 3.333... Chia
echo $a % $b;  // 1   Chia lấy dư
echo $a ** $b; // 1000 Lũy thừa (10^3)
```

### 8.2. Toán tử gán

```php
<?php

$x = 5;
$x += 3;  // $x = $x + 3  → 8
$x -= 2;  // 6
$x *= 2;  // 12
$x /= 3;  // 4
$x %= 3;  // 1
```

### 8.3. Toán tử so sánh

```php
<?php

$a = 5;
$b = "5";

var_dump($a == $b);   // true  — so sánh giá trị (lỏng)
var_dump($a === $b);  // false — so sánh giá trị VÀ kiểu (chặt)
var_dump($a != $b);   // false
var_dump($a !== $b);  // true
var_dump($a > 3);     // true
var_dump($a <= 5);    // true
```

> **Quy tắc vàng:** Khi so sánh, ưu tiên `===` và `!==` để tránh bug "ép kiểu âm thầm".

### 8.4. Toán tử logic

```php
<?php

$diem = 8;
$chuyenCan = true;

// && : cả hai đúng
var_dump($diem >= 5 && $chuyenCan); // true

// || : một trong hai đúng
var_dump($diem < 5 || $chuyenCan);  // true

// !  : phủ định
var_dump(!$chuyenCan);              // false
```

### 8.5. Nối chuỗi

```php
<?php

$ho = "Nguyen";
$ten = "An";

$hoTen = $ho . " " . $ten;  // "Nguyen An"
$hoTen .= " - CNTT";        // "Nguyen An - CNTT"
echo $hoTen;
```

---

## 9. Mảng (Array) cơ bản — nền tảng của Backend

Gần như mọi dữ liệu Backend đều đi qua mảng: danh sách sản phẩm, kết quả truy vấn DB, dữ liệu form…

### 9.1. Mảng tuần tự (Indexed Array)

```php
<?php

// Cách 1: cú pháp ngắn (khuyến nghị)
$monHoc = ["HTML", "CSS", "PHP", "Laravel"];

// Cách 2: hàm array()
$diem = array(8, 7.5, 9, 8.5);

echo $monHoc[0];  // HTML  — chỉ số bắt đầu từ 0
echo $monHoc[2];  // PHP

// Đếm phần tử
echo count($monHoc); // 4

// Thêm phần tử
$monHoc[] = "MySQL";           // thêm vào cuối
$monHoc[10] = "Docker";        // gán vào chỉ số 10 (cẩn thận: mảng có "lỗ trống")

echo "<pre>";
print_r($monHoc);
echo "</pre>";
```

### 9.2. Mảng kết hợp (Associative Array) — cực kỳ quan trọng

```php
<?php

$sinhVien = [
    "ma_sv"   => "SV001",
    "ho_ten"  => "Tran Thi B",
    "email"   => "b@student.edu.vn",
    "diem_tb" => 8.2,
];

echo $sinhVien["ho_ten"];   // Tran Thi B
echo $sinhVien["diem_tb"];  // 8.2

// Duyệt nhanh (xem trước Buổi 2)
foreach ($sinhVien as $khoa => $giaTri) {
    echo $khoa . ": " . $giaTri . "<br>";
}
```

### 9.3. Mảng nhiều chiều (Multidimensional) — preview thực tế

```php
<?php

$danhSachSV = [
    ["ma" => "SV001", "ten" => "An",  "diem" => 8],
    ["ma" => "SV002", "ten" => "Binh","diem" => 7],
    ["ma" => "SV003", "ten" => "Chi", "diem" => 9],
];

// Lấy tên sinh viên thứ 2 (chỉ số 1)
echo $danhSachSV[1]["ten"]; // Binh

echo "<pre>";
print_r($danhSachSV);
echo "</pre>";
```

```
$danhSachSV
│
├── [0] ──► ma: SV001 | ten: An  | diem: 8
├── [1] ──► ma: SV002 | ten: Binh| diem: 7
└── [2] ──► ma: SV003 | ten: Chi | diem: 9
```

Đây chính là hình dạng dữ liệu bạn sẽ gặp khi `SELECT` từ MySQL (Buổi 4) và Eloquent (Buổi 9).

### 9.4. Một số hàm mảng hay dùng ngay

```php
<?php

$so = [3, 1, 4, 1, 5];

sort($so);              // Sắp xếp tăng dần (thay đổi mảng gốc)
print_r($so);           // [1, 1, 3, 4, 5]

$a = [1, 2];
$b = [3, 4];
$gop = array_merge($a, $b); // [1, 2, 3, 4]

$ten = ["An", "Binh", "Chi"];
var_dump(in_array("Binh", $ten)); // true — kiểm tra phần tử có tồn tại không
```

---

## 10. Bài thực hành tổng hợp Buổi 1

Tạo file `htdocs/cse485-buoi1/thuchanh.php` và gõ lại toàn bộ:

```php
<?php
/**
 * Buổi 1 - Bài thực hành tổng hợp
 * Mục tiêu: biến, kiểu dữ liệu, toán tử, mảng
 */

// --- 1) Thông tin sinh viên (biến + kiểu) ---
$maSv      = "SV2025001";          // string
$hoTen     = "Le Minh Duc";        // string
$tuoi      = 20;                   // int
$diemGiuaKy = 7.5;                 // float
$diemCuoiKy = 8.0;                 // float
$datMon    = true;                 // bool

// --- 2) Tính điểm tổng kết (toán tử) ---
// Công thức giả định: 40% giữa kỳ + 60% cuối kỳ
$diemTongKet = ($diemGiuaKy * 0.4) + ($diemCuoiKy * 0.6);

// --- 3) Danh sách môn đã học (mảng) ---
$dsMonHoc = [
    "CNW"  => "Cong nghe Web",
    "CSDL" => "Co so du lieu",
    "CTDL" => "Cau truc du lieu",
];

?>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Thuc hanh Buoi 1</title>
</head>
<body>
    <h1>Phieu diem sinh vien</h1>

    <p>Ma SV: <?php echo $maSv; ?></p>
    <p>Ho ten: <?php echo $hoTen; ?></p>
    <p>Tuoi: <?php echo $tuoi; ?></p>
    <p>Diem tong ket: <?php echo $diemTongKet; ?></p>
    <p>Dat mon? <?php echo $datMon ? "Co" : "Chua"; ?></p>

    <h2>Mon da hoc</h2>
    <ul>
        <?php foreach ($dsMonHoc as $maMon => $tenMon): ?>
            <li><?php echo $maMon . " - " . $tenMon; ?></li>
        <?php endforeach; ?>
    </ul>

    <h2>Debug</h2>
    <pre><?php
        var_dump($maSv, $tuoi, $diemTongKet, $dsMonHoc);
    ?></pre>
</body>
</html>
```

### Output kỳ vọng (trên trình duyệt)

```
Phieu diem sinh vien
Ma SV: SV2025001
Ho ten: Le Minh Duc
Tuoi: 20
Diem tong ket: 7.8
Dat mon? Co

Mon da hoc
• CNW - Cong nghe Web
• CSDL - Co so du lieu
• CTDL - Cau truc du lieu

Debug
... (var_dump chi tiết kiểu + giá trị)
```

---

## 11. Lỗi thường gặp & cách xử lý

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
|-------------|------------------------|------------|
| Không mở được `localhost` | Apache chưa Start | Mở XAMPP → Start Apache |
| Trang trắng / tải mã nguồn PHP ra | File đặt sai thư mục hoặc Apache tắt | Kiểm tra `htdocs`, Start Apache |
| `Parse error` | Thiếu `;` hoặc nháy không khớp | Đọc dòng số lỗi PHP báo |
| `Undefined variable` | Dùng biến chưa gán | Gán giá trị trước khi `echo` |
| Tiếng Việt bị lỗi font | Thiếu `charset=UTF-8` | Thêm thẻ meta UTF-8 như bài mẫu |

Bật hiển thị lỗi khi học (đầu file):

```php
<?php
ini_set('display_errors', '1');
error_reporting(E_ALL);
```

---

## 12. Tóm tắt kiến thức Buổi 1

```
Backend = code chạy trên Server
         │
         ├── Client gửi Request (HTTP)
         ├── PHP xử lý (biến, toán tử, mảng…)
         └── Server trả Response (HTML/JSON)

PHP cơ bản hôm nay:
  $bien        → lưu dữ liệu
  kiểu dữ liệu → string, int, float, bool, array, null
  toán tử      → số học, so sánh (===), logic, nối chuỗi
  mảng         → indexed + associative (+ đa chiều)
```

### Checklist trước khi sang Buổi 2

- [ ] XAMPP chạy được, mở được `http://localhost/...`
- [ ] Viết được file PHP có `echo`, biến, phép tính
- [ ] Phân biệt được `==` và `===`
- [ ] Tạo và in được mảng kết hợp bằng `print_r` / `var_dump`
- [ ] Hoàn thành bài thực hành tổng hợp ở mục 10

---

## 13. Bài tập về nhà (chuẩn bị Phiếu 1)

1. Tạo mảng chứa **5 sản phẩm** (mỗi sản phẩm có `ten`, `gia`, `so_luong`).
2. Tính **tổng tiền** = `gia * so_luong` của từng sản phẩm, rồi tính tổng cả giỏ hàng.
3. In kết quả ra HTML dưới dạng danh sách `<ul>` hoặc bảng `<table>` đơn giản.

> Chi tiết Input/Output và yêu cầu nộp bài (GitHub + video OBS) nằm ở **Phiếu học tập số 1** (theo bản phiếu giảng viên phát hành).

---

## 14. Cầu nối sang Buổi 2

Bạn đã biết **lưu dữ liệu** (biến, mảng). Buổi 2 sẽ dạy cách **ra quyết định và lặp lại**:

- If / Else / Switch → máy tính biết "nếu điểm ≥ 5 thì Đạt"
- For / While / Foreach → in cả danh sách mà không copy-paste 50 lần
- Function → đóng gói logic thành khối tái sử dụng
- `require` / `include` → tách file cho dự án không còn là một file khổng lồ

Khi bạn gõ **"Tiếp tục"**, chúng ta sẽ sang **Buổi 2: Cấu trúc điều khiển & Hàm**.

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 1/12*
