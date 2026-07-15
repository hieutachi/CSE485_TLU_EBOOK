# BUỔI 5: Chuyển đổi mã thuần sang Mô hình MVC (MVC Mini)

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 4 (PDO CRUD categories)  
> **Kết quả đầu ra:** Tái cấu trúc CRUD thành Model / Controller / View riêng  
> **Phiếu học tập liên quan:** Phiếu 6

---

## 1. Mục tiêu buổi học

1. Giải thích được MVC bằng sơ đồ ASCII.
2. Tách SQL vào Model, điều hướng vào Controller, HTML vào View.
3. Có một “router” tối giản (`index.php?controller=&action=`).
4. Nhận ra MVC mini chính là “bản phác thảo” của Laravel.

---

## 2. MVC là gì?

**MVC** = Model – View – Controller: tách **ba trách nhiệm** của ứng dụng web.

```
                    ┌─────────────────────────────────────────┐
                    │              MVC MINI                   │
                    │                                         │
  Request ─────────►│  Controller                             │
  (Browser)         │   - Nhận input                          │
                    │   - Gọi Model                           │
                    │   - Chọn View                           │
                    │         │                               │
                    │         ▼                               │
                    │      Model                              │
                    │   - Nói chuyện với DB (PDO)             │
                    │   - Trả về mảng / object                │
                    │         │                               │
                    │         ▼                               │
                    │       View                              │
                    │   - Chỉ HTML + echo dữ liệu             │
                    │                                         │
  Response ◄────────│                                         │
                    └─────────────────────────────────────────┘
```

| Thành phần | Trách nhiệm | Không được làm |
|------------|-------------|----------------|
| **Model** | CRUD, quy tắc dữ liệu | Echo HTML |
| **Controller** | Điều phối request | Viết SQL dài / HTML dài |
| **View** | Hiển thị | Kết nối PDO trực tiếp |

---

## 3. Luồng request chi tiết (ASCII)

```
1. User mở:  /index.php?controller=category&action=index
2. index.php (front controller) nạp CategoryController
3. CategoryController::index()
       │
       ├─► CategoryModel::all()
       │         └─► PDO SELECT … → $rows
       │
       └─► require views/category/index.php  (nhận $rows)
4. HTML trả về trình duyệt
```

So sánh với Laravel (Buổi 6+):

```
MVC Mini                         Laravel
────────                         ───────
index.php?controller&action  ≈   routes/web.php
CategoryController           ≈   app/Http/Controllers/...
CategoryModel                ≈   app/Models/Category.php (Eloquent)
views/*.php                  ≈   resources/views/*.blade.php
```

---

## 4. Cấu trúc thư mục đề xuất

```
cse485-buoi5/
├── config/
│   └── database.php
├── models/
│   └── CategoryModel.php
├── controllers/
│   └── CategoryController.php
├── views/
│   ├── layout.php
│   └── category/
│       ├── index.php
│       ├── create.php
│       └── edit.php
└── index.php          ← Front Controller
```

---

## 5. Code từng lớp

### 5.1. `config/database.php`

```php
<?php

declare(strict_types=1);

function db(): PDO
{
    static $pdo = null;
    if ($pdo instanceof PDO) {
        return $pdo;
    }

    $pdo = new PDO(
        'mysql:host=127.0.0.1;dbname=cse485_ebook;charset=utf8mb4',
        'root',
        '',
        [
            PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
            PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
            PDO::ATTR_EMULATE_PREPARES => false,
        ]
    );

    return $pdo;
}
```

### 5.2. Model — `models/CategoryModel.php`

```php
<?php

declare(strict_types=1);

require_once __DIR__ . '/../config/database.php';

class CategoryModel
{
    public function all(): array
    {
        $stmt = db()->query('SELECT * FROM categories ORDER BY id DESC');
        return $stmt->fetchAll();
    }

    public function find(int $id): ?array
    {
        $stmt = db()->prepare('SELECT * FROM categories WHERE id = ?');
        $stmt->execute([$id]);
        $row = $stmt->fetch();
        return $row ?: null;
    }

    public function create(string $name, ?string $description): int
    {
        $stmt = db()->prepare('INSERT INTO categories (name, description) VALUES (?, ?)');
        $stmt->execute([$name, $description]);
        return (int) db()->lastInsertId();
    }

    public function update(int $id, string $name, ?string $description): bool
    {
        $stmt = db()->prepare('UPDATE categories SET name = ?, description = ? WHERE id = ?');
        return $stmt->execute([$name, $description, $id]);
    }

    public function delete(int $id): bool
    {
        $stmt = db()->prepare('DELETE FROM categories WHERE id = ?');
        return $stmt->execute([$id]);
    }
}
```

### 5.3. Controller — `controllers/CategoryController.php`

