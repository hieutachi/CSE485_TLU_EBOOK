# BUỔI 3: Lập trình Hướng đối tượng (OOP) & Quản lý trạng thái

> **Thời lượng gợi ý:** 4 giờ (lý thuyết + thực hành trên lớp) + Phiếu 03 về nhà  
> **Tiên quyết:** Buổi 1 (mảng), Buổi 2 (hàm, `stockLevel`, `lineTotal`)  
> **Kết quả đầu ra:** Viết Class/Object MiniShop; nhận GET/POST; dùng Session để login ảo  
> **Phiếu học tập:** [Phiếu 03](./worksheets/phieu-03-oop-session.md) — chuỗi MiniShop  
> **Dataset:** [CANONICAL-DATA](./worksheets/CANONICAL-DATA.md) — tổng kho CORE = **41380000**

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–15 | **Hướng cấu trúc vs OOP** (mục 2) | Chiếu 2 cột code MiniShop song song |
| 15–45 | Class / Object / `$this` / constructor | Live-code `Category`, `Product` |
| 45–60 | `public` / `private` + encapsulation | Demo gán `price = -1` nếu public |
| 60–75 | GET vs POST + `htmlspecialchars` | Demo URL lộ password nếu dùng GET |
| 75–105 | Session + login/logout | Live-code 3 file login |
| 105–145 | **Thực hành trên lớp Phiếu 03 mục 2** | SV tạo `src/` + `data.php` + bảng dashboard |
| 145–160 | Checkpoint + giao về nhà Phiếu 03 mục 3 | Nhắc credential `admin` / `MiniShop@03` |

> Trên lớp **ưu tiên xong OOP + in bảng 8 object**. Login/Session có thể demo nhanh rồi để SV hoàn thiện về nhà (Phiếu 03).

---

## 1. Mục tiêu buổi học

Sau Buổi 3, sinh viên có thể:

1. Phân biệt **lập trình hướng cấu trúc** (Buổi 1–2) và **lập trình hướng đối tượng** (Buổi 3) bằng ví dụ MiniShop.
2. Giải thích **Class vs Object** (`Category`, `Product`).
3. Viết class có thuộc tính, `__construct`, phương thức; dùng `$this`.
4. Phân biệt `public` / `private` và lý do encapsulation.
5. Nhận dữ liệu form bằng `$_GET` / `$_POST`; luôn escape khi in HTML.
6. Dùng **Session** để “nhớ” đăng nhập giữa các request; viết guard trang dashboard.
7. (Preview) Biết Cookie khác Session ở chỗ nào.

---

## 2. Lập trình hướng cấu trúc vs hướng đối tượng (trọng tâm lý thuyết)

Đây là bước nhảy tư duy quan trọng nhất giữa **Buổi 2** và **Buổi 3**.  
Cùng một bài toán MiniShop — hai cách tổ chức code khác nhau.

### 2.1. Lập trình hướng cấu trúc (Structured Programming) là gì?

**Định nghĩa ngắn:** chương trình được tổ chức thành **chuỗi lệnh + hàm**, dữ liệu (biến/mảng) và xử lý (hàm) **tách rời**. Luồng chạy đi từ trên xuống, rẽ nhánh (`if`), lặp (`foreach`), gọi hàm khi cần.

```
DỮ LIỆU (passive)              XỬ LÝ (active)
┌──────────────────┐           ┌─────────────────────┐
│ $products = [    │           │ lineTotal($p)       │
│   [sku, price…]  │  ──────►  │ stockLevel($p)      │
│ ]                │  truyền   │ inventoryValue($ps) │
└──────────────────┘  vào hàm  └─────────────────────┘
```

**Đặc điểm:**

| Đặc điểm | Giải thích | Bạn đã làm ở đâu? |
|----------|------------|-------------------|
| Chương trình = dữ liệu + thuật toán | Mảng lưu SP; hàm tính toán | Buổi 1–2 |
| Hàm nhận dữ liệu từ ngoài | `lineTotal(array $product)` | `helpers.php` |
| Dễ học, khớp tư duy “làm từng bước” | Đọc từ trên xuống | Phiếu 01–02 |
| Khi lớn lên dễ rối | Cùng một mảng bị nhiều hàm đụng vào; quên gọi hàm đúng | — |

**Ví dụ hướng cấu trúc — đúng kiểu Buổi 2 MiniShop:**

