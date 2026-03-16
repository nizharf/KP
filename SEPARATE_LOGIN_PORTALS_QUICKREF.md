# Portal Login Terpisah - Quick Reference

## Akses Cepat

| Portal | URL | Theme | Role |
|--------|-----|-------|------|
| **Login Selection** | `http://localhost/` | - | Semua |
| **Admin Portal** | `http://localhost/admin/login` | 🔴 Merah | Admin Only |
| **User Portal** | `http://localhost/login` | 🔵 Biru | User Only |

## Alur Login

### Admin
1. Buka `/admin/login`
2. Masukkan email & password (harus role = admin)
3. ✅ Redirect ke admin dashboard (`/admin/dashboard`)
4. Logout: Klik tombol logout → redirect ke `/admin/login`

### User
1. Buka `/login`
2. Masukkan email & password (harus role ≠ admin)
3. ✅ Redirect ke user dashboard (`/dashboard`)
4. Logout: Klik tombol logout → redirect ke `/login`

## Fitur Tambahan

- ✅ **Remember Me** - Checkbox untuk stay logged in
- 👁️ **Password Toggle** - Klik eye icon untuk show/hide password
- 🔄 **Portal Switch** - Link untuk switch antar portal login
- ⚠️ **Error Messages** - Pesan error jika role tidak sesuai
- ✨ **Responsive Design** - Mobile-friendly UI

## Error Messages

| Kondisi | Error Message |
|---------|---------------|
| Admin login di user portal | "Akun admin tidak dapat login di portal ini." |
| User login di admin portal | "Hanya admin yang dapat login di portal ini." |
| Email/password salah | "Email atau password salah" |

## Controller Methods

### AdminAuthController
- `create()` - Tampilkan halaman admin login
- `store()` - Proses admin login (dengan validasi role)
- `destroy()` - Logout admin

### UserAuthController
- `create()` - Tampilkan halaman user login
- `store()` - Proses user login (dengan validasi role)
- `destroy()` - Logout user

## Middleware yang Digunakan

- **guest** - Untuk login routes (tidak boleh sudah login)
- **auth** - Untuk dashboard/protected routes
- **is_admin** - Untuk admin-only routes (jika ada di AdminAuthController check)

## Customization

### Ubah Warna Theme
Edit di view file:
- Admin: `resources/views/auth/admin-login.blade.php` - Ubah `bg-red-*` ke warna lain
- User: `resources/views/auth/user-login.blade.php` - Ubah `bg-blue-*` ke warna lain

### Ubah Text
Edit di view file masing-masing:
- "Portal khusus untuk administrator sistem"
- "Login untuk mengakses sistem inventaris"

### Tambah Fitur
- Validasi 2FA: Modifikasi method `store()` di AdminAuthController/UserAuthController
- Social login: Tambahkan di bawah form login
- Rate limiting: Tambahkan middleware ke routes
