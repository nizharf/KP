# Setup QR Scanner di Views Lain

## Quick Start

Jika ingin menambahkan QR Scanner ke Blade view lain, ikuti langkah berikut:

### Opsi 1: Include Component (Recommended)

Buat component di `app/View/Components/QrScannerModal.php`:

```php
<?php

namespace App\View\Components;

use Illuminate\View\Component;

class QrScannerModal extends Component
{
    public function render()
    {
        return view('components.qr-scanner-modal');
    }
}
```

Kemudian di view mana pun:

```blade
<x-qr-scanner-modal />
```

### Opsi 2: Manual Include

Copy-paste modal HTML dari `resources/views/admin/transfers/index.blade.php` ke view Anda.

### Opsi 3: Use Class-based Scanner

```blade
<!-- Include library dan config -->
<script src="{{ asset('js/qr-scanner-config.js') }}"></script>
<script src="{{ asset('js/qr-scanner.js') }}"></script>

<!-- Di script section Anda -->
<script>
  window.QRScannerConfig = {
    // Custom config
    assetLookupUrl: '/admin/assets/by-code',
    transferStoreUrl: '/admin/transfers',
  };
  
  // Initialize manually
  const scanner = new QRAssetTransferScanner(window.QRScannerConfig);
  scanner.init();
</script>
```

---

## File Structure

```
resources/
├── views/
│   ├── admin/
│   │   └── transfers/
│   │       └── index.blade.php          ← Original implementation
│   └── components/
│       └── qr-scanner-modal.blade.php   ← (Optional) New component
└── js/
    ├── qr-scanner.js                   ← Class-based scanner (optional)
    └── qr-scanner-config.js            ← Configuration (optional)

public/
└── js/
    └── qrcode.min.js                   ← Existing QR library (if using)
```

---

## API Requirements

Pastikan backend sudah support:

### 1. Asset Lookup Endpoint
```
GET /admin/assets/by-code/{code}

Response:
{
  "success": true,
  "asset": {
    "id": 1,
    "asset_code": "AST-001",
    "name": "Laptop",
    "location": "Ruang IT",
    "category": "Electronics",
    "brand": "Dell",
    "model": "XPS"
  }
}
```

### 2. Transfer Store Endpoint
```
POST /admin/transfers
Content-Type: application/json

Request:
{
  "asset_code": "AST-001",
  "from_location": "Ruang IT",
  "to_location": "Ruang Kepala",
  "transfer_date": "2026-01-20T10:30:00Z",
  "notes": "Optional notes"
}

Response:
{
  "success": true,
  "message": "Perpindahan berhasil dicatat.",
  "redirect": "/admin/transfers"
}
```

---

## Customization

### Change Colors

Di view, update Tailwind classes:

```blade
<!-- Primary button color -->
<button class="bg-blue-600">Buka Kamera</button>

<!-- Success button color -->
<button class="bg-emerald-600">Proses</button>

<!-- Error message color -->
<div class="text-amber-700">Error message</div>
```

### Change Text/Labels

Edit string di modal:

```blade
<h2 class="text-xl font-semibold">Custom Title</h2>
<label>Custom Label</label>
<button>Custom Button Text</button>
```

### Add Custom Validation

Modify JavaScript `fetchAssetData()`:

```javascript
function fetchAssetData(assetCode) {
  // Add custom validation
  if (!assetCode.startsWith('AST-')) {
    showError('Kode harus dimulai dengan AST-');
    return;
  }
  
  // Continue with fetch...
}
```

### Add Custom Events

```javascript
// Before fetch
qrInput.addEventListener('input', (e) => {
  console.log('User typing:', e.target.value);
});

// Before submit
submitTransferBtn.addEventListener('click', () => {
  console.log('About to submit transfer');
});

// After successful transfer
// Modify response handling section
```

---

## Testing Locally

### 1. Test Camera Access
```javascript
navigator.mediaDevices.getUserMedia({ video: true })
  .then(stream => console.log('Camera OK'))
  .catch(err => console.log('Camera Error:', err));
```

### 2. Test QR Detection
```javascript
// Use test data
const testData = 'AST-001';
handleQrScanned(testData);
```

### 3. Test API
```bash
# Get asset
curl http://localhost:8000/admin/assets/by-code/AST-001

# Post transfer
curl -X POST http://localhost:8000/admin/transfers \
  -H "Content-Type: application/json" \
  -d '{
    "asset_code": "AST-001",
    "from_location": "Ruang IT",
    "to_location": "Ruang Kepala"
  }'
```

---

## Browser Requirements

- **Modern browser** dengan support:
  - MediaDevices API (Camera)
  - Canvas API (QR processing)
  - Fetch API (AJAX)
  - ES6 JavaScript

- **HTTPS required** untuk production camera access
- **Permissions needed**: Camera access

---

## Security Checklist

- [ ] CSRF token included dalam request
- [ ] Route protected dengan auth middleware
- [ ] User role verified (admin)
- [ ] Input validated server-side
- [ ] Asset exists sebelum transfer
- [ ] Locations validated
- [ ] Audit trail recorded (moved_by)

---

## Performance Tips

1. **Lazy load jsQR library** - Load hanya saat modal dibuka
   ```blade
   <script id="jsqrLib" data-src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.js" defer></script>
   ```

2. **Cache asset lookup** - Avoid redundant API calls
   ```javascript
   const assetCache = {};
   ```

3. **Debounce camera frame processing** - Reduce CPU usage
   ```javascript
   const throttledScan = throttle(scanFrame, 100);
   ```

4. **Minimize bundle** - Use minified version
   ```html
   <script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.min.js"></script>
   ```

---

## Troubleshooting

### Modal tidak muncul?
- Check modal HTML ada di DOM
- Check JavaScript tidak ada error di console
- Verify button ID match: `id="scanQrBtn"`

### Camera tidak bisa diakses?
- HTTPS required di production
- User must grant camera permission
- Check browser console untuk error
- Fallback ke manual entry

### QR tidak terdeteksi?
- QR code harus clear dan visible
- Jarak optimal 20-50cm
- Cahaya cukup terang
- Try manual entry

### API call failed?
- Check endpoint exists
- Verify CORS headers (if cross-origin)
- Check CSRF token included
- Check auth middleware

---

## Version History

- **v1.0** (Jan 20, 2026)
  - Initial release
  - QR camera scanning
  - Manual entry support
  - Asset transfer integration

---

## Support

For issues or questions:
1. Check console untuk error messages
2. Review documentation di `QR_SCANNER_DOCUMENTATION.md`
3. Test dengan browser DevTools
4. Check database untuk audit trail