```php
<?php
// ===== HƯỚNG CẤU TRÚC =====
// 1) Dữ liệu: chỉ là mảng (không “biết” tự tính gì cả)
$product = [
    'sku' => 'KB-01',
    'name' => 'Keychron K2',
    'price' => 1890000,
    'qty' => 3,
];

// 2) Xử lý: hàm ở ngoài, nhận mảng vào
function lineTotal(array $p): int
{
    return $p['price'] * $p['qty'];
}

function stockLevel(array $p): string
{
    if ($p['qty'] >= 5) {
        return 'Du';
    }
    if ($p['qty'] >= 2) {
        return 'Sap het';
    }
    return 'Can nhap';
}

// 3) Dùng: lập trình viên phải NHỚ gọi đúng hàm + truyền đúng mảng
echo lineTotal($product);    // 5670000
echo stockLevel($product);   // Sap het

// Nguy cơ: ai đó tính tay, bỏ qua hàm chuẩn
echo $product['price'] + $product['qty']; // SAI nghiệp vụ — máy vẫn chạy!
```

**Hình ảnh nhớ:**  
Hướng cấu trúc giống **quầy tự phục vụ**: nguyên liệu (mảng) để trên bàn; công thức (hàm) dán trên tường; bạn phải tự lấy nguyên liệu + làm đúng bước.

---

### 2.2. Lập trình hướng đối tượng (OOP) là gì?

**Định nghĩa ngắn:** gom **dữ liệu + hành vi liên quan** vào một đơn vị gọi là **đối tượng (object)**, được tạo từ **lớp (class)** — bản thiết kế chung.

```
┌─────────────────────────────────────────┐
│  OBJECT Product (KB-01)                 │
│  ┌─────────────┐   ┌─────────────────┐  │
│  │ Dữ liệu     │   │ Hành vi         │  │
│  │ sku, price  │ + │ lineTotal()     │  │
│  │ qty, name…  │   │ stockLevel()    │  │
│  └─────────────┘   └─────────────────┘  │
└─────────────────────────────────────────┘
         ▲
         │ new Product(...)
┌────────┴────────┐
│  CLASS Product  │  ← khuôn / thiết kế
└─────────────────┘
```

**Bốn ý tưởng cốt lõi (mức nhập môn Buổi 3):**

| Ý tưởng | Nghĩa dân dã | Trong MiniShop |
|---------|--------------|----------------|
| **Đóng gói (Encapsulation)** | SP tự mang theo cách tính tiền / mức tồn | `$p->lineTotal()` |
| **Lớp (Class)** | Khuôn chung cho mọi sản phẩm | `class Product` |
| **Đối tượng (Object)** | Một SP cụ thể | `KB-01`, `MS-01`… |
| **Che giấu chi tiết** | Bên ngoài không cần biết công thức bên trong | View chỉ gọi method |

(Kế thừa / đa hình: preview nhẹ; Laravel Model sẽ gặp lại.)

**Ví dụ hướng đối tượng — đúng kiểu Buổi 3 MiniShop:**

```php
<?php
// ===== HƯỚNG ĐỐI TƯỢNG =====
class Product
{
    public function __construct(
        public string $sku,
        public string $name,
        public int $categoryId,
        public int $price,
        public int $qty
    ) {}

    // Hành vi ĐI CÙNG dữ liệu
    public function lineTotal(): int
    {
        return $this->price * $this->qty;
    }

    public function stockLevel(): string
    {
        if ($this->qty >= 5) {
            return 'Du';
        }
        if ($this->qty >= 2) {
            return 'Sap het';
        }
        return 'Can nhap';
    }
}

$product = new Product('KB-01', 'Keychron K2', 1, 1890000, 3);

// Dùng: hỏi CHÍNH object đó — không cần nhớ hàm rời ở file khác
echo $product->lineTotal();    // 5670000
echo $product->stockLevel();   // Sap het
```

**Hình ảnh nhớ:**  
OOP giống **nhân viên quầy**: mỗi sản phẩm là một “nhân viên” biết giá và số lượng của mình, bạn chỉ cần hỏi *“thành tiền bao nhiêu?”* (`lineTotal()`).

---

### 2.3. So sánh trực tiếp trên CÙNG bài toán MiniShop

| Tiêu chí | Hướng cấu trúc (Buổi 2) | Hướng đối tượng (Buổi 3) |
|----------|-------------------------|---------------------------|
| Đơn vị tổ chức | Hàm + mảng | Class + Object |
| Thành tiền | `lineTotal($p)` | `$p->lineTotal()` |
| Mức tồn | `stockLevel($p)` | `$p->stockLevel()` |
| Dữ liệu nằm đâu? | `$products[]` (mảng) | Thuộc tính trong object |
| Ai “biết” quy tắc nghiệp vụ? | Lập trình viên + file `helpers.php` | Chính object `Product` |
| Thêm SP mới | Thêm 1 phần tử mảng | `new Product(...)` |
| Rủi ro thường gặp | Quên gọi hàm / tính tay sai | Quên `new` / nhầm `$this` |
| Khi dự án lớn | Nhiều hàm đụng chung mảng → khó bảo trì | Gom logic theo thực thể → rõ trách nhiệm |
| Cầu nối Laravel | — | `Product` ≈ Model Eloquent |

