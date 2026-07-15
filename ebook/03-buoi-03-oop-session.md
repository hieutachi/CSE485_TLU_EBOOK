# BUỔI 3: Lập trình Hướng đối tượng (OOP) & Quản lý trạng thái

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 1–2  
> **Kết quả đầu ra:** Viết được Class/Object cơ bản; nhận GET/POST; dùng Session/Cookie  
> **Phiếu học tập liên quan:** [Phiếu 03](./worksheets/phieu-03-oop-session.md) — chuỗi MiniShop

---

## 1. Mục tiêu buổi học

1. Hiểu Class vs Object gắn với thực tế (`SinhVien`, `SanPham`).
2. Dùng thuộc tính, phương thức, constructor.
3. Nhận dữ liệu từ trình duyệt qua GET/POST (và validate cơ bản).
4. Lưu trạng thái đăng nhập ảo bằng Session; hiểu Cookie.

---

## 2. Vì sao cần OOP?

Ở Buổi 1–2, dữ liệu nằm trong mảng, xử lý nằm trong hàm rời. Khi dự án lớn:

```
Mảng $sp + hàm tinhTien() + hàm inSp() + hàm giamGia()
        ↓ dễ rối
Class SanPham { thuộc tính + phương thức đi cùng nhau }
```

**OOP = đóng gói dữ liệu và hành vi liên quan vào một “khuôn”.**

---

## 3. Class và Object

```
Class = khuôn bánh           Object = bánh cụ thể đã nướng
┌──────────────────┐         ┌──────────────────┐
│ class SanPham    │         │ $a = new SanPham │
│  - ten           │  ──new► │   ten = "Chuot"  │
│  - gia           │         │   gia = 250000   │
│  + thanhTien()   │         └──────────────────┘
└──────────────────┘         ┌──────────────────┐
                             │ $b = new SanPham │
                             │   ten = "Ban phim│
                             │   gia = 900000   │
                             └──────────────────┘
```

```php
<?php

class SanPham
{
    // Thuộc tính (properties)
    public string $ten;
    public int $gia;
    public int $soLuong;

    // Hàm khởi tạo — chạy khi new
    public function __construct(string $ten, int $gia, int $soLuong = 1)
    {
        $this->ten = $ten;
        $this->gia = $gia;
        $this->soLuong = $soLuong;
    }

    // Phương thức (methods)
    public function thanhTien(): int
    {
        return $this->gia * $this->soLuong;
    }

    public function moTa(): string
    {
        return "{$this->ten} - " . number_format($this->gia) . " đ";
    }
}

$sp1 = new SanPham('Chuot Logitech', 250000, 2);
$sp2 = new SanPham('Ban phim', 1200000);

echo $sp1->moTa();           // Chuot Logitech - 250,000 đ
echo $sp1->thanhTien();      // 500000
```

`$this` = “chính object đang gọi phương thức”.

---

## 4. Visibility: public / private / protected

| Mức | Ai truy cập được? |
|-----|-------------------|
| `public` | Mọi nơi |
| `private` | Chỉ bên trong class đó |
| `protected` | Class đó + class con (kế thừa) |

```php
<?php

class SinhVien
{
    public string $hoTen;
    private float $diemTb;

    public function __construct(string $hoTen, float $diemTb)
    {
        $this->hoTen = $hoTen;
        $this->setDiemTb($diemTb);
    }

    public function setDiemTb(float $diem): void
    {
        if ($diem < 0 || $diem > 10) {
            throw new InvalidArgumentException('Diem khong hop le');
        }
        $this->diemTb = $diem;
    }

    public function getDiemTb(): float
    {
        return $this->diemTb;
    }
}

$sv = new SinhVien('An', 8.5);
echo $sv->hoTen;
echo $sv->getDiemTb();
// echo $sv->diemTb; // LỖI: private
```

> Đây là **encapsulation**: không cho bên ngoài sửa dữ liệu lung tung.

---

## 5. Preview kế thừa (sẽ gặp lại trong Laravel Model)

