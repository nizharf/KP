# Portal Login Terpisah - Dokumentasi Implementasi

## Ringkasan
Sistem inventaris sekarang memiliki dua portal login yang terpisah untuk admin dan user dengan UI yang berbeda.

## URL Login

### Portal Admin
- **URL:** `http://localhost/admin/login`
- **Warna Theme:** Merah (Red)
- **Akses ke:** Admin Dashboard, Kelola Aset, Kelola User
- **Pembatasan:** Hanya user dengan role "admin" dapat login

### Portal User
- **URL:** `http://localhost/login`
- **Warna Theme:** Biru (Blue)
- **Akses ke:** Dashboard User, Lihat Aset
- **Pembatasan:** User dengan role "admin" tidak dapat login

### Landing Page
- **URL:** `http://localhost/` atau `http://localhost/login-selection`
- **Deskripsi:** Halaman untuk memilih portal login (Admin atau User)

## File yang Dibuat/Dimodifikasi

### Controllers (Baru)
1. **`app/Http/Controllers/Auth/AdminAuthController.php`**
   - Controller untuk admin login
   - Verifikasi role admin sebelum authenticate
   - Redirect ke admin dashboard setelah login
   - Logout ke `/admin/login`

2. **`app/Http/Controllers/Auth/UserAuthController.php`**
   - Controller untuk user login
   - Memblokir login user dengan role admin
   - Redirect ke user dashboard setelah login
   - Logout ke `/login`

### Views (Baru)
1. **`resources/views/auth/admin-login.blade.php`**
   - Halaman login untuk admin
   - Styling dengan border merah
   - Link ke user login portal

2. **`resources/views/auth/user-login.blade.php`**
   - Halaman login untuk user
   - Styling dengan border biru
   - Link ke admin login portal

3. **`resources/views/auth/login-selection.blade.php`**
   - Landing page dengan pilihan portal
   - Dua kartu untuk Admin dan User
   - Deskripsi fitur masing-masing portal

### Routes (Dimodifikasi)
1. **`routes/admin.php`**
   - Tambah admin login routes dengan middleware 'guest'
   - Tambah admin logout route

2. **`routes/auth.php`**
   - Update user login routes menggunakan UserAuthController
   - Update logout menggunakan UserAuthController

3. **`routes/web.php`**
   - Ubah root route ke login selection page
   - Include auth.php routes

## Fitur Utama

### Pemisahan Role
- Admin login terpisah dengan validasi role "admin"
- User login dengan pemblokiran akses admin
- Redirect otomatis ke portal yang sesuai

### Keamanan
- Middleware 'guest' untuk login routes (tidak boleh login 2x)
- Validasi role sebelum authenticate
- Session regenerate setelah login
- CSRF protection pada semua form

### User Experience
- Halaman login selection untuk navigasi mudah
- Switch link antar portal login
- Remember me functionality
- Password visibility toggle
- Error dan success messages

## Testing

### Test Admin Login
1. Buka `http://localhost/admin/login`
2. Masukkan email admin dan password
3. Harus login berhasil dan redirect ke admin dashboard

### Test User Login
1. Buka `http://localhost/login`
2. Masukkan email user (bukan admin) dan password
3. Harus login berhasil dan redirect ke user dashboard

### Test Login Selection
1. Buka `http://localhost/` atau `http://localhost/login-selection`
2. Akan melihat dua pilihan portal (Admin dan User)
3. Klik salah satu untuk ke portal login yang dipilih

### Test Pembatasan
- Admin tidak bisa login di user portal (error message)
- User tidak bisa login di admin portal (error message)

## Routes yang Tersedia

```
GET|HEAD        /                                     login.selection
GET|HEAD        /login                                user.login
POST            /login                                user.login.store
POST            /logout                               logout
GET|HEAD        /admin/login                          admin.login
POST            /admin/login                          admin.login.store
POST            /admin/logout                         admin.logout
```

## Catatan

- Halaman login original (`resources/views/auth/login.blade.php`) masih ada untuk backward compatibility
- AuthController original masih ada namun tidak digunakan di routes
- Semua middleware dan validasi sudah terintegrasi dengan Laravel Auth