```
HƯỚNG CẤU TRÚC                         HƯỚNG ĐỐI TƯỢNG
─────────────────────                  ──────────────────────────
$products (mảng)                       $productObjects (object[])
      │                                      │
      ▼                                      ▼
helpers.php  lineTotal($p)             Product::lineTotal()  ← trong class
index.php    echo lineTotal($p)        dashboard.php  echo $p->lineTotal()
```

---

### 2.4. Ví dụ “đôi” — cùng 2 sản phẩm, hai phong cách

Chạy hai khối này trên lớp (hoặc để SV đổi từ trái sang phải):

```php
<?php
// ---------- A) CẤU TRÚC ----------
$cart = [
    ['sku' => 'KB-01', 'price' => 1890000, 'qty' => 3],
    ['sku' => 'MN-02', 'price' => 8500000, 'qty' => 1],
];

function lineTotal(array $p): int
{
    return $p['price'] * $p['qty'];
}

$sumA = 0;
foreach ($cart as $row) {
    $sumA += lineTotal($row); // phải nhớ hàm + truyền mảng
}
echo "Cau truc: {$sumA}\n"; // 14170000


// ---------- B) ĐỐI TƯỢNG ----------
class ProductDemo
{
    public function __construct(
        public string $sku,
        public int $price,
        public int $qty
    ) {}

    public function lineTotal(): int
    {
        return $this->price * $this->qty;
    }
}

$cartB = [
    new ProductDemo('KB-01', 1890000, 3),
    new ProductDemo('MN-02', 8500000, 1),
];

$sumB = 0;
foreach ($cartB as $p) {
    $sumB += $p->lineTotal(); // hỏi từng object
}
echo "OOP: {$sumB}\n"; // 14170000 — CÙNG KẾT QUẢ, KHÁC CÁCH TỔ CHỨC
```

**Chốt miệng trên lớp:**  
*Kết quả nghiệp vụ giống nhau (14170000). Khác ở chỗ **code được sắp xếp thế nào** khi hệ thống phình to.*

---

### 2.5. Khi nào dùng gì? (trả lời SV hay hỏi)

| Tình huống | Nên nghiêng về |
|------------|----------------|
| Script ngắn, bài tập nhỏ, học điều khiển / hàm | Hướng cấu trúc (Buổi 1–2) |
| Nhiều thực thể (SP, DM, User…) + quy tắc gắn từng thực thể | OOP (Buổi 3 → Laravel) |
| Framework Laravel, Model, Eloquent | OOP (bắt buộc tư duy class) |
| Hàm tiện ích thuần (format tiền, escape HTML) | Vẫn dùng hàm — không cần nhồi mọi thứ vào class |

> **Lưu ý quan trọng:** OOP **không thay thế** `if/foreach/hàm`. Bên trong method vẫn là lập trình cấu trúc. OOP là lớp **tổ chức** bên ngoài.

```
OOP (vỏ tổ chức)
└── method lineTotal()
       └── bên trong: price * qty   ← vẫn là cấu trúc!
```

---

### 2.6. Câu hỏi thảo luận (5 phút trên lớp)

1. Trong code cấu trúc, nếu một bạn tính `$p['price'] + $p['qty']` thay vì gọi `lineTotal`, chuyện gì xảy ra? OOP giúp giảm rủi ro đó thế nào?  
2. `helpers.php` và `class Product` khác nhau chỗ nào về **trách nhiệm**?  
3. Vì sao Laravel dùng `Product::create([...])` / `$product->update([...])` thay vì chỉ mảng + hàm SQL rời?

---

### 2.7. Điểm đứng trước khi code OOP tiếp

Ở Buổi 1–2, MiniShop đang hướng cấu trúc:

```
data.php          → mảng $categories, $products
helpers.php       → lineTotal(), stockLevel(), inventoryValue()…
index.php         → foreach mảng + gọi hàm
```

Từ đây trở đi (Buổi 3), ta **chuyển dần** sang:

```
src/Product.php   → class + method
src/Category.php  → class + method
data.php          → new Product(...) / new Category(...)
dashboard.php     → $p->lineTotal()
```

```
Buổi 2 (cấu trúc)                 Buổi 3 (OOP)
─────────────────────             ─────────────────────────
$p['price'] * $p['qty']      →    $product->lineTotal()
stockLevel($p)               →    $product->stockLevel()
```

---

## 3. Class và Object — hiểu bằng hình ảnh

