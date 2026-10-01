# OUONA Studio — toko dan panel pengelola

Toko dan admin berjalan bersama melalui server Node.js kecil (Node 20+; tanpa paket npm). Server menyimpan katalog dan pesanan di `outputs/data/store.json`, serta gambar yang diunggah di `outputs/uploads/`.

## Jalankan secara lokal

Buka PowerShell pada folder `outputs`, lalu jalankan:

```powershell
node .\server.mjs
```

Buka `http://127.0.0.1:3000`. Untuk membuat admin pertama, kunjungi `/admin.html`. Kunci setup lokal adalah `ouona-local-first-setup`; lalu buat nama admin dan kata sandi unik minimal 12 karakter. Untuk server yang dapat diakses publik, atur `ADMIN_SETUP_KEY` yang panjang dan acak, `NODE_ENV=production`, dan `HOST=0.0.0.0` pada konfigurasi host. Setup admin pertama akan tertutup sesudah akun dibuat.

Login admin memakai kata sandi hash, cookie sesi HttpOnly, token CSRF, dan pembatasan percobaan login sederhana. Sesi disimpan di memori server dan berakhir setelah 8 jam.

## Aktifkan checkout Midtrans

Buat akun merchant Midtrans dan mulai dengan kredensial sandbox. Server Key hanya disimpan sebagai variabel lingkungan di server; jangan menaruhnya pada kode browser.

```powershell
$env:MIDTRANS_SERVER_KEY = 'your-midtrans-sandbox-server-key'
$env:MIDTRANS_MODE = 'sandbox'
$env:PUBLIC_URL = 'https://domain-toko-anda.example'
node .\server.mjs
```

Pada Midtrans MAP, set Payment Notification URL ke `https://domain-toko-anda.example/api/payments/midtrans-notification`. Server memverifikasi `signature_key` dan nilai transaksi sebelum mengubah status pesanan menjadi lunas. Untuk menerima pembayaran nyata, selesaikan onboarding merchant, gunakan kredensial produksi, set `MIDTRANS_MODE=production`, dan host situs melalui HTTPS. Checkout tidak aktif tanpa Server Key. Midtrans yang meng-host halaman pembayaran; situs tidak mengumpulkan atau menyimpan data kartu.

Ongkos kirim prototipe adalah tarif domestik tetap Rp 35.000 dan gratis mulai Rp 2.500.000. Ubah melalui `SHIPPING_FEE_IDR`. Hubungkan tarif/label ekspedisi dan tetapkan wilayah layanan serta pajak sebelum situs diluncurkan.

## Kelola produk

Admin dapat menambah, mengubah, menyembunyikan, dan menghapus produk; mengatur harga, kategori, variasi, stok, deskripsi, bahan, dan alt text; serta mengunggah foto JPG, PNG, atau WebP hingga 3,5 MB. Katalog dan foto demo harus diganti dengan data asli dan gambar yang hak penggunaannya sudah dipastikan.

## Sebelum peluncuran publik

Gunakan host Node yang mendukung penyimpanan persisten untuk `outputs/data` dan `outputs/uploads`, HTTPS, serta backup. Siapkan email konfirmasi pesanan, tarif ekspedisi, syarat retur/privasi yang sesuai wilayah operasi, dan pemantauan. Kredensial merchant Midtrans dan kunci setup admin harus disimpan pada secret manager milik host. Integrasi pembayaran ini belum siap menerima pembayaran nyata sampai merchant memasang kredensial dan mengonfigurasi URL webhook HTTPS.
