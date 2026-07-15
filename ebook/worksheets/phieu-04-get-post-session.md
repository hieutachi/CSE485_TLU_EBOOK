# Phiếu học tập 04 — GET/POST & Session

> **Gắn với:** Buổi 3 (Form + Session)  
> **Repo gợi ý:** `cse485-phieu-04-login-session`

---

## 1. Tóm tắt lý thuyết

- GET: dữ liệu trên URL; POST: trong body — dùng POST cho đăng nhập.
- `$_GET`, `$_POST`, `$_SERVER['REQUEST_METHOD']`.
- Session: `session_start()`; lưu `$_SESSION`; hủy khi logout.
- Escape output bằng `htmlspecialchars` chống XSS cơ bản.

---

## 2. Bài tập thực hành

### Yêu cầu

Xây **đăng nhập ảo** 3 file: `login.php`, `dashboard.php`, `logout.php`.

**Tài khoản cứng:**

| Username | Password |
|----------|----------|
| `sv001`  | `cntt@2025` |

### Luồng

1. Form POST trên `login.php`.
2. Đúng → `$_SESSION['user'] = 'sv001'` → redirect `dashboard.php`.
3. Sai → hiện lỗi tiếng Việt.
4. Vào `dashboard.php` khi chưa login → redirect về `login.php`.
5. `logout.php` hủy session → về login.

### Input / Output

| Input | Output |
|-------|--------|
| `sv001` / `cntt@2025` | Redirect dashboard, chữ `Xin chao, sv001` |
| `sv001` / `sai` | Ở lại login, thông báo sai |
| Vào dashboard (chưa login) | Redirect login |
| Bấm logout | Mất session, về login |

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 04                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG đủ 3 file login/dashboard/logout       ║
║  2. Video OBS + camera mặt: demo ĐÚNG, SAI, chặn chưa login, ║
║     logout — thuyết trình Session lưu gì?                    ║
║  3. Nộp link Repo + Google Drive                             ║
╚══════════════════════════════════════════════════════════════╝
```