```
Class = khuôn bánh                 Object = bánh đã nướng
┌─────────────────────┐            ┌──────────────────────┐
│ class Product       │            │ $a = new Product(...)│
│  sku, name, price…  │   ─new─►   │  sku = "KB-01"       │
│  lineTotal()        │            │  name = "Keychron K2"│
│  stockLevel()       │            └──────────────────────┘
└─────────────────────┘            ┌──────────────────────┐
                                   │ $b = new Product(...)│
                                   │  sku = "MS-01"       │
                                   └──────────────────────┘
```

| Khái niệm | Giải thích ngắn | Ví dụ MiniShop |
|-----------|-----------------|----------------|
| **Class** | Bản thiết kế / khuôn | `Product` |
| **Object** | Một thể hiện cụ thể | SP `KB-01` |
| **Property** | Dữ liệu của object | `$price`, `$qty` |
| **Method** | Hành vi của object | `lineTotal()` |
| **`$this`** | “Chính object đang chạy method” | `$this->price` |
| **`new`** | Tạo object từ class | `new Product(...)` |

### 3.1. Cú pháp tối thiểu

```php
<?php

class Product
{
    // Thuộc tính
    public string $sku;
    public string $name;
    public int $categoryId;
    public int $price;
    public int $qty;

    // Constructor: chạy ngay khi new
    public function __construct(
        string $sku,
        string $name,
        int $categoryId,
        int $price,
        int $qty
    ) {
        $this->sku = $sku;
        $this->name = $name;
        $this->categoryId = $categoryId;
        $this->price = $price;
        $this->qty = $qty;
    }

    // Method: thành tiền (mang logic từ Buổi 2 vào class)
    public function lineTotal(): int
    {
        return $this->price * $this->qty;
    }

    // Method: mức tồn — chuỗi EXACT theo CANONICAL-DATA
    public function stockLevel(): string
    {
        if ($this->qty >= 5) {
            return 'Du';
        }
        if ($this->qty >= 2) {
            return 'Sap het';
        }
        return 'Can nhap';
    }

    public function toArray(): array
    {
        return [
            'sku' => $this->sku,
            'name' => $this->name,
            'category_id' => $this->categoryId,
            'price' => $this->price,
            'qty' => $this->qty,
            'line_total' => $this->lineTotal(),
            'stock' => $this->stockLevel(),
        ];
    }
}

$p = new Product('KB-01', 'Keychron K2', 1, 1890000, 3);

echo $p->name;            // Keychron K2
echo $p->lineTotal();     // 5670000
echo $p->stockLevel();    // Sap het
```

### 3.2. Constructor property promotion (PHP 8+) — viết gọn

Khi dạy lớp đã dùng PHP 8+, có thể rút gọn:

```php
<?php

class Category
{
    public function __construct(
        public int $id,
        public string $name,
        public string $description = ''
    ) {}

    public function label(): string
    {
        return "[{$this->id}] {$this->name}";
    }
}

$cat = new Category(1, 'Ban phim', 'Danh muc ban phim co / membrane');
echo $cat->label(); // [1] Ban phim
```

> **Lưu ý giảng:** Promotion chỉ là đường tắt khai báo thuộc tính. Bản chất OOP không đổi. Nếu SV dùng PHP 7.x trên XAMPP cũ, hãy viết dạng đầy đủ mục 3.1.

### 3.3. Câu hỏi kiểm tra nhanh (đặt trên lớp)

1. `Product` và `$p` cái nào là class, cái nào là object?  
2. Trong `lineTotal()`, vì sao viết `$this->price` mà không phải `$price`?  
3. Hai lệnh `new Product(...)` tạo ra bao nhiêu object trong bộ nhớ?

---

## 4. Visibility & Encapsulation

| Mức | Ai truy cập? | Dùng khi nào (mức nhập môn) |
|-----|--------------|-----------------------------|
| `public` | Mọi nơi | Thuộc tính/method cần gọi từ ngoài (demo) |
| `private` | Chỉ trong class | Dữ liệu cần bảo vệ / validate trước khi gán |
| `protected` | Class + class con | Preview kế thừa (Laravel sau này) |

### 4.1. Demo “vì sao cần private”

```php
<?php

class ProductUnsafe
{
    public int $price;
}

$x = new ProductUnsafe();
$x->price = -999999; // Vẫn “chạy” — dữ liệu sai vẫn tồn tại!
```

Cách an toàn hơn (ý tưởng; Phiếu 03 chưa bắt buộc private hết):

```php
<?php

class ProductSafe
{
    private int $price;

    public function __construct(int $price)
    {
        $this->setPrice($price);
    }

    public function setPrice(int $price): void
    {
        if ($price < 0) {
            throw new InvalidArgumentException('Gia khong hop le');
        }
        $this->price = $price;
    }

    public function getPrice(): int
    {
        return $this->price;
    }
}
```

