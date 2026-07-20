# Phiếu 05 — MVC Mini cho `categories`

| | |
|---|---|
| **Buổi** | 5 — đọc kèm [Buổi 05](../05-buoi-05-mvc-mini.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-05` |
| **Nhận từ Phiếu 04** | DB `minishop_cse485.categories` + CRUD PDO “một cục” |
| **Mang sang Phiếu 06** | Map tư duy Route → Controller → View sang Laravel |

### Chuẩn đầu ra (CLO)

1. Giải thích được MVC bằng sơ đồ + ví dụ request “Thêm category”.  
2. Cấu trúc thư mục đúng contract; whitelist controller/action.  
3. Model không echo HTML; View không `new PDO`.  
4. CRUD đủ + flash Session một lần sau redirect.

---

## 1. Lý thuyết — vì sao phải tách MVC?

### 1.1. Vấn đề file “ôm hết” (P04)

```
categories.php
  ├── SQL PDO
  ├── if POST → insert
  └── echo <table>…
```

Sửa giao diện dễ đụng SQL; copy sang bảng khác phải nhân đôi cả file.

### 1.2. MVC = ba trách nhiệm

```
Request ──► Controller ──► Model (PDO/SQL) ──► DB
                │
                └──► View (HTML) ──► Response
```

| Thành phần | Được làm | Không được làm |
|------------|----------|----------------|
| **Model** | CRUD, trả mảng | `echo` HTML |
| **Controller** | Đọc input, gọi Model, chọn View, redirect | SQL dài / HTML dài |
| **View** | HTML + escape | `db()` / `new PDO` |

### 1.3. Front Controller

Một cửa: `public/index.php?controller=category&action=index`

**Whitelist** — không `new $_GET['x']` tùy ý:

```php
$map = ['category' => 'CategoryController'];
$actions = ['index', 'create', 'edit', 'delete'];
```

### 1.4. So với Laravel (cầu nối miệng)

| MVC Mini | Laravel |
|----------|---------|
| `index.php?controller&action` | `routes/web.php` |
| `CategoryController` | `app/Http/Controllers/...` |
| `CategoryModel` | `app/Models/Category` (Eloquent) |
| `views/*.php` | Blade |

### 1.5. Flash message

```
POST create OK → $_SESSION['flash']='...' → redirect index → in 1 lần → unset
```

Tránh F5 làm insert lại (PRG: Post/Redirect/Get).

---

## 2. Bài trên lớp (~20') — Khung chạy được Index

### Cấu trúc bắt buộc

```
minishop-05/
├── config/database.php
├── models/CategoryModel.php
├── controllers/CategoryController.php
├── views/category/index.php
├── views/category/create.php
├── views/category/edit.php
└── public/index.php
```

URL mẫu:

```
/public/index.php?controller=category&action=index
/public/index.php?controller=category&action=create
/public/index.php?controller=category&action=edit&id=1
```

### Việc trên lớp

1. Whitelist + nạp controller.  
2. `CategoryModel::all()` + view index từ DB.  
3. Link “Them moi” mở form (nên xong store nếu kịp).

**Checkpoint GV:** Model không HTML; View không `new PDO`.

### Gợi ý chữ ký Model

```text
all(): array
find(int $id): ?array
create(string $name, ?string $description): int
update(int $id, string $name, ?string $description): bool
delete(int $id): bool
```

---

## 3. Bài về nhà (~30') — CRUD + kiến trúc

### A — Đủ action

| Action | HTTP | Model |
|--------|------|-------|
| index | GET | all() |
| create | GET form + POST | create() |
| edit | GET + POST | find(), update() |
| delete | **POST** (khuyến nghị) | delete() |

> Delete bằng GET dễ bị crawl/prefetch xóa nhầm — ưu tiên form POST + confirm.

### B — Flash Session sau create/update/delete

### C — `ARCHITECTURE.md`

ASCII luồng Thêm category + 5–7 dòng so với 1 file P04.

### D — Checklist README (tick)

- [ ] Thêm / Sửa / Xóa được  
- [ ] View không PDO / Model không echo  

---

## 4. Lỗi thường gặp

| Lỗi | Nguyên nhân | Cách xử |
|-----|-------------|---------|
| 404 Controller | Sai whitelist / tên file | Khớp `CategoryController.php` |
| Class not found | Sai đường dẫn require | Dùng `__DIR__` |
| Session flash không hiện | Quên `session_start()` | Đầu front controller |
| Vẫn SQL trong view | Copy thói P04 | Chuyển hết vào Model |

---

## 5. Rubric kiến trúc

| Vi phạm | Hậu quả |
|---------|---------|
| SQL trong View | Trượt kiến trúc |
| HTML trong Model | Trượt kiến trúc |
| Không whitelist | Trừ điểm bảo mật |

---

## 6. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 05                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo cse485-ms-05 đúng cấu trúc thư mục                  ║
║  2. Video CRUD ?controller=&action= + flash                  ║
║  3. ARCHITECTURE.md: MVC giải quyết đau gì so với P04?       ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 06:** Laravel — Route/Controller/View sẵn; map MVC mini sang framework.
