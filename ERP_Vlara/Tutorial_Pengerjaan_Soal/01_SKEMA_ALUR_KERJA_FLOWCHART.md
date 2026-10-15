# SKEMA ALUR KERJA (FLOWCHART) PENGERJAAN SOAL ERPNEXT MANUFAKTUR
**Simulasi Kasus: PT Furnitur Kayu Jaya**

Dokumen ini memetakan seluruh arsitektur proses bisnis dari pengadaan bahan baku, proses manufaktur, pencatatan biaya overhead dan tenaga kerja, hingga siklus penjualan dan penyelesaian pembayaran.

---

## 1. Diagram Alur Transaksi End-to-End

```mermaid
flowchart TD
    subgraph S0["0. Master Data Setup"]
        MD1["Akun & CoA Manufaktur\n(1131, 1133, 1134, 5110, dll)"] --> MD2["Master Item & UOM\n(RM-KAYU-01, FG-MEJA-01)"]
        MD2 --> MD3["BOM (Bill of Materials)\n1 Unit FG = 2 Batang RM"]
        MD3 --> MD4["Vendor (PT Kayu Perhutani)\n& Customer (PT Mebel Sejahtera)"]
    end

    subgraph S1["Soal 1: Siklus Pembelian (Procurement)"]
        PO["Purchase Order (PO)\n100 Batang @ Rp 500.000"]
        PO -->|"Barang Datang"| PR["Purchase Receipt (PR)\nMasuk: Stores Warehouse\nDr: 1131 Persediaan | Cr: 2113"]
        PR -->|"Faktur Pemasok"| PINV["Purchase Invoice (PINV)\nDr: 2113 | Cr: 2111 Utang Usaha\n(Termin: 30 Hari)"]
    end

    subgraph S2["Soal 2: Rilis Produksi & Transfer Bahan"]
        WO["Work Order (#WO-2026-001)\nTarget: 10 Unit FG-MEJA-01"]
        WO -->|"Material Transfer"| STE_TRF["Stock Entry (Transfer WIP)\n20 Batang Kayu (Rp 10.000.000)\nStores -> WIP Warehouse\nDr: 1133 WIP | Cr: 1131 Bahan Baku"]
    end

    subgraph S3["Soal 3: Biaya Overhead (BOP) & Upah"]
        JE1["Journal Entry (Listrik Pabrik)\nRp 1.500.000 via Transfer Bank BCA\nDr: 5220 Beban Listrik | Cr: 1113/1120 Bank"]
        JE2["Journal Entry (Upah Buruh)\nRp 2.500.000 Beban Tenaga Kerja\nDr: 5140 Upah Buruh | Cr: 2122 Utang Gaji"]
    end

    subgraph S4["Soal 4: Penyelesaian Produksi (Manufacture)"]
        STE_MFG["Stock Entry (Manufacture)\n10 Unit Meja Selesai\nWIP -> Finished Goods Warehouse\n+ Additional Costs: Rp 4.000.000 (Listrik + Upah)\nTotal Nilai: Rp 14.000.000 (HPP = Rp 1.400.000/unit)\nDr: 1134 FG | Cr: 1133 WIP & Cr: Beban Absorpsi"]
    end

    subgraph S5["Soal 5: Siklus Penjualan (Sales)"]
        SO["Sales Order (SO)\n5 Unit Meja @ Rp 2.800.000"]
        SO -->|"Kirim Barang"| DN["Delivery Note (DN)\nKeluar dari FG Warehouse\nDr: 5110 HPP (Rp 7.000.000) | Cr: 1134 FG"]
        DN -->|"Terbitkan Faktur"| SINV["Sales Invoice (SINV)\nDr: 1121 Piutang (Rp 14.000.000) | Cr: 4110 Penjualan\n(Termin: 14 Hari)"]
    end

    subgraph S6["Soal 6: Siklus Pembayaran (Settlement)"]
        PAY_IN["Payment Entry (Receive)\nTerima Rp 14.000.000 dari Pelanggan\nDr: Bank BCA | Cr: 1121 Piutang"]
        PAY_OUT["Payment Entry (Pay)\nBayar Rp 50.000.000 ke Pemasok\nDr: 2111 Utang Usaha | Cr: Bank BCA"]
    end

    subgraph S7["7. Verifikasi Laporan"]
        TB["Trial Balance & Stock Ledger\n• Bank BCA: (Rp 37.500.000) Cr\n• Bahan Baku: Rp 40.000.000 Dr\n• WIP: Rp 0 (Nihil)\n• Barang Jadi: Rp 7.000.000 Dr\n• Pendapatan: Rp 14.000.000 Cr\n• HPP: Rp 7.000.000 Dr"]
    end

    MD4 --> PO
    PR --> WO
    STE_TRF --> STE_MFG
    JE1 -.->|"Alokasi Rp 1.500.000"| STE_MFG
    JE2 -.->|"Alokasi Rp 2.500.000"| STE_MFG
    STE_MFG --> SO
    SINV --> PAY_IN
    PINV --> PAY_OUT
    PAY_IN --> TB
    PAY_OUT --> TB
```