```php
<?php

class NguoiDung
{
    public function __construct(public string $email) {}
}

class Admin extends NguoiDung
{
    public function quyen(): string
    {
        return 'toan_quyen';
    }
}

$admin = new Admin('admin@example.com');
echo $admin->email;     // kế thừa
echo $admin->quyen();   // riêng của Admin
```

---

## 6. Nhận dữ liệu từ trình duyệt: GET & POST

```
┌──────── Client ────────┐         ┌──── Server (PHP) ────┐
│ Form method="GET"      │ ──────► │ $_GET['ten']         │
│   ?ten=An&tuoi=20      │         │ (hiện trên URL)      │
│                        │         │                      │
│ Form method="POST"     │ ──────► │ $_POST['mat_khau']   │
│   body ẩn trong HTTP   │         │ (không hiện trên URL)│
└────────────────────────┘         └──────────────────────┘
```

| | GET | POST |
|---|-----|------|
| Dữ liệu trên URL? | Có | Không |
| Dùng khi | Tìm kiếm, lọc, phân trang | Đăng nhập, thêm/sửa/xóa |
| Độ dài | Giới hạn URL | Lớn hơn (phụ thuộc server) |

### 6.1. Form GET — tìm kiếm

```php
<!-- search.php -->
<form method="get" action="search.php">
    <input type="text" name="q" placeholder="Tu khoa">
    <button type="submit">Tim</button>
</form>

<?php
$q = $_GET['q'] ?? '';
if ($q !== '') {
    echo "Ban dang tim: " . htmlspecialchars($q, ENT_QUOTES, 'UTF-8');
}
```

### 6.2. Form POST — gửi dữ liệu nhạy cảm hơn

```php
<!-- login.php -->
<form method="post" action="login.php">
    <label>Username <input type="text" name="username"></label>
    <label>Password <input type="password" name="password"></label>
    <button type="submit">Dang nhap</button>
</form>

<?php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = trim($_POST['username'] ?? '');
    $password = $_POST['password'] ?? '';

    // Demo cứng — Buổi 4 sẽ lấy từ DB
    if ($username === 'admin' && $password === '123456') {
        echo "Dang nhap thanh cong";
    } else {
        echo "Sai tai khoan hoac mat khau";
    }
}
```

### 6.3. Luôn escape khi in ra HTML

```php
echo htmlspecialchars($username, ENT_QUOTES, 'UTF-8');
```

Tránh XSS cơ bản: người dùng nhập `<script>…</script>` sẽ không chạy được như mã độc.

---

## 7. Session — nhớ trạng thái giữa các request

HTTP **không trạng thái** (stateless). Mỗi request độc lập. Session giúp Server nhớ “user này đã đăng nhập”.

```
Request 1: POST login OK
    Server tạo Session ID → gửi Cookie PHPSESSID về trình duyệt
         │
Request 2: Browser tự gửi kèm Cookie PHPSESSID
    Server đọc $_SESSION → biết vẫn là user đó
```

```php
<?php
// BẮT BUỘC gọi trước khi dùng $_SESSION hoặc trước mọi output HTML
session_start();

$_SESSION['user_id'] = 1;
$_SESSION['username'] = 'admin';

echo $_SESSION['username'];

// Đăng xuất
// unset($_SESSION['username']);
// session_destroy();
```

### Ví dụ đăng nhập ảo hoàn chỉnh (2 file)

**login.php**

```php
<?php
session_start();

$error = '';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = trim($_POST['username'] ?? '');
    $password = $_POST['password'] ?? '';

    if ($username === 'admin' && $password === '123456') {
        $_SESSION['logged_in'] = true;
        $_SESSION['username'] = $username;
        header('Location: dashboard.php');
        exit;
    }
    $error = 'Sai thong tin dang nhap';
}
?>
<!DOCTYPE html>
<html lang="vi">
<head><meta charset="UTF-8"><title>Login</title></head>
<body>
    <h1>Dang nhap</h1>
    <?php if ($error): ?><p style="color:red"><?= htmlspecialchars($error) ?></p><?php endif; ?>
    <form method="post">
        <input name="username" placeholder="Username" required>
        <input type="password" name="password" placeholder="Password" required>
        <button>Login</button>
    </form>
</body>
</html>
```