**Encapsulation** = không cho bên ngoài sửa lung tung; mọi thay đổi đi qua method có kiểm tra.

> Trên lớp: với MiniShop buổi này, **public + constructor** là đủ để SV hoàn thành Phiếu 03. Private là “hạt giống” tư duy — đừng nhồi quá nhiều trong 1 buổi.

---

## 5. Preview kế thừa (chỉ 5 phút)

Laravel Model sau này: `class Product extends Model`. Hôm nay chỉ cần nhận ra từ khóa `extends`:

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

$admin = new Admin('admin@minishop.local');
echo $admin->email;   // kế thừa từ NguoiDung
echo $admin->quyen(); // riêng Admin
```

```
NguoiDung
    ▲
    │ extends
 Admin  (+ quyen())
```

---

## 6. Từ mảng Buổi 2 → object Buổi 3 (trọng tâm thực hành)

### 6.1. Cấu trúc thư mục đề xuất (khớp Phiếu 03)

```
htdocs/minishop-03/
├── src/
│   ├── Category.php
│   └── Product.php
├── data.php          ← new Category / new Product từ CANONICAL-DATA
├── login.php         ← về nhà
├── dashboard.php     ← trên lớp: bảng 8 SP; về nhà: + guard + orders
└── logout.php        ← về nhà
```

### 6.2. `src/Category.php` (mẫu giảng)

```php
<?php

declare(strict_types=1);

class Category
{
    public function __construct(
        public int $id,
        public string $name,
        public string $description = ''
    ) {}

    public function label(): string
    {
        return "[{$this->id}] {$this->name}";
    }
}
```

### 6.3. `src/Product.php` (mẫu giảng — khớp CLO Phiếu 03)

```php
<?php

declare(strict_types=1);

class Product
{
    public function __construct(
        public string $sku,
        public string $name,
        public int $categoryId,
        public int $price,
        public int $qty
    ) {}

    public function lineTotal(): int
    {
        return $this->price * $this->qty;
    }

    public function stockLevel(): string
    {
        if ($this->qty >= 5) {
            return 'Du';
        }
        if ($this->qty >= 2) {
            return 'Sap het';
        }
        return 'Can nhap';
    }

    public function toArray(): array
    {
        return [
            'sku' => $this->sku,
            'name' => $this->name,
            'category_id' => $this->categoryId,
            'price' => $this->price,
            'qty' => $this->qty,
            'line_total' => $this->lineTotal(),
            'stock' => $this->stockLevel(),
        ];
    }
}
```

### 6.4. `data.php` — tạo 3 + 8 object

```php
<?php

declare(strict_types=1);

require_once __DIR__ . '/src/Category.php';
require_once __DIR__ . '/src/Product.php';

/** @var Category[] $categoryObjects */
$categoryObjects = [
    new Category(1, 'Ban phim', 'Danh muc ban phim co / membrane'),
    new Category(2, 'Chuot', 'Danh muc chuot may tinh'),
    new Category(3, 'Man hinh', 'Danh muc man hinh'),
];

/** @var Product[] $productObjects */
$productObjects = [
    new Product('KB-01', 'Keychron K2', 1, 1890000, 3),
    new Product('KB-02', 'Akko 3087', 1, 1290000, 5),
    new Product('KB-03', 'Leopold FC660M', 1, 2750000, 2),
    new Product('MS-01', 'Logitech M331', 2, 290000, 10),
    new Product('MS-02', 'Razer Viper', 2, 990000, 4),
    new Product('MS-03', 'Xiaomi Silent', 2, 250000, 8),
    new Product('MN-01', 'Dell 24 inch', 3, 3200000, 2),
    new Product('MN-02', 'LG UltraFine', 3, 8500000, 1),
];

/** Map id → tên danh mục (phục vụ in bảng) */
$categoryMap = [];
foreach ($categoryObjects as $cat) {
    $categoryMap[$cat->id] = $cat->name;
}

function inventoryValueFromObjects(array $products): int
{
    $sum = 0;
    foreach ($products as $p) {
        /** @var Product $p */
        $sum += $p->lineTotal();
    }
    return $sum;
}
```

**Checkpoint trên lớp:**  
`inventoryValueFromObjects($productObjects)` phải ra **41380000**.

### 6.5. In bảng trên `dashboard.php` (bản trên lớp — chưa bắt buộc login)

```php
<?php

declare(strict_types=1);

require_once __DIR__ . '/data.php';

$total = inventoryValueFromObjects($productObjects);

