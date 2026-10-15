# PANDUAN LANGKAH DEMI LANGKAH (TUTORIAL KLIK ERPNEXT)
**Pengerjaan Kasus Manufaktur PT Furnitur Kayu Jaya**

Panduan ini disusun secara berurutan (*step-by-step*) agar Anda dapat melakukan transaksi secara mandiri di antarmuka web ERPNext (Desk).

---

## 0. Persiapan Awal (Prasyarat Sistem)

### A. Memastikan Bagan Akun (Chart of Accounts) Terhubung ke Gudang
1. Buka menu navigasi: **Stock > Warehouse** (atau ketik `Warehouse List` di bilah pencarian atas).
2. Buka masing-masing gudang berikut dan pastikan field **Account** telah diset:
   * **Stores - VD**: Akun `1131 - Persediaan Bahan Baku (Raw Material) - VD`
   * **Work In Progress - VD**: Akun `1133 - Persediaan Barang Dalam Proses (WIP) - VD`
   * **Finished Goods - VD**: Akun `1134 - Persediaan Barang Jadi (Finished Goods) - VD`
3. Klik **Save**.

### B. Master Item, Satuan (UOM), dan BOM
1. Buka **Stock > Item**:
   * `RM-KAYU-01`: Nama = *Kayu Jati Olahan*, Default UOM = *Batang*, Default Gudang = *Stores - VD*.
   * `FG-MEJA-01`: Nama = *Meja Kayu Jati*, Default UOM = *Unit*, Default Gudang = *Finished Goods - VD*.
2. Buka **Manufacturing > Bill of Materials (BOM) > Add BOM**:
   * **Item**: `FG-MEJA-01`
   * **Quantity**: `1`
   * **Items Table**: Tambahkan `RM-KAYU-01`, Qty = `2`, Rate = `500.000`.
   * Centang: **Is Active** dan **Is Default**.
   * Klik **Save** lalu klik **Submit**.

---

## SOAL 1: Pengadaan Bahan Baku Utama (Siklus Pembelian)

### Langkah 1.1: Membuat Purchase Order (PO)
1. Buka menu: **Buying > Purchase Order > Add Purchase Order**.
2. Masukkan data:
   * **Supplier**: `PT Kayu Perhutani`
   * **Date / Transaction Date**: `05-10-2026`
   * **Required By Date**: `05-10-2026`
   * **Items Table**:
     * Item Code: `RM-KAYU-01`
     * Quantity: `100`
     * Rate: `500.000`
     * Target Warehouse: `Stores - VD`
     * Amount otomatis menjadi: `Rp 50.000.000`
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit nomor ID PO (contoh: `PUR-ORD-2026-00014`).*

### Langkah 1.2: Membuat Purchase Receipt (Penerimaan Barang)
1. Dari halaman Purchase Order yang baru disubmit, klik tombol **Create** di kanan atas ➔ pilih **Receipt**.
2. Sistem otomatis membuka formulir **Purchase Receipt**:
   * Pastikan **Accepted Warehouse** = `Stores - VD`.
   * Qty = `100`.
   * Nilai = `Rp 50.000.000`.
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit nomor ID Purchase Receipt (contoh: `MAT-PRE-2026-00004`).*
   * *Dampak Akuntansi: Debet 1131 (Bahan Baku) Rp 50.000.000, Kredit 2113 (Belum Ditagih) Rp 50.000.000.*

### Langkah 1.3: Membuat Purchase Invoice (Faktur Pembelian)
1. Dari halaman Purchase Receipt, klik tombol **Create** di kanan atas ➔ pilih **Invoice**.
2. Sistem membuka formulir **Purchase Invoice**:
   * **Credit To**: `2111 - Utang Usaha Pemasok Bahan Baku - VD`.
   * **Payment Terms**: 30 Hari (Jatuh Tempo: 04-11-2026).
   * Nilai Total Tagihan: `Rp 50.000.000`.
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit nomor ID Purchase Invoice (contoh: `ACC-PINV-2026-00010`).*
   * *Dampak Akuntansi: Debet 2113 Rp 50.000.000, Kredit 2111 (Utang Usaha) Rp 50.000.000.*

---

## SOAL 2: Pengeluaran Bahan Baku ke Lantai Produksi (Material Transfer)

### Langkah 2.1: Merilis Work Order (Perintah Kerja)
1. Buka menu: **Manufacturing > Work Order > Add Work Order**.
2. Masukkan data:
   * **Item to Produce**: `FG-MEJA-01`
   * **Qty to Manufacture**: `10`
   * **BOM No**: Pilih BOM aktif (yang menggunakan 2 batang kayu / unit).
   * **WIP Warehouse**: `Work In Progress - VD`
   * **Target Warehouse**: `Finished Goods - VD`
   * Pada tabel *Required Items*, otomatis muncul `RM-KAYU-01` sebesar `20 Batang`.
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit nomor ID Work Order (contoh: `MFG-WO-2026-00002`).*

