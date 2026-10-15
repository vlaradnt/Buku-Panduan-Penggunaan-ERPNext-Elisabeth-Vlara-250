# Skenario Praktik Siklus Manufaktur (Manufacturing Cycle) ERPNext

Dokumen ini berisi data transaksi siap pakai untuk latihan modul manufaktur secara end-to-end (dari pembelian bahan baku hingga penjualan produk jadi).

---

## 🏗️ Alur Praktik Manufaktur ERPNext

```
[1. Pembelian Bahan Baku] ──> [2. Masuk Gudang Bahan Baku] ──> [3. Buat BOM Resep Meja]
                                                                        │
[6. Pembayaran Pelanggan] <── [5. Penjualan & Kirim FG]  <── [4. Work Order Produksi]
```

---

## 📋 Skenario 1: Pembelian Bahan Baku (Buying Module)

Pabrik membutuhkan stok bahan baku awal untuk memproduksi 10 unit meja.

### A. Purchase Order 1 (Ke Supplier: PT Rimba Kayu Lestari)
* **Supplier**: `PT Rimba Kayu Lestari`
* **Item**: `RM-KAYU-01` (Papan Kayu Mahoni 120x60)
* **Quantity**: `15 Nos` (untuk cadangan 10 meja + 5 cadangan)
* **Rate**: `Rp 85.000`
* **Target Warehouse**: `Gudang Bahan Baku`
* **Total Transaksi**: `Rp 1.275.000`

### B. Purchase Order 2 (Ke Supplier: CV Logam Baja Perkasa)
* **Supplier**: `CV Logam Baja Perkasa`
* **Item 1**: `RM-BESI-01` (Kaki Meja Besi Hollow)
  * Quantity: `50 Nos`
  * Rate: `Rp 40.000`
  * Subtotal: `Rp 2.000.000`
* **Item 2**: `RM-BAUT-01` (Baut & Sekrup Meja M6)
  * Quantity: `200 Nos`
  * Rate: `Rp 1.500`
  * Subtotal: `Rp 300.000`
* **Target Warehouse**: `Gudang Bahan Baku`
* **Total Transaksi**: `Rp 2.300.000`

### C. Purchase Order 3 (Ke Supplier: Toko Cat Sumber Warna)
* **Supplier**: `Toko Cat Sumber Warna`
* **Item**: `RM-CAT-01` (Cat Finishing Pelitur Kayu)
* **Quantity**: `10 Liter`
* **Rate**: `Rp 60.000`
* **Target Warehouse**: `Gudang Bahan Baku`
* **Total Transaksi**: `Rp 600.000`

---

## 📦 Skenario 2: Penerimaan Bahan Baku & Tagihan Pemasok

Setelah Purchase Order dibuat dan disetujui (*Submit*):
1. **Purchase Receipt (Tanda Terima Barang)**:
   * Klik tombol **Create > Receipt** pada masing-masing Purchase Order.
   * Pastikan barang masuk ke **Gudang Bahan Baku**.
   * Klik **Submit**. *(Stok bahan baku sekarang resmi bertambah di sistem)*.
2. **Purchase Invoice (Faktur Pembelian)**:
   * Klik **Create > Invoice**.
   * Klik **Submit**.
3. **Payment Entry (Pelunasan Pembayaran)**:
   * Klik **Create > Payment**.
   * Pilih akun kas/bank, lalu klik **Submit**.

---

## 🛠️ Skenario 3: Pembuatan Resep Produksi (Bill of Materials - BOM)

Buka modul **Manufacturing > Bill of Materials > Add BOM**.

* **Item**: `FG-MEJA-01` (Meja Kerja Kayu Minimalis)
* **Quantity**: `1`
* **With Operations**: *Opsional (centang jika ingin menambahkan tahap kerja)*
* **Tabel Raw Materials (Items)**:
  1. `RM-KAYU-01` | Qty: `1` | UOM: `Nos` | Rate: `85.000`
  2. `RM-BESI-01` | Qty: `4` | UOM: `Nos` | Rate: `40.000`
  3. `RM-BAUT-01` | Qty: `16` | UOM: `Nos` | Rate: `1.500`
  4. `RM-CAT-01` | Qty: `0.5` | UOM: `Liter` | Rate: `60.000`