---

## 2. Matriks Pemetaan Dokumen & Aliran Fisik Gudang

| Tahapan | Dokumen ERPNext | Gudang Asal | Gudang Tujuan | Perubahan Nilai Persediaan |
| :--- | :--- | :---: | :---: | :--- |
| **Pengadaan** | `Purchase Receipt` | Pemasok Luar | `Stores Warehouse` | `+ Rp 50.000.000` (Bahan Baku Masuk) |
| **Rilis Bahan** | `Stock Entry (Transfer)` | `Stores Warehouse` | `Work In Progress` | `Stores: -Rp 10.000.000` \| `WIP: +Rp 10.000.000` |
| **Barang Jadi**| `Stock Entry (Manufacture)`| `Work In Progress` | `Finished Goods` | `WIP: -Rp 10.000.000` \| `FG: +Rp 14.000.000` *(+4jt Biaya Konversi)* |
| **Pengiriman** | `Delivery Note` | `Finished Goods` | Pelanggan Luar | `FG: -Rp 7.000.000` *(Diakui sebagai HPP)* |

---

## 3. Matriks Aliran Jurnal Akuntansi

| No | Transaksi | Akun Debet (Dr) | Akun Kredit (Cr) | Nominal (Rp) |
| :---: | :--- | :--- | :--- | :---: |
| **1a** | Penerimaan Barang Pembelian | `1131 - Persediaan Bahan Baku` | `2113 - Barang Belum Ditagih` | 50.000.000 |
| **1b** | Pengakuan Faktur Pembelian | `2113 - Barang Belum Ditagih` | `2111 - Utang Usaha Pemasok` | 50.000.000 |
| **2** | Pengeluaran Bahan ke WIP | `1133 - Persediaan WIP` | `1131 - Persediaan Bahan Baku` | 10.000.000 |
| **3a** | Bayar Listrik Pabrik | `5220 - Listrik, Air & Gas Pabrik` | `1113/1120 - Bank BCA` | 1.500.000 |
| **3b** | Akrual Upah Buruh Pabrik | `5140 - Upah Buruh Langsung` | `2122 - Utang Gaji Buruh` | 2.500.000 |
| **4** | Penyelesaian Produk Jadi | `1134 - Persediaan Barang Jadi` | `1133 - Persediaan WIP`<br>`5220 - Alokasi Listrik`<br>`5140 - Alokasi Upah` | 14.000.000<br>*(Kredit: 10jt WIP + 1.5jt Listrik + 2.5jt Upah)* |
| **5a** | Pengiriman Barang (HPP) | `5110 - Pemakaian Bahan / HPP` | `1134 - Persediaan Barang Jadi` | 7.000.000 |
| **5b** | Penjualan Produk Jadi | `1121 - Piutang Usaha Lokal` | `4110 - Penjualan Produk Jadi` | 14.000.000 |
| **6a** | Terima Pelunasan Piutang | `1113/1120 - Bank BCA` | `1121 - Piutang Usaha Lokal` | 14.000.000 |
| **6b** | Bayar Pelunasan Utang Pemasok | `2111 - Utang Usaha Pemasok` | `1113/1120 - Bank BCA` | 50.000.000 |