function h(string $s): string
{
    return htmlspecialchars($s, ENT_QUOTES, 'UTF-8');
}
?>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>MiniShop — Dashboard (Buoi 3)</title>
    <style>
        body { font-family: system-ui, sans-serif; margin: 1.5rem; }
        table { border-collapse: collapse; width: 100%; max-width: 960px; }
        th, td { border: 1px solid #ccc; padding: .5rem .75rem; text-align: left; }
        th { background: #f3f4f6; }
    </style>
</head>
<body>
    <h1>MiniShop Dashboard (OOP)</h1>
    <p>So san pham: <?= count($productObjects) ?></p>
    <p>Tong gia tri kho: <strong><?= $total ?></strong></p>

    <table>
        <thead>
            <tr>
                <th>SKU</th>
                <th>Ten</th>
                <th>Danh muc</th>
                <th>Gia</th>
                <th>SL</th>
                <th>Thanh tien</th>
                <th>Muc ton</th>
            </tr>
        </thead>
        <tbody>
            <?php foreach ($productObjects as $p): ?>
                <tr>
                    <td><?= h($p->sku) ?></td>
                    <td><?= h($p->name) ?></td>
                    <td><?= h($categoryMap[$p->categoryId] ?? '—') ?></td>
                    <td><?= $p->price ?></td>
                    <td><?= $p->qty ?></td>
                    <td><?= $p->lineTotal() ?></td>
                    <td><?= h($p->stockLevel()) ?></td>
                </tr>
            <?php endforeach; ?>
        </tbody>
    </table>
</body>
</html>
```

### 6.6. Quy tắc “vàng” khi chấm trên lớp

| Được | Không được |
|------|------------|
| `$p->lineTotal()` | `$p->price * $p->qty` ngay trong vòng lặp view |
| `$p->stockLevel()` | Copy lại if/else stock trong `dashboard.php` |
| `htmlspecialchars` khi echo tên/SKU | Echo thô `$p->name` |

---

## 7. Nhận dữ liệu từ trình duyệt: GET & POST

HTTP request mang dữ liệu vào PHP qua các “túi” toàn cục:

```
Browser
   │
   │  GET  /search.php?q=ban+phim     →  $_GET['q']
   │  POST /login.php  (body form)   →  $_POST['username'], $_POST['password']
   ▼
PHP đọc → xử lý → trả HTML
```

| | GET | POST |
|---|-----|------|
| Hiện trên URL? | Có | Không (body) |
| Bookmark / chia sẻ được? | Dễ | Khó |
| Dùng khi | Tìm kiếm, lọc, phân trang | Login, tạo/sửa/xóa, dữ liệu nhạy cảm |
| Độ dài | Giới hạn URL | Lớn hơn (phụ thuộc server) |

### 7.1. Form GET — lọc (ôn từ Buổi 2)

```php
<form method="get" action="dashboard.php">
    <label>Category ID
        <input type="number" name="category_id" value="<?= h((string) ($_GET['category_id'] ?? '')) ?>">
    </label>
    <button type="submit">Loc</button>
</form>
```

```php
$categoryId = isset($_GET['category_id']) && $_GET['category_id'] !== ''
    ? (int) $_GET['category_id']
    : null;
```

### 7.2. Form POST — đăng nhập (trọng tâm Buổi 3)

```php
<form method="post" action="login.php">
    <label>Username <input type="text" name="username" required></label>
    <label>Password <input type="password" name="password" required></label>
    <button type="submit">Dang nhap</button>
</form>
```

```php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = trim($_POST['username'] ?? '');
    $password = $_POST['password'] ?? '';
    // xử lý…
}
```

**Demo trên lớp (rất nên làm):**  
Đổi tạm `method="get"` ở form login → submit → chỉ URL có `?password=...` → hỏi SV: “Vì sao POST an toàn hơn trong ngữ cảnh này?”

### 7.3. Escape output — chống XSS cơ bản

```php
echo htmlspecialchars($username, ENT_QUOTES, 'UTF-8');
// hoặc helper h() như trên
```

Thử nhập username: `<script>alert(1)</script>`  
- Không escape → script có thể chạy.  
- Có `htmlspecialchars` → hiện như chữ thường.

### 7.4. Redirect đúng cách

```php
header('Location: dashboard.php');
exit; // BẮT BUỘC — tránh chạy tiếp code phía dưới
```

Lỗi hay gặp: gọi `header()` **sau khi đã echo HTML** → “headers already sent”.

---

## 8. Session — nhớ trạng thái giữa các request

### 8.1. Vì sao cần Session?

HTTP **stateless**: mỗi request độc lập. Biến PHP `$loggedIn = true` **mất** khi request kết thúc.

```
Request 1: POST login OK
     Server tạo session file + Session ID
     Gửi Set-Cookie: PHPSESSID=abc123  →  Browser lưu
              │
Request 2: Browser tự gửi Cookie PHPSESSID=abc123
     Server tìm đúng session → $_SESSION['auth'] vẫn true
```

### 8.2. API tối thiểu

```php
<?php
session_start(); // gọi TRƯỚC mọi output HTML và trước khi đụng $_SESSION

$_SESSION['auth'] = true;
$_SESSION['username'] = 'admin';

$ok = !empty($_SESSION['auth']);

// Xóa một key
unset($_SESSION['username']);

// Hủy cả session (logout)
$_SESSION = [];
session_destroy();
```

### 8.3. Credential chuẩn MiniShop (Phiếu 03)

| Field | Value |
|-------|-------|
| username | `admin` |
| password | `MiniShop@03` |
| session keys | `auth = true`, `username = 'admin'` |

> **Không** dùng `123456` trong bài nộp Phiếu 03 — GRADING-CONTRACT / CANONICAL-DATA đã khóa credential trên.

### 8.4. Bộ ba file login (mẫu hoàn chỉnh — giao về nhà)

**login.php**

```php
<?php

declare(strict_types=1);

session_start();

// Đã login rồi thì khỏi hiện form
if (!empty($_SESSION['auth'])) {
    header('Location: dashboard.php');
    exit;
}

$error = '';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = trim($_POST['username'] ?? '');
    $password = $_POST['password'] ?? '';

    if ($username === 'admin' && $password === 'MiniShop@03') {
        $_SESSION['auth'] = true;
        $_SESSION['username'] = $username;
        header('Location: dashboard.php');
        exit;
    }

    $error = 'Sai thong tin dang nhap';
}

