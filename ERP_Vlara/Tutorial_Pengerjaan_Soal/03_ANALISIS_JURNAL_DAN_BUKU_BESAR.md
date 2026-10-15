# ANALISIS JURNAL, BUKU BESAR, DAN REKONSILIASI KASUS MANUFAKTUR

Dokumen ini menjelaskan logika akuntansi keuangan dan akuntansi biaya (*cost accounting*) yang terjadi di balik setiap transaksi ERPNext yang telah dijalankan.

---

## 1. Analisis Jurnal Akuntansi per Transaksi

### Transaksi 1: Siklus Pembelian Bahan Baku
* **Penerimaan Fisik (Purchase Receipt: `MAT-PRE-2026-00004`)**:
  * `(Dr) 1131 - Persediaan Bahan Baku (Raw Material)`: **Rp 50.000.000**
  * `(Cr) 2113 - Penerimaan Barang Belum Ditagih (Accrued Expense)`: **Rp 50.000.000**
  * *Penjelasan*: Persediaan diakui pada saat risiko dan manfaat barang berpindah secara fisik ke gudang bahan baku (`Stores Warehouse`).

* **Penerimaan Faktur (Purchase Invoice: `ACC-PINV-2026-00010`)**:
  * `(Dr) 2113 - Penerimaan Barang Belum Ditagih`: **Rp 50.000.000**
  * `(Cr) 2111 - Utang Usaha Pemasok Bahan Baku`: **Rp 50.000.000**
  * *Penjelasan*: Akun penampung sementara (`2113`) ditutup/dieliminasi dan beralih menjadi kewajiban utang dagang definitif kepada vendor PT Kayu Perhutani dengan termin kredit 30 hari.

---

### Transaksi 2: Pengeluaran Bahan Baku ke Lantai Produksi
* **Material Transfer for Manufacture (`MAT-STE-2026-00003`)**:
  * `(Dr) 1133 - Persediaan Barang Dalam Proses (WIP)`: **Rp 10.000.000**
  * `(Cr) 1131 - Persediaan Bahan Baku (Raw Material)`: **Rp 10.000.000**
  * *Penjelasan*: Pengeluaran 20 batang kayu jati olahan (@ Rp 500.000) dari gudang bahan baku dipindahkan ke area proses perakitan meja. Tidak ada laba/rugi yang timbul, hanya reklasifikasi kelompok aset lancar.

---

### Transaksi 3: Beban Listrik Pabrik & Alokasi Upah Buruh
* **Pembayaran Listrik Pabrik (`ACC-JV-2026-00001`)**:
  * `(Dr) 5220 - Listrik, Air, & Gas Pabrik (Overhead)`: **Rp 1.500.000**
  * `(Cr) 1113 / 1120 - Kas / Bank BCA`: **Rp 1.500.000**
  * *Penjelasan*: Pengeluaran kas/bank untuk membayar operasional utilitas mesin pabrik.

* **Pengakuan Beban Upah Buruh Langsung (`ACC-JV-2026-00002`)**:
  * `(Dr) 5140 - Upah Buruh Langsung Pabrik`: **Rp 2.500.000**
  * `(Cr) 2122 - Utang Gaji Buruh & Staf`: **Rp 2.500.000**
  * *Penjelasan*: Pengakuan biaya tenaga kerja yang terlibat langsung dalam pembuatan 10 unit meja.

---

### Transaksi 4: Penyelesaian Produk Jadi & Kapitalisasi Biaya Konversi
* **Stock Entry Manufacture (`MAT-STE-2026-00004`)**:
  * `(Dr) 1134 - Persediaan Barang Jadi (Finished Goods)`: **Rp 14.000.000**
  * `(Cr) 1133 - Persediaan Barang Dalam Proses (WIP)`: **Rp 10.000.000**
  * `(Cr) 5220 - Listrik, Air, & Gas Pabrik (Alokasi BOP)`: **Rp 1.500.000**
  * `(Cr) 5140 - Upah Buruh Langsung Pabrik (Alokasi Tenaga Kerja)`: **Rp 2.500.000**
  * *Penjelasan (Absorption Costing)*: Biaya bahan mentah (Rp 10.000.000) beserta biaya overhead dan upah (Rp 4.000.000) dikapitalisasi ke dalam aset persediaan barang jadi.
  * HPP per unit = `Rp 14.000.000 / 10 unit` = **Rp 1.400.000 / unit**.

---

### Transaksi 5: Penjualan & Pengiriman Produk Jadi
* **Pengiriman Barang (Delivery Note: `MAT-DN-2026-00002`)**:
  * `(Dr) 5110 - Pemakaian Bahan Baku Utama (HPP / COGS)`: **Rp 7.000.000**
  * `(Cr) 1134 - Persediaan Barang Jadi (Finished Goods)`: **Rp 7.000.000**
  * *Penjelasan*: 5 unit meja keluar dari gudang Finished Goods. HPP diakui sebesar `5 unit x Rp 1.400.000 = Rp 7.000.000`.