### Langkah 2.2: Mentransfer Bahan Baku ke Gudang WIP
1. Dari halaman Work Order tersebut, klik tombol **Start** di pojok kanan atas ➔ pilih **Material Transfer**.
2. Sistem otomatis membuka formulir **Stock Entry** dengan tipe **Material Transfer for Manufacture**:
   * **Default Source Warehouse**: `Stores - VD`
   * **Default Target Warehouse**: `Work In Progress - VD`
   * Item: `RM-KAYU-01`, Qty = `20`, Nilai Transfer = `Rp 10.000.000`.
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit nomor ID Stock Entry Transfer (contoh: `MAT-STE-2026-00003`).*
   * *Dampak Akuntansi: Debet 1133 (WIP) Rp 10.000.000, Kredit 1131 (Bahan Baku) Rp 10.000.000.*

---

## SOAL 3: Pencatatan Beban Listrik Pabrik & Upah Buruh Langsung

### Langkah 3.1: Pembayaran Beban Listrik via Bank BCA (Rp 1.500.000)
1. Buka menu: **Accounting > Journal Entry > Add Journal Entry**.
2. Masukkan data:
   * **Entry Type**: `Bank Entry`
   * **Posting Date**: `10-10-2026`
   * **Cheque / Reference No**: `TRF-BCA-PLN-001`
   * **Cheque / Reference Date**: `10-10-2026`
   * **Tabel Accounts**:
     * Baris 1: Account = `5220 - Listrik, Air, & Gas Pabrik - VD`, Debit = `1.500.000`, Credit = `0`, Cost Center = `Main - VD`.
     * Baris 2: Account = `1113 - Bank Operasional - IDR - VD` (Kas/Bank BCA), Debit = `0`, Credit = `1.500.000`.
   * **User Remark**: *Pembayaran beban listrik pabrik via Transfer Bank BCA*.
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit ID Journal Entry (contoh: `ACC-JV-2026-00001`).*

### Langkah 3.2: Pencatatan Alokasi Upah Buruh Langsung (Rp 2.500.000)
1. Buka menu: **Accounting > Journal Entry > Add Journal Entry**.
2. Masukkan data:
   * **Entry Type**: `Journal Entry`
   * **Posting Date**: `10-10-2026`
   * **Tabel Accounts**:
     * Baris 1: Account = `5140 - Upah Buruh Langsung Pabrik - VD`, Debit = `2.500.000`, Credit = `0`, Cost Center = `Main - VD`.
     * Baris 2: Account = `2122 - Utang Gaji Buruh & Staf - VD`, Debit = `0`, Credit = `2.500.000`.
   * **User Remark**: *Alokasi beban upah buruh langsung pabrik*.
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit ID Journal Entry (contoh: `ACC-JV-2026-00002`).*

---

## SOAL 4: Penyelesaian Produksi Barang Jadi (Manufacture Entry)

### Langkah 4.1: Pelaporan Produk Selesai & Alokasi Biaya Tambahan (BOP & Upah)
1. Buka kembali Work Order Anda di **Manufacturing > Work Order** (pilih yang tadi dibuat di Soal 2).
2. Klik tombol **Finish** di pojok kanan atas ➔ pilih **Manufacture**.
3. Sistem otomatis membuat formulir **Stock Entry** dengan tipe **Manufacture**:
   * **Qty to Produce**: `10`
   * Source Warehouse: `Work In Progress - VD` (Bahan Baku keluar 20 batang senilai Rp 10.000.000).
   * Target Warehouse: `Finished Goods - VD` (Barang Jadi masuk 10 unit meja).
4. Gulir ke bawah ke bagian **Additional Costs** (Biaya Tambahan):
   * Tambah Baris 1:
     * Expense Account = `5220 - Listrik, Air, & Gas Pabrik - VD`
     * Description = *Beban Listrik Pabrik*
     * Amount = `1.500.000`
   * Tambah Baris 2:
     * Expense Account = `5140 - Upah Buruh Langsung Pabrik - VD`
     * Description = *Upah Buruh Langsung Pabrik*
     * Amount = `2.500.000`
   * *Total Additional Costs = Rp 4.000.000.*
5. Periksa Total Nilai Barang Jadi:
   * Nilai Bahan = `Rp 10.000.000`
   * Biaya Tambahan = `Rp 4.000.000`
   * **Total Nilai Produk Jadi = Rp 14.000.000** ➔ **HPP = Rp 1.400.000 / unit**.
6. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit ID Stock Entry Manufacture (contoh: `MAT-STE-2026-00004`).*
   * *Dampak Akuntansi: Debet 1134 (Barang Jadi) Rp 14.000.000, Kredit 1133 (WIP) Rp 10.000.000, Kredit Akun Biaya Absorpsi Rp 4.000.000.*

---

## SOAL 5: Penjualan Produk Jadi & Pengiriman Barang (Siklus Penjualan)

