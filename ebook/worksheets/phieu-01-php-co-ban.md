# Phiếu học tập 01 — PHP Cơ bản

> **Gắn với:** Buổi 1  
> **Repo gợi ý đặt tên:** `cse485-phieu-01-php-co-ban`

---

## 1. Tóm tắt lý thuyết

- PHP chạy trên Server; Client chỉ nhận HTML kết quả.
- Biến bắt đầu bằng `$`; kiểu động (`string`, `int`, `float`, `bool`, `array`, `null`).
- So sánh chặt: `===` / `!==`.
- Mảng indexed (`$a[0]`) và associative (`$a['ten']`).
- In ra: `echo`, debug: `var_dump` / `print_r`.

---

## 2. Bài tập thực hành

### Yêu cầu

Viết file `index.php` quản lý **giỏ hàng đơn giản bằng mảng**:

1. Khai báo mảng **đúng 5 sản phẩm**, mỗi phần tử gồm: `ten` (string), `gia` (int), `so_luong` (int).
2. Với mỗi sản phẩm, tính `thanh_tien = gia * so_luong`.
3. Tính `tong_gio_hang` = tổng mọi `thanh_tien`.
4. In ra trình duyệt dưới dạng bảng HTML (`<table>`).
5. Dùng `var_dump` ở cuối trang (trong `<pre>`) để debug mảng gốc.

### Input (dữ liệu cứng trong code — bắt buộc dùng đúng số liệu này để dễ chấm)

```php
$cart = [
    ['ten' => 'Ban phim', 'gia' => 500000, 'so_luong' => 1],
    ['ten' => 'Chuot', 'gia' => 200000, 'so_luong' => 2],
    ['ten' => 'USB 32GB', 'gia' => 150000, 'so_luong' => 3],
    ['ten' => 'Loa', 'gia' => 800000, 'so_luong' => 1],
    ['ten' => 'Tai nghe', 'gia' => 450000, 'so_luong' => 2],
];
```

### Output kỳ vọng

| Ten | Gia | SL | Thanh tien |
|-----|-----|----|------------|
| Ban phim | 500000 | 1 | 500000 |
| Chuot | 200000 | 2 | 400000 |
| USB 32GB | 150000 | 3 | 450000 |
| Loa | 800000 | 1 | 800000 |
| Tai nghe | 450000 | 2 | 900000 |

**Tong gio hang: 3050000**

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 01                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. MÃ NGUỒN                                                 ║
║     • Tạo 01 GitHub Repository RIÊNG cho phiếu này           ║
║     • Push toàn bộ source (ít nhất index.php + README ngắn)  ║
║     • Nộp LINK repo lên hệ thống chấm điểm tự động           ║
║                                                              ║
║  2. MINH CHỨNG VIDEO (bắt buộc)                              ║
║     • Quay bằng OBS Studio                                   ║
║     • BẬT CAMERA quay RÕ MẶT sinh viên (góc màn hình)        ║
║     • Thao tác: mở localhost → chạy code → chỉ bảng kết quả  ║
║     • Thuyết trình ngắn: giải thích input & output           ║
║     • Upload Google Drive, bật quyền xem cho GV              ║
║     • Nộp LINK Drive cùng với link GitHub                    ║
╚══════════════════════════════════════════════════════════════╝
```