**dashboard.php**

```php
<?php
session_start();

if (empty($_SESSION['logged_in'])) {
    header('Location: login.php');
    exit;
}
?>
<!DOCTYPE html>
<html lang="vi">
<head><meta charset="UTF-8"><title>Dashboard</title></head>
<body>
    <h1>Xin chao, <?= htmlspecialchars($_SESSION['username']) ?></h1>
    <a href="logout.php">Dang xuat</a>
</body>
</html>
```

**logout.php**

```php
<?php
session_start();
$_SESSION = [];
session_destroy();
header('Location: login.php');
exit;
```

---

## 8. Cookie — lưu phía Client

```php
<?php
// Lưu cookie 7 ngày
setcookie('theme', 'dark', time() + 7 * 24 * 60 * 60, '/');

// Đọc
$theme = $_COOKIE['theme'] ?? 'light';

// Xóa: set thời gian hết hạn trong quá khứ
setcookie('theme', '', time() - 3600, '/');
```

| | Session | Cookie |
|---|---------|--------|
| Lưu ở đâu? | Server (ID trên cookie) | Trình duyệt |
| Dùng cho | Đăng nhập, giỏ hàng tạm, flash message | Ngôn ngữ, theme, “nhớ tôi” |
| Bảo mật | An toàn hơn cho dữ liệu nhạy cảm | Không lưu mật khẩu vào cookie |

---

## 9. Bài thực hành tổng hợp: Class Product + Form + Session

```php
<?php
session_start();

class Product
{
    public function __construct(
        public string $name,
        public int $price
    ) {}

    public function label(): string
    {
        return $this->name . ' (' . number_format($this->price) . ' đ)';
    }
}

$catalog = [
    new Product('Chuot', 250000),
    new Product('Ban phim', 900000),
    new Product('Loa', 450000),
];

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $index = (int) ($_POST['product_index'] ?? -1);
    if (isset($catalog[$index])) {
        $_SESSION['cart'][] = $catalog[$index]->name;
    }
}

$cart = $_SESSION['cart'] ?? [];
?>
<!DOCTYPE html>
<html lang="vi">
<head><meta charset="UTF-8"><title>Buoi 3</title></head>
<body>
    <h1>San pham</h1>
    <form method="post">
        <select name="product_index">
            <?php foreach ($catalog as $i => $p): ?>
                <option value="<?= $i ?>"><?= htmlspecialchars($p->label()) ?></option>
            <?php endforeach; ?>
        </select>
        <button>Them vao gio</button>
    </form>

    <h2>Gio hang (Session)</h2>
    <ul>
        <?php foreach ($cart as $item): ?>
            <li><?= htmlspecialchars($item) ?></li>
        <?php endforeach; ?>
    </ul>
</body>
</html>
```

---

## 10. Tóm tắt & Checklist

```
OOP: Class → new Object → thuộc tính + phương thức + __construct
HTTP: GET (URL) / POST (body)
State: Session (server) / Cookie (client)
An toàn: htmlspecialchars khi in; không tin dữ liệu form
```

- [ ] Viết class `SanPham` / `SinhVien` có constructor
- [ ] Phân biệt public/private
- [ ] Form POST đăng nhập ảo + chặn vào dashboard khi chưa login
- [ ] Thêm item vào giỏ bằng Session

## 11. Bài tập trên lớp & về nhà (chuỗi MiniShop)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp** | Class `Category` + `Product` (8 object) | [Phiếu 03 — mục 2](./worksheets/phieu-03-oop-session.md) |
| **Về nhà** | Login Session `admin` / `MiniShop@03` + order Session | [Phiếu 03 — mục 3](./worksheets/phieu-03-oop-session.md) |

**Cầu nối Buổi 4:** 3 danh mục MiniShop lên **MySQL** — CRUD PDO bảng `categories` (chưa làm products).

→ [Buổi 4](./04-buoi-04-pdo-crud.md) · [Phiếu 04](./worksheets/phieu-04-pdo-categories.md)

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 3/12*