### Langkah 5.1: Membuat Sales Order (Pesanan Penjualan)
1. Buka menu: **Selling > Sales Order > Add Sales Order**.
2. Masukkan data:
   * **Customer**: `PT Mebel Sejahtera`
   * **Date**: `15-10-2026`
   * **Delivery Date**: `15-10-2026`
   * **Items Table**:
     * Item Code: `FG-MEJA-01`
     * Quantity: `5`
     * Rate: `2.800.000`
     * Source Warehouse: `Finished Goods - VD`
     * Total Amount = `Rp 14.000.000`
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit nomor ID Sales Order (contoh: `SAL-ORD-2026-00007`).*

### Langkah 5.2: Membuat Delivery Note (Surat Jalan / Pengiriman Fisik)
1. Dari halaman Sales Order, klik tombol **Create** di kanan atas ➔ pilih **Delivery**.
2. Sistem membuka formulir **Delivery Note**:
   * Source Warehouse = `Finished Goods - VD`
   * Qty = `5` unit.
   * Pastikan Expense Account = `5110 - Pemakaian Bahan Baku Utama - VD` (COGS/HPP).
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit nomor ID Delivery Note (contoh: `MAT-DN-2026-00002`).*
   * *Dampak Akuntansi: Debet 5110 (HPP) Rp 7.000.000, Kredit 1134 (Barang Jadi) Rp 7.000.000.*

### Langkah 5.3: Membuat Sales Invoice (Faktur Penjualan)
1. Dari halaman Delivery Note, klik tombol **Create** di kanan atas ➔ pilih **Invoice**.
2. Sistem membuka formulir **Sales Invoice**:
   * **Debit To**: `1121 - Piutang Usaha Lokal - VD`.
   * **Income Account**: `4110 - Penjualan Produk Jadi - Lokal - VD`.
   * **Payment Terms**: 14 Hari.
   * Total Nilai Faktur = `Rp 14.000.000`.
3. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit nomor ID Sales Invoice (contoh: `ACC-SINV-2026-00007`).*
   * *Dampak Akuntansi: Debet 1121 (Piutang) Rp 14.000.000, Kredit 4110 (Penjualan) Rp 14.000.000.*

---

## SOAL 6: Penerimaan Piutang & Pelunasan Utang Usaha (Siklus Kas/Bank)

### Langkah 6.1: Penerimaan Pembayaran dari Pelanggan (Rp 14.000.000)
1. Buka Sales Invoice yang tadi diterbitkan (`ACC-SINV-2026-00007`).
2. Klik tombol **Create** di pojok kanan atas ➔ pilih **Payment**.
3. Sistem membuka formulir **Payment Entry** (Payment Type: *Receive*):
   * **Paid To**: `1113 - Bank Operasional - IDR - VD` (Kas / Bank BCA).
   * **Received Amount**: `14.000.000`.
   * **Cheque / Reference No**: `TRF-BCA-CUST-001`.
   * **Cheque / Reference Date**: `20-10-2026`.
4. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit ID Payment Entry Penerimaan (contoh: `ACC-PAY-2026-00010`).*
   * *Status Sales Invoice otomatis berubah menjadi **Paid**.*

### Langkah 6.2: Pelunasan Utang Pembelian ke Pemasok (Rp 50.000.000)
1. Buka Purchase Invoice pengadaan kayu (`ACC-PINV-2026-00010`).
2. Klik tombol **Create** di pojok kanan atas ➔ pilih **Payment**.
3. Sistem membuka formulir **Payment Entry** (Payment Type: *Pay*):
   * **Paid From**: `1113 - Bank Operasional - IDR - VD` (Kas / Bank BCA).
   * **Paid Amount**: `50.000.000`.
   * **Cheque / Reference No**: `TRF-BCA-SUPP-001`.
   * **Cheque / Reference Date**: `20-10-2026`.
4. Klik **Save** ➔ Klik **Submit** ➔ Klik **Yes**.
   * *Hasil: Terbit ID Payment Entry Pembayaran (contoh: `ACC-PAY-2026-00011`).*
   * *Status Purchase Invoice otomatis berubah menjadi **Paid**.*

---

## 7. Cara Memeriksa Hasil Laporan Akhir

1. **Memeriksa Saldo Akun di Trial Balance**:
   * Buka: **Accounting > Trial Balance**
   * Filter: Company = `Vlara Danita`
   * Periksa 6 akun utama:
     * `1113 / 1120` Kas/Bank BCA: saldo kredit Rp 37.500.000 (bersih mutasi keluar).
     * `1131` Persediaan Bahan Baku: saldo debet Rp 40.000.000 (sisa 80 batang).
     * `1133` Persediaan WIP: Rp 0 (selesai penuh).
     * `1134` Persediaan Barang Jadi: saldo debet Rp 7.000.000 (sisa 5 unit meja).
     * `4110` Pendapatan Penjualan: saldo kredit Rp 14.000.000.
     * `5110` HPP: saldo debet Rp 7.000.000.
2. **Memeriksa Fisik Stok di Stock Balance**:
   * Buka: **Stock > Stock Balance**
   * Filter: Item = `RM-KAYU-01` di `Stores - VD` (Sisa 80 Batang).
   * Filter: Item = `FG-MEJA-01` di `Finished Goods - VD` (Sisa 5 Unit).
