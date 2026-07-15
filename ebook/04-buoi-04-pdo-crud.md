# BUỔI 4: Cơ sở dữ liệu & Tương tác MySQL bằng PDO

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 1–3  
> **Kết quả đầu ra:** Tạo bảng MySQL; CRUD một bảng bằng PDO (Prepared Statements)  
> **Phiếu học tập liên quan:** [Phiếu 04](./worksheets/phieu-04-pdo-categories.md) — chuỗi MiniShop
> **Ví dụ xuyên suốt:** Bảng `categories`

---

## 1. Mục tiêu buổi học

1. Hiểu vai trò CSDL trong Backend.
2. Tạo database + bảng `categories` trên MySQL (phpMyAdmin hoặc SQL).
3. Kết nối PHP ↔ MySQL bằng **PDO**.
4. Thực hiện Create / Read / Update / Delete an toàn (chống SQL Injection).

---

## 2. Vì sao cần cơ sở dữ liệu?

Session/Cookie chỉ lưu tạm. Dữ liệu nghiệp vụ (danh mục, sản phẩm, đơn hàng) cần lưu **bền vững**:

```
Trình duyệt ──HTTP──► PHP ──PDO──► MySQL
                         ▲
                         └── Kết quả rows → PHP → HTML
```

---

## 3. Chuẩn bị MySQL trên XAMPP

1. Start **Apache** + **MySQL** trong XAMPP.
2. Mở phpMyAdmin: `http://localhost/phpmyadmin`
3. Tạo database: `cse485_ebook` (Collation: `utf8mb4_unicode_ci`).

### SQL tạo bảng `categories`

```sql
CREATE TABLE categories (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

Thêm vài dòng mẫu:

```sql
INSERT INTO categories (name, description) VALUES
('Laptop', 'May tinh xach tay'),
('Chuot', 'Thiet bi tro chuot'),
('Ban phim', 'Ban phim co / thuong');
```

---

## 4. PDO là gì? Vì sao không dùng `mysqli_query` nối chuỗi?

**PDO** (PHP Data Objects) = lớp trừu tượng kết nối CSDL, hỗ trợ Prepared Statements.

```
❌ Nguy hiểm:
"SELECT * FROM users WHERE email = '$email'"
   → Nếu $email = ' OR 1=1 --  → SQL Injection

✅ An toàn:
SELECT * FROM users WHERE email = ?
   → PDO bind giá trị, tách khỏi câu lệnh SQL
```

---

## 5. Kết nối PDO

Tạo `htdocs/cse485-buoi4/config.php`:

```php
<?php

declare(strict_types=1);

const DB_HOST = '127.0.0.1';
const DB_NAME = 'cse485_ebook';
const DB_USER = 'root';
const DB_PASS = ''; // XAMPP mặc định thường để trống
const DB_CHARSET = 'utf8mb4';

function db(): PDO
{
    static $pdo = null;

    if ($pdo instanceof PDO) {
        return $pdo;
    }

    $dsn = 'mysql:host=' . DB_HOST . ';dbname=' . DB_NAME . ';charset=' . DB_CHARSET;

    $options = [
        PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION, // ném Exception khi lỗi
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,       // trả về mảng kết hợp
        PDO::ATTR_EMULATE_PREPARES   => false,                  // dùng prepare thật của MySQL
    ];

    $pdo = new PDO($dsn, DB_USER, DB_PASS, $options);
    return $pdo;
}
```

Kiểm tra kết nối (`test_db.php`):

```php
<?php
require_once __DIR__ . '/config.php';

try {
    db()->query('SELECT 1');
    echo 'Ket noi MySQL thanh cong';
} catch (PDOException $e) {
    echo 'Loi: ' . htmlspecialchars($e->getMessage());
}
```

---

## 6. CRUD với PDO

### 6.1. READ — lấy tất cả / lấy theo id

```php
<?php
require_once __DIR__ . '/config.php';

// Tất cả
$stmt = db()->query('SELECT id, name, description, created_at FROM categories ORDER BY id DESC');
$categories = $stmt->fetchAll();

// Theo id (prepared)
$id = 1;
$stmt = db()->prepare('SELECT * FROM categories WHERE id = ?');
$stmt->execute([$id]);
$category = $stmt->fetch(); // false nếu không có
```

### 6.2. CREATE — thêm mới

```php
<?php
require_once __DIR__ . '/config.php';

$name = 'Tai nghe';
$description = 'Tai nghe co day / khong day';

$sql = 'INSERT INTO categories (name, description) VALUES (:name, :description)';
$stmt = db()->prepare($sql);
$stmt->execute([
    ':name' => $name,
    ':description' => $description,
]);

$newId = (int) db()->lastInsertId();
echo "Da them category id = {$newId}";
```

### 6.3. UPDATE — sửa

```php
<?php
require_once __DIR__ . '/config.php';

$id = 1;
$name = 'Laptop gaming';
$description = 'Cap nhat mo ta';

$sql = 'UPDATE categories SET name = :name, description = :description WHERE id = :id';
$stmt = db()->prepare($sql);
$stmt->execute([
    ':name' => $name,
    ':description' => $description,
    ':id' => $id,
]);

echo 'So dong anh huong: ' . $stmt->rowCount();
```

### 6.4. DELETE — xóa

```php
<?php
require_once __DIR__ . '/config.php';

$id = 3;
$stmt = db()->prepare('DELETE FROM categories WHERE id = ?');
$stmt->execute([$id]);

echo 'Da xoa, rowCount = ' . $stmt->rowCount();
```

---

## 7. Trang quản lý Categories đơn giản (1 file)

`categories.php` — minh họa luồng CRUD thuần trước khi tách MVC (Buổi 5):

```php
<?php
declare(strict_types=1);
require_once __DIR__ . '/config.php';

