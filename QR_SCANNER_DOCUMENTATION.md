# Dokumentasi QR Scanner untuk Perpindahan Aset

## Overview
Fitur QR Scanner memungkinkan admin untuk dengan cepat memindahkan aset dengan cara menscan QR code atau memasukkan kode aset secara manual.

## Fitur-Fitur

### 1. Scan QR Code
- Buka modal dengan tombol "Scan QR untuk Pindah Aset"
- Klik "Buka Kamera" untuk mengaktifkan webcam
- Arahkan QR code ke depan kamera
- Sistem otomatis akan mendeteksi dan memproses QR code

### 2. Manual Entry
- Jika tidak bisa menggunakan kamera, ketik langsung kode aset di field input
- Tekan Enter untuk memproses

### 3. Form Perpindahan
Setelah aset ditemukan, muncul form dengan field:
- **Dari Lokasi** - Lokasi awal aset (auto-filled dari database)
- **Ke Lokasi** - Lokasi tujuan aset
- **Catatan** - Catatan tambahan (opsional)

### 4. Proses Perpindahan
- Klik tombol "Proses Perpindahan"
- Sistem akan validasi data
- Aset akan dipindahkan dan lokasi diupdate di database
- Halaman akan refresh dan data baru muncul di tabel

## Komponen-Komponen yang Dimodifikasi

### Frontend
**File:** `resources/views/admin/transfers/index.blade.php`
- Modal QR Scanner dengan video stream
- Input field untuk manual entry
- Form perpindahan (dari lokasi, ke lokasi, catatan)
- JavaScript untuk handle QR scanning menggunakan jsQR library
- Camera access dan frame processing

### Backend
**File:** `app/Http/Controllers/Transfer/AssetTransferController.php`
- Updated `store()` method untuk mendukung JSON response
- Deteksi format request (JSON atau HTML)

**File:** `app/Http/Controllers/Asset/AssetLookupController.php`
- Updated response format untuk konsistensi
- Tambahan field aset (category, brand, model)

## Libraries yang Digunakan

1. **jsQR** - JavaScript QR Code Scanner
   - CDN: https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.js
   - Untuk decode QR code dari canvas

2. **MediaDevices API** - Browser API untuk akses kamera

## API Endpoints

### 1. Lookup Asset by Code
```
GET /admin/assets/by-code/{code}
```
**Response:**
```json
{
  "success": true,
  "asset": {
    "id": 1,
    "asset_code": "AST-001",
    "name": "Laptop Dell",
    "location": "Ruang IT",
    "category": "Electronics",
    "brand": "Dell",
    "model": "XPS 13"
  }
}
```

### 2. Store Transfer (dari QR Scanner)
```
POST /admin/transfers
Content-Type: application/json

{
  "asset_code": "AST-001",
  "from_location": "Ruang IT",
  "to_location": "Ruang Kepala",
  "transfer_date": "2026-01-20T10:30:00Z",
  "notes": "Perpindahan rutin"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Perpindahan berhasil dicatat.",
  "redirect": "/admin/transfers"
}
```

## Validasi

### Client-side
- Kode aset harus valid (ditemukan di database)
- Lokasi awal dan tujuan harus diisi
- Lokasi awal dan tujuan tidak boleh sama

### Server-side
- Asset code harus ada di database
- from_location dan to_location harus berbeda
- User harus authenticated dan berstatus admin

## Flow Diagram

```
User klik "Scan QR"
    ↓
Modal terbuka
    ↓
User pilih: Buka Kamera / Manual Entry
    ↓
Input: Scan QR atau Ketik Kode
    ↓
Fetch Asset Data (API: /admin/assets/by-code/{code})
    ↓
Aset ditemukan → Tampilkan Form Perpindahan
    ↓
User isi: Dari Lokasi & Ke Lokasi
    ↓
Klik "Proses Perpindahan"
    ↓
Submit Form (POST: /admin/transfers)
    ↓
Perpindahan tercatat → Refresh halaman
```

## Troubleshooting

### Kamera tidak bisa diakses
- Pastikan browser memiliki permission untuk akses kamera
- Check browser console untuk error messages
- Gunakan HTTPS untuk akses kamera di production

### QR Code tidak terbaca
- Pastikan QR code jelas dan tidak blur
- Cahaya cukup untuk membaca QR code
- Gunakan manual entry sebagai fallback

### Asset tidak ditemukan
- Verifikasi kode aset yang digunakan
- Pastikan aset sudah didaftarkan di sistem
- Check database untuk memastikan data asset ada

## Testing

### Manual Testing Checklist
- [ ] Tombol "Scan QR untuk Pindah Aset" dapat diklik
- [ ] Modal terbuka dengan benar
- [ ] Tombol "Buka Kamera" trigger akses kamera
- [ ] QR scanning bekerja dengan benar
- [ ] Manual entry (ketik kode) bekerja
- [ ] Asset data ter-fetch dengan baik
- [ ] Form perpindahan tampil setelah asset ditemukan
- [ ] Validasi form bekerja (lokasi tidak boleh sama)
- [ ] Perpindahan berhasil disimpan ke database
- [ ] Data baru muncul di tabel setelah refresh
- [ ] Error message tampil dengan benar jika ada error

## Future Enhancements

1. **Batch Transfer** - Scan multiple QR codes sebelum submit
2. **History** - Simpan history scan session
3. **Barcode Support** - Tambah support untuk barcode scanning
4. **Photo Capture** - Ambil foto saat perpindahan untuk dokumentasi
5. **Offline Mode** - Cache data untuk offline scanning
6. **Mobile App** - Dedicated mobile app untuk scanning
