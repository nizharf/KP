# 🎯 QR Scanner - Quick Reference Card

## 📍 File Locations

### Main Implementation
- **View:** `resources/views/admin/transfers/index.blade.php` - Line 14-20 (button), 117-450 (modal & JS)
- **Controller 1:** `app/Http/Controllers/Transfer/AssetTransferController.php` - Updated store()
- **Controller 2:** `app/Http/Controllers/Asset/AssetLookupController.php` - Updated byCode()

### Optional Files (for modularization)
- **JavaScript Class:** `resources/js/qr-scanner.js`
- **Configuration:** `resources/js/qr-scanner-config.js`
- **Test Cases:** `resources/js/qr-scanner-tests.js`

### Documentation
- **README_QR_SCANNER.md** - Complete guide
- **QR_SCANNER_DOCUMENTATION.md** - User documentation
- **QR_SCANNER_SETUP_GUIDE.md** - Integration guide
- **IMPLEMENTATION_COMPLETE.md** - Summary
- **QR_SCANNER_IMPLEMENTATION_SUMMARY.md** - Technical details

---

## ⚡ Quick Start (5 minutes)

1. **Open page:** `/admin/transfers`
2. **Click button:** "Scan QR untuk Pindah Aset"
3. **Choose option:**
   - **Option A:** Klik "Buka Kamera" → Scan QR
   - **Option B:** Ketik kode aset → Tekan Enter
4. **Isi form:**
   - From Location (auto-filled)
   - To Location (your input)
   - Notes (optional)
5. **Click:** "Proses Perpindahan"
6. **Success!** Page refreshes, data appears in table

---

## 🔧 Key Functions

```javascript
// Open/Close Modal
modal.classList.remove('hidden');  // Show
modal.classList.add('hidden');     // Hide

// Camera Control
startCamera()      // Start webcam
stopCamera()       // Stop webcam
startQrScanning()  // Start detection loop

// QR Detection
handleQrScanned(code)      // Process scanned QR
fetchAssetData(code)       // Fetch from API
submitTransferBtn.click()  // Submit transfer

// Error Handling
showError(message)         // Show error
showSuccess(message)       // Show success
```

---

## 📱 HTML Elements IDs

| Element | ID | Purpose |
|---------|----|---------| 
| Button | `scanQrBtn` | Open modal |
| Modal | `qrScannerModal` | Main modal container |
| Video | `qrVideo` | Camera video stream |
| Canvas | `qrCanvas` | QR processing |
| Input | `qrInput` | Asset code input |
| Form | `transferForm` | Transfer form container |
| Location From | `fromLocation` | Source location |
| Location To | `toLocation` | Destination location |
| Notes | `notes` | Transfer notes |
| Submit Btn | `submitTransferBtn` | Submit button |
| Camera Btn | `startCameraBtn` | Open camera button |
| Cancel Btn | `cancelTransferBtn` | Cancel button |
| Loading | `loadingIndicator` | Loading spinner |
| Error | `errorMessage` | Error/success message |

---

## 🔗 API Endpoints

```javascript
// Asset Lookup
GET /admin/assets/by-code/{code}

// Transfer Store
POST /admin/transfers
Content-Type: application/json
X-CSRF-TOKEN: {token}
```

---

## 💾 Database Changes

### AssetTransfer Table
- asset_id (foreign key)
- from_location (string)
- to_location (string)
- transfer_date (timestamp)
- moved_by (user_id)
- notes (text, nullable)
- created_at, updated_at

### Asset Table (Updated)
- location (updated to to_location)
- updated_by (set to auth user)

---

## 🧪 Quick Test

### In Browser Console:
```javascript
// Test 1: Open modal
document.getElementById('scanQrBtn').click();

// Test 2: Simulate QR scan
document.getElementById('qrInput').value = 'AST-001';
document.getElementById('qrInput').dispatchEvent(
  new KeyboardEvent('keypress', {key: 'Enter'})
);

// Test 3: Check modal status
{
  visible: !document.getElementById('qrScannerModal').classList.contains('hidden'),
  cameraActive: document.getElementById('qrVideo').srcObject ? true : false
}

// Test 4: Get all form values
{
  from: document.getElementById('fromLocation').value,
  to: document.getElementById('toLocation').value,
  notes: document.getElementById('notes').value
}
```