```php
<?php

declare(strict_types=1);

require_once __DIR__ . '/../models/CategoryModel.php';

class CategoryController
{
    private CategoryModel $model;

    public function __construct()
    {
        $this->model = new CategoryModel();
    }

    public function index(): void
    {
        $categories = $this->model->all();
        require __DIR__ . '/../views/category/index.php';
    }

    public function create(): void
    {
        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $name = trim($_POST['name'] ?? '');
            $description = trim($_POST['description'] ?? '');
            if ($name !== '') {
                $this->model->create($name, $description !== '' ? $description : null);
            }
            header('Location: index.php?controller=category&action=index');
            exit;
        }
        require __DIR__ . '/../views/category/create.php';
    }

    public function edit(): void
    {
        $id = (int) ($_GET['id'] ?? 0);
        $category = $this->model->find($id);
        if (!$category) {
            http_response_code(404);
            echo 'Not found';
            return;
        }

        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $name = trim($_POST['name'] ?? '');
            $description = trim($_POST['description'] ?? '');
            $this->model->update($id, $name, $description !== '' ? $description : null);
            header('Location: index.php?controller=category&action=index');
            exit;
        }

        require __DIR__ . '/../views/category/edit.php';
    }

    public function delete(): void
    {
        $id = (int) ($_GET['id'] ?? 0);
        $this->model->delete($id);
        header('Location: index.php?controller=category&action=index');
        exit;
    }
}
```

### 5.4. Front Controller — `index.php`

```php
<?php

declare(strict_types=1);

$controllerName = $_GET['controller'] ?? 'category';
$action = $_GET['action'] ?? 'index';

// Whitelist — tránh gọi class/hàm tùy ý từ URL
$map = [
    'category' => 'CategoryController',
];

if (!isset($map[$controllerName])) {
    http_response_code(404);
    exit('Controller not found');
}

require_once __DIR__ . '/controllers/' . $map[$controllerName] . '.php';

$controller = new $map[$controllerName]();

if (!method_exists($controller, $action)) {
    http_response_code(404);
    exit('Action not found');
}

$controller->{$action}();
```

### 5.5. View — `views/category/index.php`

```php
<?php
/** @var array $categories */
function h(string $s): string
{
    return htmlspecialchars($s, ENT_QUOTES, 'UTF-8');
}
?>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Categories</title>
</head>
<body>
    <h1>Danh muc</h1>
    <p><a href="index.php?controller=category&action=create">+ Them moi</a></p>
    <table border="1" cellpadding="8">
        <tr><th>ID</th><th>Name</th><th>Mo ta</th><th></th></tr>
        <?php foreach ($categories as $row): ?>
            <tr>
                <td><?= (int) $row['id'] ?></td>
                <td><?= h($row['name']) ?></td>
                <td><?= h((string) ($row['description'] ?? '')) ?></td>
                <td>
                    <a href="index.php?controller=category&action=edit&id=<?= (int) $row['id'] ?>">Sua</a>
                    |
                    <a href="index.php?controller=category&action=delete&id=<?= (int) $row['id'] ?>"
                       onclick="return confirm('Xoa?')">Xoa</a>
                </td>
            </tr>
        <?php endforeach; ?>
    </table>
</body>
</html>
```

`views/category/create.php`:

```php
<!DOCTYPE html>
<html lang="vi">
<head><meta charset="UTF-8"><title>Them Category</title></head>
<body>
    <h1>Them danh muc</h1>
    <form method="post">
        <input name="name" required placeholder="Ten">
        <input name="description" placeholder="Mo ta">
        <button>Luu</button>
    </form>
    <p><a href="index.php?controller=category&action=index">Quay lai</a></p>
</body>
</html>
```

`views/category/edit.php`:

```php
<?php
/** @var array $category */
function h(string $s): string { return htmlspecialchars($s, ENT_QUOTES, 'UTF-8'); }
?>
<!DOCTYPE html>
<html lang="vi">
<head><meta charset="UTF-8"><title>Sua Category</title></head>
<body>
    <h1>Sua #<?= (int) $category['id'] ?></h1>
    <form method="post">
        <input name="name" value="<?= h($category['name']) ?>" required>
        <input name="description" value="<?= h((string) ($category['description'] ?? '')) ?>">
        <button>Cap nhat</button>
    </form>
</body>
</html>
```

---

## 6. So sánh “trước / sau”

```
BUỔI 4 (1 file ôm hết)          BUỔI 5 (MVC)
─────────────────────          ─────────────────
categories.php                 Model: SQL
  SQL + HTML + if POST           Controller: điều phối
  khó bảo trì, khó mở rộng       View: HTML
                                 dễ thay UI / dễ test Model
```

---

## 7. Checklist & cầu nối Laravel

- [ ] Giải thích được luồng MVC trên giấy
- [ ] CategoryModel không `echo` HTML
- [ ] View không `new PDO`
- [ ] CRUD chạy qua `index.php?controller=category&action=...`

**Cầu nối Buổi 6:** Laravel làm sẵn routing, Controllers, Blade Views, Eloquent Models — bạn sẽ **map** kiến thức MVC mini sang framework.

---

*CSE485 – Ebook Backend PHP & Laravel | Buổi 5/12*