function h(string $s): string
{
    return htmlspecialchars($s, ENT_QUOTES, 'UTF-8');
}
?>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>MiniShop Login</title>
</head>
<body>
    <h1>Dang nhap MiniShop</h1>
    <?php if ($error !== ''): ?>
        <p style="color:#b91c1c"><?= h($error) ?></p>
    <?php endif; ?>
    <form method="post" action="login.php">
        <p>
            <label>Username
                <input type="text" name="username" required autocomplete="username">
            </label>
        </p>
        <p>
            <label>Password
                <input type="password" name="password" required autocomplete="current-password">
            </label>
        </p>
        <button type="submit">Dang nhap</button>
    </form>
</body>
</html>
```

**Phần đầu dashboard.php (bản về nhà — có guard)**

```php
<?php

declare(strict_types=1);

session_start();

if (empty($_SESSION['auth'])) {
    header('Location: login.php');
    exit;
}

require_once __DIR__ . '/data.php';

// … in bảng object như mục 6.5 …
// Thêm: Xin chao, <?= h($_SESSION['username']) ?>
// Thêm form order + list $_SESSION['orders'] (Phiếu 03)
```

**logout.php**

```php
<?php

declare(strict_types=1);

session_start();
$_SESSION = [];

if (ini_get('session.use_cookies')) {
    $p = session_get_cookie_params();
    setcookie(session_name(), '', time() - 42000, $p['path'], $p['domain'], $p['secure'], $p['httponly']);
}

session_destroy();
header('Location: login.php');
exit;
```

### 8.5. Orders trong Session (nhiệm vụ về nhà)

Ý tưởng: chứng minh Session giữ được dữ liệu sau F5.

```php
// Trong dashboard.php — xử lý POST order
if ($_SERVER['REQUEST_METHOD'] === 'POST' && ($_POST['action'] ?? '') === 'order') {
    $sku = trim($_POST['sku'] ?? '');
    $qty = (int) ($_POST['qty'] ?? 0);

    if ($sku !== '' && $qty > 0) {
        $_SESSION['orders'][] = [
            'sku' => $sku,
            'qty' => $qty,
            'at' => date('H:i:s'),
        ];
    }
    header('Location: dashboard.php'); // PRG pattern — tránh F5 submit lại
    exit;
}

$orders = $_SESSION['orders'] ?? [];
```

Form gợi ý:

```php
<form method="post">
    <input type="hidden" name="action" value="order">
    <select name="sku">
        <?php foreach ($productObjects as $p): ?>
            <option value="<?= h($p->sku) ?>"><?= h($p->sku . ' — ' . $p->name) ?></option>
        <?php endforeach; ?>
    </select>
    <input type="number" name="qty" min="1" value="1" required>
    <button type="submit">Dat thu</button>
