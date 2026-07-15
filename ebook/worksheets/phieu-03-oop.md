# Phiếu học tập 03 — OOP (Class Product)

> **Gắn với:** Buổi 3 (phần OOP)  
> **Repo gợi ý:** `cse485-phieu-03-oop-product`

---

## 1. Tóm tắt lý thuyết

- Class = khuôn; Object = thể hiện (`new`).
- Thuộc tính + phương thức; `__construct` khởi tạo.
- `$this` trỏ tới object hiện tại.
- `public` / `private` kiểm soát truy cập (encapsulation).

---

## 2. Bài tập thực hành

### Yêu cầu

File `Product.php` + `index.php`:

```php
class Product
{
    public function __construct(
        public string $name,
        public int $price,
        public int $quantity = 1
    ) {}

    public function lineTotal(): int { /* gia * quantity */ }
    public function label(): string { /* "Name - price đ" */ }
}
```

Trong `index.php`: tạo mảng **ít nhất 3** object `Product`, duyệt `foreach`, in `<ol>` gồm `label()` và `lineTotal()`.

### Input (object)

1. `new Product('Ban phim', 500000, 1)`
2. `new Product('Chuot', 200000, 2)`
3. `new Product('Monitor', 3500000, 1)`

### Output kỳ vọng

```
1. Ban phim - 500000 đ | Thanh tien: 500000
2. Chuot - 200000 đ | Thanh tien: 400000
3. Monitor - 3500000 đ | Thanh tien: 3500000
Tong: 4400000
```

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 03                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG (Product.php, index.php)               ║
║  2. Video OBS + camera mặt: mở code class, chạy output       ║
║  3. Thuyết trình: Class vs Object khác nhau thế nào?         ║
║  4. Nộp link Repo + Drive                                    ║
╚══════════════════════════════════════════════════════════════╝
```
