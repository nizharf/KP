# Fitur Import Excel - Dokumentasi

## Ringkasan
Fitur import Excel telah ditambahkan ke aplikasi Inventaris untuk memudahkan penambahan data aset secara massal. Fitur ini menggunakan library `maatwebsite/excel` yang sudah terinstall.

## Komponen yang Ditambahkan

### 1. **Import Class** (`app/Imports/AssetsImport.php`)
- Mengimplementasikan `ToModel`, `WithHeadingRow`, dan `WithValidation`
- Memvalidasi setiap baris data sebelum disimpan
- Skip baris kosong secara otomatis
- Skip aset dengan asset_code yang sudah ada (tidak akan duplicate)
- Supports all asset fields: asset_code, name, category, brand, model, serial_number, location, status, condition, purchase_date, purchase_price, notes

### 2. **Template Export Class** (`app/Exports/AssetsTemplateExport.php`)
- Menyediakan template Excel yang siap digunakan
- Include contoh data dan formatting
- Auto-size columns untuk kemudahan

### 3. **Controller Methods** (di `AssetController.php`)
- `importPage()` - Menampilkan halaman form import
- `importStore(Request $request)` - Process file upload dan import data
- `template()` - Download template Excel

### 4. **Views**
- `resources/views/admin/assets/import.blade.php` - Halaman import dengan form dan informasi

### 5. **Routes** (di `routes/admin.php`)
```php
Route::get('/assets/import/page', 'importPage')->name('assets.import');
Route::post('/assets/import', 'importStore')->name('assets.import.store');
Route::get('/assets/template/download', 'template')->name('assets.template');
```

### 6. **UI Update**
- Tombol "Import Excel" ditambahkan di halaman Data Aset (index)

## Cara Menggunakan

### 1. Download Template
- Klik tombol "Import Excel" di halaman Data Aset
- Klik "Download Template" untuk mendapatkan file template
- Template berisi contoh format dan kolom yang diperlukan

### 2. Isi Data
Kolom yang **WAJIB** diisi:
- `asset_code` (kode unik aset)
- `name` (nama aset)

Kolom **OPSIONAL**:
- `category` - Kategori aset
- `brand` - Brand/Merek
- `model` - Model
- `serial_number` - Nomor seri
- `location` - Lokasi
- `status` - Status (available, in_use, maintenance, lost, retired)
- `condition` - Kondisi (good, fair, bad)
- `purchase_date` - Tanggal pembelian (format: YYYY-MM-DD)
- `purchase_price` - Harga pembelian
- `notes` - Catatan

### 3. Upload File
- Pilih file Excel (.xlsx, .xls, atau .csv)
- Maksimal ukuran: 10MB
- Klik tombol "Import Data"

### 4. Verifikasi
- Data akan divalidasi sebelum disimpan
- Jika ada error, akan ditampilkan pesan error
- Jika berhasil, data akan disimpan dan Anda akan di-redirect ke halaman Data Aset

## Format Data

### Status
Pilih salah satu:
- `available` - Tersedia
- `in_use` - Sedang Digunakan
- `maintenance` - Perbaikan
- `lost` - Hilang
- `retired` - Tidak Digunakan

### Condition
Pilih salah satu:
- `good` - Baik
- `fair` - Cukup
- `bad` - Buruk

### Purchase Date
Format: `YYYY-MM-DD` (contoh: `2026-01-20`)

## Validasi & Keamanan

1. **File Validation**
   - Format: xlsx, xls, csv
   - Ukuran maksimal: 10MB

2. **Data Validation**
   - asset_code dan name wajib diisi
   - Status dan condition harus sesuai enum
   - Purchase date harus format valid
   - Purchase price harus numeric

3. **Duplicate Prevention**
   - Asset dengan asset_code yang sama akan di-skip (tidak di-update)
   - Setiap data yang diimport akan otomatis diset:
     - `created_by` = User yang melakukan import
     - `updated_by` = User yang melakukan import
     - `created_at` & `updated_at` = Waktu saat diimport

## Error Handling

Jika terjadi error, pesan akan ditampilkan dengan detail:
- File tidak sesuai format
- File terlalu besar
- Validasi data gagal
- Data di Excel tidak sesuai format

## Contoh Data Excel

| asset_code | name | category | brand | model | serial_number | location | status | condition | purchase_date | purchase_price | notes |
|---|---|---|---|---|---|---|---|---|---|---|---|
| AST001 | Laptop Dell XPS 13 | Hardware | Dell | XPS 13 | SN123456789 | Ruang IT | available | good | 2025-06-15 | 15000000 | Kondisi sangat baik |
| AST002 | Monitor LG 24 inch | Hardware | LG | 24UP550 | SN987654321 | Ruang IT | available | good | 2025-07-20 | 3500000 | Monitor IPS |

## Tips

1. Gunakan template yang sudah disediakan untuk memastikan format benar
2. Pastikan asset_code unik dan belum ada di database
3. Gunakan format tanggal YYYY-MM-DD untuk purchase_date
4. Jangan gunakan karakter khusus di asset_code
5. Jika akan import data besar, bisa split menjadi beberapa file kecil
6. Selalu backup database sebelum import data besar