* **Faktur Penjualan (Sales Invoice: `ACC-SINV-2026-00007`)**:
  * `(Dr) 1121 - Piutang Usaha Lokal`: **Rp 14.000.000**
  * `(Cr) 4110 - Penjualan Produk Jadi - Lokal`: **Rp 14.000.000**
  * *Penjelasan*: Pengakuan piutang dan pendapatan sebesar `5 unit x Rp 2.800.000 = Rp 14.000.000`.
  * *Laba Kotor Sementara*: `Rp 14.000.000 - Rp 7.000.000` = **Rp 7.000.000 (Margin 50%)**.

---

### Transaksi 6: Siklus Pembayaran Kas & Bank
* **Penerimaan Piutang Pelanggan (`ACC-PAY-2026-00010`)**:
  * `(Dr) 1113 / 1120 - Kas / Bank BCA`: **Rp 14.000.000**
  * `(Cr) 1121 - Piutang Usaha Lokal`: **Rp 14.000.000**
  * *Penjelasan*: Pelunasan faktur penjualan oleh PT Mebel Sejahtera. Status faktur menjadi **Paid**.

* **Pelunasan Utang ke Pemasok (`ACC-PAY-2026-00011`)**:
  * `(Dr) 2111 - Utang Usaha Pemasok Bahan Baku`: **Rp 50.000.000**
  * `(Cr) 1113 / 1120 - Kas / Bank BCA`: **Rp 50.000.000**
  * *Penjelasan*: Pelunasan faktur pembelian kayu jati ke PT Kayu Perhutani. Status faktur menjadi **Paid**.

---

## 2. Buku Besar T (T-Account Summary) Kasus Ini

```
          1131 Persediaan Bahan Baku                   1133 Persediaan WIP
   -----------------------------------------   -----------------------------------------
   (1) Beli 100 btg  50.000.000 | (2) Pakai 20  10.000.000   (2) Masuk     10.000.000 | (4) Selesai 10  10.000.000
   -----------------------------------------   -----------------------------------------
   Saldo Akhir:      40.000.000 (80 Batang)    Saldo Akhir:               0 (Nihil)

          1134 Persediaan Barang Jadi                 1113 / 1120 Bank BCA
   -----------------------------------------   -----------------------------------------
   (4) Selesai 10    14.000.000 | (5) Jual 5     7.000.000   (6) Piutang   14.000.000 | (3) Listrik      1.500.000
   -----------------------------------------                 | (6) Utang     50.000.000
   Saldo Akhir:       7.000.000 (5 Unit)       -----------------------------------------
                                               Saldo Bersih:            -37.500.000 (Cr)

          4110 Pendapatan Penjualan                    5110 Beban Pokok (HPP)
   -----------------------------------------   -----------------------------------------
                                | (5) Penjualan 14.000.000   (5) HPP Meja   7.000.000 |
   -----------------------------------------   -----------------------------------------
   Saldo Akhir:      14.000.000 (Cr)           Saldo Akhir:   7.000.000 (Dr)
```

---

## 3. Rubrik Penilaian untuk Dosen / Evaluator

1. **Konsistensi Aliran Fisik vs Akuntansi (Bobot 30%)**:
   * Apakah penerimaan fisik (PR) mencatat persediaan di Stores Warehouse? *(Ya, Rp 50jt)*
   * Apakah pengeluaran ke lantai pabrik memindahkan stok dari Stores ke WIP? *(Ya, 20 batang)*
   * Apakah barang jadi masuk ke Finished Goods Warehouse dengan HPP yang benar? *(Ya, Rp 1,4jt/unit)*
2. **Penerapan Biaya Tambahan / Absorption Costing (Bobot 25%)**:
   * Apakah biaya overhead (listrik) dan tenaga kerja (upah) dialokasikan dengan tepat pada Stock Entry Manufacture? *(Ya, total Rp 4jt)*
3. **Ketepatan Pengakuan Pendapatan & HPP (Bobot 25%)**:
   * Apakah Delivery Note mengakui pengurang persediaan barang jadi dan pembentukan HPP? *(Ya, Rp 7jt)*
   * Apakah Sales Invoice mengakui pendapatan dan piutang dengan benar? *(Ya, Rp 14jt)*
4. **Kelengkapan Dokumen & Status Pelunasan (Bobot 20%)**:
   * Seluruh 10 dokumen dari PO, PR, PINV, WO, STE Transfer, STE Manufacture, SO, DN, SINV, hingga Payment Entry berhasil disubmit dan berstatus resmi.