$pdo = db();
$message = '';

// CREATE
if ($_SERVER['REQUEST_METHOD'] === 'POST' && ($_POST['action'] ?? '') === 'create') {
    $name = trim($_POST['name'] ?? '');
    $description = trim($_POST['description'] ?? '');
    if ($name !== '') {
        $stmt = $pdo->prepare('INSERT INTO categories (name, description) VALUES (?, ?)');
        $stmt->execute([$name, $description]);
        $message = 'Them thanh cong';
    }
}

// DELETE
if (isset($_GET['delete'])) {
    $id = (int) $_GET['delete'];
    $stmt = $pdo->prepare('DELETE FROM categories WHERE id = ?');
    $stmt->execute([$id]);
    header('Location: categories.php');
    exit;
}

// READ
$categories = $pdo->query('SELECT * FROM categories ORDER BY id DESC')->fetchAll();

function h(string $s): string
{
    return htmlspecialchars($s, ENT_QUOTES, 'UTF-8');
}
?>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Categories CRUD</title>
</head>
<body>
    <h1>Quan ly Categories</h1>
    <?php if ($message): ?><p><?= h($message) ?></p><?php endif; ?>

    <h2>Them moi</h2>
    <form method="post">
        <input type="hidden" name="action" value="create">
        <input name="name" placeholder="Ten danh muc" required>
        <input name="description" placeholder="Mo ta">
        <button type="submit">Them</button>
    </form>

    <h2>Danh sach</h2>
    <table border="1" cellpadding="8">
        <tr><th>ID</th><th>Name</th><th>Description</th><th>Thao tac</th></tr>
        <?php foreach ($categories as $row): ?>
            <tr>
                <td><?= (int) $row['id'] ?></td>
                <td><?= h($row['name']) ?></td>
                <td><?= h((string) $row['description']) ?></td>
                <td>
                    <a href="edit.php?id=<?= (int) $row['id'] ?>">Sua</a> |
                    <a href="?delete=<?= (int) $row['id'] ?>"
                       onclick="return confirm('Xoa?')">Xoa</a>
                </td>
            </tr>
        <?php endforeach; ?>
    </table>
</body>
</html>
```

`edit.php` (Update):

```php
<?php
declare(strict_types=1);
require_once __DIR__ . '/config.php';

$id = (int) ($_GET['id'] ?? 0);
$stmt = db()->prepare('SELECT * FROM categories WHERE id = ?');
$stmt->execute([$id]);
$category = $stmt->fetch();

if (!$category) {
    exit('Khong tim thay');
}

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $name = trim($_POST['name'] ?? '');
    $description = trim($_POST['description'] ?? '');
    $upd = db()->prepare('UPDATE categories SET name = ?, description = ? WHERE id = ?');
    $upd->execute([$name, $description, $id]);
    header('Location: categories.php');
    exit;
}

function h(string $s): string
{
    return htmlspecialchars($s, ENT_QUOTES, 'UTF-8');
}
?>
<!DOCTYPE html>
<html lang="vi">
<head><meta charset="UTF-8"><title>Sua Category</title></head>
<body>
    <h1>Sua #<?= (int) $category['id'] ?></h1>
    <form method="post">
        <label>Name <input name="name" value="<?= h($category['name']) ?>" required></label>
        <label>Description <input name="description" value="<?= h((string) $category['description']) ?>"></label>
        <button>Cap nhat</button>
    </form>
    <p><a href="categories.php">Quay lai</a></p>
</body>
</html>
```

---

## 8. Sơ đồ luồng CRUD thuần

```
[Browser Form]
      │ POST name, description
      ▼
[categories.php]
      │ prepare + execute INSERT
      ▼
[MySQL: categories]
      │ SELECT *
      ▼
[HTML Table] ──► Browser
```

---

## 9. Lỗi thường gặp

| Lỗi | Nguyên nhân | Cách xử |
|-----|-------------|---------|
| `Access denied` | Sai user/pass | Kiểm tra `config.php` |
| `Unknown database` | Chưa tạo DB | Tạo `cse485_ebook` |
| `SQLSTATE[HY093]` | Sai tên placeholder | Khớp `:name` với mảng execute |
| Tiếng Việt lỗi | Charset sai | Dùng `utf8mb4` |
| Xóa nhầm | Không confirm | Thêm `confirm()` + (sau này) soft delete |

---

## 10. Tóm tắt & Checklist

```
MySQL lưu bền vững
PDO kết nối + ERRMODE_EXCEPTION
CRUD = prepare → execute → fetch/fetchAll
Luôn dùng ? hoặc :named — không nối chuỗi SQL
```

- [ ] Tạo DB + bảng `categories`
- [ ] Kết nối PDO thành công
- [ ] Thêm / xem / sửa / xóa được ít nhất 1 danh mục
- [ ] Hiểu vì sao Prepared Statement chống SQL Injection

## 11. Bài tập trên lớp & về nhà (chuỗi MiniShop)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp** | DB `minishop_cse485`, seed 3 DM, list + form thêm | [Phiếu 04 — mục 2](./worksheets/phieu-04-pdo-categories.md) |
| **Về nhà** | CRUD đủ + bắt trùng tên UNIQUE | [Phiếu 04 — mục 3](./worksheets/phieu-04-pdo-categories.md) |

**Cầu nối Buổi 5:** Tách CRUD categories sang **MVC mini** (cùng DB).

→ [Buổi 5](./05-buoi-05-mvc-mini.md) · [Phiếu 05](./worksheets/phieu-05-mvc-mini.md)

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 4/12*