</form>
```

### 8.6. Kịch bản demo / chấm (làm trên lớp cuối giờ hoặc video nộp)

| Bước | Thao tác | Kết quả kỳ vọng |
|------|----------|-----------------|
| 1 | Mở `dashboard.php` khi chưa login | Redirect `login.php` |
| 2 | Login sai password | Ở lại login + thông báo lỗi |
| 3 | Login `admin` / `MiniShop@03` | Vào dashboard, 8 SP, tổng **41380000** |
| 4 | Thêm 2 order, nhấn F5 | Danh sách order vẫn còn |
| 5 | Logout → mở lại dashboard | Bị chặn, về login |

---

## 9. Cookie — phân biệt nhanh với Session

```php
<?php
// Cookie lưu phía trình duyệt — KHÔNG dùng để lưu mật khẩu / auth chính
setcookie('theme', 'dark', [
    'expires' => time() + 7 * 24 * 60 * 60,
    'path' => '/',
    'httponly' => true,
    'samesite' => 'Lax',
]);

$theme = $_COOKIE['theme'] ?? 'light';
```

| | Session | Cookie |
|---|---------|--------|
| Dữ liệu nằm đâu? | Server (ID trên cookie) | Trình duyệt |
| Phù hợp | Login, giỏ/order tạm, flash | Theme, ngôn ngữ, “nhớ username” (không nhớ password) |
| SV cần làm Phiếu 03? | **Bắt buộc** | Chỉ cần hiểu khái niệm |

---

## 10. Lỗi thường gặp trên lớp (xử lý nhanh)

| Triệu chứng | Nguyên nhân | Cách xử |
|-------------|-------------|---------|
| `Class "Product" not found` | Quên `require_once` file class | Require `src/Product.php` trước `new` |
| `Cannot redeclare class` | Require class 2 lần không `*_once` | Dùng `require_once` |
| Tổng kho ≠ 41380000 | Sai giá/qty so với CANONICAL | So lại 8 dòng dataset |
| `headers already sent` | Echo/HTML trước `header()` / `session_start()` | `session_start()` dòng đầu file |
| Login xong vẫn bị đá về | Guard kiểm tra sai key (`logged_in` vs `auth`) | Thống nhất `$_SESSION['auth']` |
| F5 bị submit lại form | Không redirect sau POST | PRG: POST → `header Location` → GET |
| Tiếng Việt lỗi font | Thiếu charset | `<meta charset="UTF-8">` |

Bật lỗi khi học (đầu file PHP):

```php
<?php
ini_set('display_errors', '1');
error_reporting(E_ALL);
```

---

## 11. Tóm tắt bảng trắng (chốt buổi)

```
OOP
  Class  = khuôn (Product, Category)
  Object = new …
  Method = lineTotal(), stockLevel()   ← logic Buổi 2 “đi vào” object

HTTP form
  GET  = lọc / tìm (lên URL)
  POST = login / ghi dữ liệu
  In ra HTML → htmlspecialchars

Session
  session_start()
  $_SESSION['auth'], ['username'], ['orders']
  Guard: chưa auth → redirect login
  Logout: clear + destroy
```

### Checklist trước khi ra về

- [ ] Phân biệt được hướng cấu trúc (mảng+hàm) và OOP (class+object) trên MiniShop  
- [ ] Giải thích được Class vs Object bằng MiniShop  
- [ ] `data.php` tạo đủ 3 Category + 8 Product  
- [ ] Dashboard gọi `$p->lineTotal()` / `$p->stockLevel()`  
- [ ] Tổng kho = **41380000**  
- [ ] Hiểu vì sao cần `session_start()` và POST cho login  

---

## 12. Bài tập trên lớp & về nhà (chuỗi MiniShop)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20–40')** | `Category` + `Product` + `data.php` + bảng dashboard | [Phiếu 03 — mục 2](./worksheets/phieu-03-oop-session.md) |
| **Về nhà (~30')** | Login `admin` / `MiniShop@03` + guard + orders Session | [Phiếu 03 — mục 3](./worksheets/phieu-03-oop-session.md) |

**Repo nộp:** `cse485-ms-03` + video OBS (camera mặt) theo box cuối phiếu.

### Câu hỏi thuyết trình bắt buộc trong video nộp

1. Lập trình hướng cấu trúc và hướng đối tượng khác nhau thế nào? Lấy ví dụ `lineTotal` MiniShop.  
2. Class và object khác nhau thế nào trong bài của bạn?  
3. Vì sao dùng Session chứ không chỉ biến PHP thường?

---

## 13. Cầu nối Buổi 4

Hôm nay Category/Product vẫn “sống” trong bộ nhớ PHP (mảng object).  
Buổi 4: đưa **categories** vào **MySQL**, thao tác CRUD bằng **PDO** — bước đầu thay mảng bằng CSDL thật.

→ [Buổi 4](./04-buoi-04-pdo-crud.md) · [Phiếu 04](./worksheets/phieu-04-pdo-categories.md)

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 3/12*