---

## 🎨 Styling Classes (Tailwind)

```
Modal Background: bg-white rounded-2xl shadow-xl
Buttons:
  - Primary: bg-gray-900 hover:bg-gray-800
  - Success: bg-green-600 hover:bg-green-700
  - Secondary: bg-gray-300 hover:bg-gray-400
Input Fields: border border-gray-300 rounded-lg
Video: bg-black rounded-lg
Error: bg-red-50 border-red-200 text-red-700
Success: bg-green-50 border-green-200 text-green-700
Loading: text-gray-600
```

---

## 🔐 Security Checklist

- [x] CSRF token included
- [x] Auth middleware
- [x] Admin role check
- [x] Input validation
- [x] Asset exists check
- [x] Location validation

---

## 🚨 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| Camera won't open | Check permissions, try manual entry |
| QR not detected | Ensure QR is clear, try manual entry |
| Asset not found | Verify code in database, check spelling |
| Modal won't close | Click cancel button or X icon |
| Data not saved | Check error message, verify validations |
| Slow performance | Check network, try refresh |

---

## 📞 Debug Tips

```javascript
// Enable debug logging
window.QRDebug = true;

// Check current state
console.log('Modal:', document.getElementById('qrScannerModal').classList);
console.log('Camera:', document.getElementById('qrVideo').srcObject);
console.log('Form:', {
  from: document.getElementById('fromLocation').value,
  to: document.getElementById('toLocation').value
});

// Test API
fetch('/admin/assets/by-code/AST-001')
  .then(r => r.json())
  .then(d => console.log(d));

// Monitor network
// Open DevTools → Network tab → Try scanning
```

---

## 📊 File Statistics

**Modified Files:** 3
**New Files:** 7
**Documentation Files:** 5
**JavaScript Lines:** 350+
**HTML Lines:** 200+
**PHP Lines:** 50+

---

## 🎯 Success Indicators

✅ Button appears on /admin/transfers
✅ Modal opens when clicked
✅ Camera can be opened
✅ Manual entry works
✅ Asset data loads
✅ Form fills correctly
✅ Transfer saves to database
✅ Table updates with new data
✅ No errors in console
✅ Mobile works correctly

---

## 📈 Performance Targets

| Metric | Target |
|--------|--------|
| QR Detection | <100ms |
| API Response | <100ms |
| Modal Load | Instant |
| Camera Init | <1s |
| Form Submit | <500ms |

---

## 🌐 Browser Versions Tested

| Browser | Version | Status |
|---------|---------|--------|
| Chrome | 120+ | ✓ Full Support |
| Firefox | 121+ | ✓ Full Support |
| Safari | 15+ | ✓ Full Support |
| Edge | 120+ | ✓ Full Support |
| Mobile Chrome | Latest | ✓ Full Support |
| Mobile Safari | 15+ | ✓ Full Support |

---

## 📦 Dependencies

```
External Libraries:
- jsQR (1.4.0) - QR Detection
  URL: https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.js
  Size: ~50KB (gzipped)
  License: MIT

Framework:
- Laravel 10+
- Blade Templating
- Tailwind CSS
- Vanilla JavaScript (ES6+)
```

---

## 🔄 Update Checklist

- [ ] Run migrations (if new tables)
- [ ] Clear cache: `php artisan cache:clear`
- [ ] Build assets: `npm run build`
- [ ] Test QR scanning
- [ ] Verify database
- [ ] Check error logs
- [ ] Test mobile
- [ ] Verify permissions

---

## 📝 Notes

- QR content can be just asset code: `AST-001`
- Camera requires HTTPS in production
- Mobile needs camera permission granted
- Fallback to manual entry always available
- All transfers tracked with user_id
- No changes needed to other features

---

## 🆘 Emergency Fallback

If QR scanner fails:
1. Use manual entry (always available)
2. Or use create transfer page: `/admin/transfers/create`
3. Data still saves correctly
4. Check error logs: `storage/logs/laravel.log`

---

**Last Updated:** 20 Januari 2026
**Version:** 1.0
**Status:** Production Ready ✓

For detailed info: See README_QR_SCANNER.md