* **Total Cost Bahan Baku**: `Rp 299.000`
* Klik **Save** lalu klik **Submit**. *(BOM aktif bertanda Default)*.

---

## ⚙️ Skenario 4: Eksekusi Perintah Kerja (Work Order Produksi)

Pabrik akan memproduksi **5 unit meja**.

1. Buka **Manufacturing > Work Order > Add Work Order**.
2. **Item to Produce**: `FG-MEJA-01`
3. **Qty to Manufacture**: `5`
4. **BOM No**: *(otomatis terisi nomor BOM yang baru dibuat)*
5. **Source Warehouse**: `Gudang Bahan Baku`
6. **WIP Warehouse**: `Gudang Work In Progress (WIP)`
7. **Target Warehouse**: `Gudang Barang Jadi`
8. Klik **Save** lalu **Submit**.

### Tahap Eksekusi Work Order:
* **Langkah 1 (Transfer Bahan ke Ruang Produksi)**:
  * Klik tombol **Start** di Work Order.
  * Sistem akan membuat dokumen *Stock Entry (Material Transfer for Manufacture)*.
  * Periksa jumlah bahan yang keluar (5 Kayu, 20 Kaki Besi, 80 Baut, 2.5 Liter Cat).
  * Klik **Submit**. Status Work Order berubah menjadi **In Process**.
* **Langkah 2 (Penyelesaian Produksi)**:
  * Klik tombol **Finish** di Work Order.
  * Sistem akan membuat dokumen *Stock Entry (Manufacture)*.
  * Produk jadi (5 unit `FG-MEJA-01`) otomatis masuk ke **Gudang Barang Jadi**, dan bahan di WIP berkurang.
  * Klik **Submit**. Status Work Order berubah menjadi **Completed**.

---

## 💼 Skenario 5: Penjualan Produk Jadi (Selling Module)

Pelanggan `CV Sentra Mebel Indonesia` membeli 3 unit Meja Kerja Kayu Minimalis.

1. **Sales Order**:
   * Buka **Selling > Sales Order > Add Sales Order**.
   * Customer: `CV Sentra Mebel Indonesia`
   * Delivery Date: Hari ini
   * Item: `FG-MEJA-01` | Qty: `3` | Rate: `Rp 650.000`
   * Total: `Rp 1.950.000`
   * Klik **Save** lalu **Submit**.
2. **Delivery Note (Pengiriman Barang)**:
   * Pada Sales Order, klik **Create > Delivery**.
   * Source Warehouse: `Gudang Barang Jadi`.
   * Klik **Submit**. *(Stok meja berkurang dari 5 menjadi 2)*.
3. **Sales Invoice (Faktur Penjualan)**:
   * Pada Sales Order / Delivery Note, klik **Create > Invoice**.
   * Klik **Submit**.
4. **Payment Entry (Penerimaan Uang)**:
   * Pada Sales Invoice, klik **Create > Payment**.
   * Masukkan ke akun Kas/Bank, lalu klik **Submit**.

---

## 📊 Skenario 6: Evaluasi Laporan & Analisis

Setelah semua siklus selesai, Anda bisa mengevaluasi hasil praktik melalui menu laporan ERPNext:

1. **Kartu Stok (Stock Ledger)**:
   * Buka menu: `Stock > Stock Ledger`.
   * Lihat mutasi bahan baku masuk, transfer ke WIP, dan barang jadi keluar ke pelanggan.
2. **Laba Rugi (Profit and Loss Statement)**:
   * Buka menu: `Accounting > Profit and Loss Statement`.
   * Lihat Pendapatan Penjualan: `Rp 1.950.000`
   * Harga Pokok Penjualan (HPP / COGS): `3 unit x Rp 299.000 = Rp 897.000`
   * Laba Kotor Penjualan: `Rp 1.053.000`.
