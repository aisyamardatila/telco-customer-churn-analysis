# Telco Customer Churn Analysis

Analisis churn pelanggan telekomunikasi memakai **Google Sheets** (cleaning, pivot) dan **Looker Studio** (dashboard). Tujuannya mengetahui segmen pelanggan mana yang paling rentan berhenti berlangganan.

**Hasil singkat:** dari 7.032 pelanggan, **26,6%** churn. Risikonya terkonsentrasi pada pelanggan kontrak bulanan, pembayar electronic check, pengguna fiber optic, dan pelanggan di tahun pertama.

🔗 **Dashboard interaktif:** [link Looker Studio](https://datastudio.google.com/reporting/d340f900-d50c-42ee-bf49-17042af9deba)

Overview
<img width="1875" height="1407" alt="overview" src="https://github.com/user-attachments/assets/495ec9f4-2be9-4f55-abbe-a1f909b1517d" />

Deep Dive
<img width="1875" height="1407" alt="deep-dive" src="https://github.com/user-attachments/assets/8b6c69ae-ac20-4ac2-8081-2fffcd43d60a" />

---

## Temuan dan Rekomendasi

| # | Temuan | Rekomendasi |
|---|---|---|
| 1 | **Kontrak bulanan paling berisiko:** churn 43%, dibanding 11% (1 tahun) dan 3% (2 tahun). | Beri insentif upgrade ke kontrak tahunan pada bulan ke-3 sampai ke-6. |
| 2 | **Churn terpusat di tahun pertama:** 48% (0–12 bln), 29% (13–24), 20% (25–48), 10% (49+). Pelanggan churn rata-rata baru berlangganan 18 bulan, vs 38 bulan pada yang bertahan. | Program onboarding dan follow-up untuk 12 bulan pertama. |
| 3 | **Electronic check churn tertinggi:** 45%, vs 19% (mailed check), 17% (transfer otomatis), 15% (kartu kredit otomatis). | Dorong migrasi ke pembayaran otomatis (cashback atau diskon kecil). |
| 4 | **Fiber optic berisiko tinggi, terutama tanpa kontrak:** churn 41,9% (DSL 19%, tanpa internet 7,4%). Month-to-month + fiber mencapai 54,6%, sedangkan Two year + fiber hanya 7,2%. | Prioritaskan segmen ini untuk survei kepuasan dan penawaran kontrak panjang. |
| 5 | **Layanan pendukung berkaitan dengan churn rendah:** tanpa Online Security 41,8% vs 14,6% dengan Online Security; tanpa Tech Support 41,7% vs 15,2% dengan Tech Support. | Uji coba bundling gratis Online Security atau Tech Support untuk pelanggan fiber baru, lalu ukur dampaknya. |

*Catatan: seluruh temuan bersifat korelasi, bukan bukti sebab-akibat. Faktor-faktor di atas saling terkait dan tidak dikontrol satu sama lain.*

**KPI keseluruhan:** 7.032 pelanggan, 1.869 churn (26,6%), rata-rata Monthly Charges 64,8.

---

## Dataset

[Telco Customer Churn (Kaggle, BlastChar)](https://www.kaggle.com/datasets/blastchar/telco-customer-churn): 7.043 pelanggan, 21 kolom (demografi, layanan, kontrak, tagihan, status churn). Data berupa snapshot tanpa dimensi waktu.

Data files © Original Authors (data contoh IBM). Salinan data disertakan hanya untuk edukasi dan portofolio nonkomersial.

---

## Proses

1. **Struktur sheet:** `raw` (data asli, tidak diubah), `clean` (hasil cleaning), `analysis` (KPI dan pivot).
2. **Cleaning:** 11 baris `TotalCharges` kosong, semuanya pelanggan baru (`tenure = 0`) yang belum ditagih, **dihapus** (0,16% data). Hasilnya 7.043 → **7.032 baris**. Tidak ada ID duplikat.
3. **Kolom turunan:** `Churn_Flag` (0/1), `Senior_Label`, `Tenure_Group`, `Num_Addons` (jumlah add-on 0–6), `Charges_Group`. Contoh rumus: `=IFS(F2<=12,"0-12 bln",F2<=24,"13-24 bln",F2<=48,"25-48 bln",TRUE,"49+ bln")`.
4. **Analisis:** pivot table dengan `AVERAGE` dari `Churn_Flag` per segmen (rata-rata kolom 0/1 sama dengan churn rate), plus `COUNTA` untuk ukuran segmen. Perbandingan churn vs non-churn memakai `AVERAGEIF`.
5. **Dashboard:** Looker Studio dengan calculated field `Churn Rate = AVG(Churn_Flag)`, filter Gender, Contract, Senior Label, dan Internet Service.

**Catatan teknis:** CSV Kaggle memakai titik sebagai desimal. Dengan locale Indonesia, Sheets membaca kolom desimal sebagai teks sehingga rata-rata salah. Solusinya: set locale ke **United States** sebelum impor, lalu verifikasi dengan `COUNT`, `MAX`, dan `AVERAGE`.

---

## Keterbatasan

- Snapshot tanpa tanggal, jadi tren churn dari waktu ke waktu tidak bisa dianalisis.
- Tidak ada data **alasan** churn (harga, kualitas jaringan, layanan pelanggan).
- Analisis deskriptif, belum ada model prediktif. Langkah lanjutan: regresi logistik atau decision tree.

---

## Struktur Repositori

```
├── data/raw/                 (CSV asli Kaggle)
├── data/cleaned/             (CSV hasil cleaning)
├── workbooks/                (workbook Google Sheets dalam .xlsx)
├── dashboard/                (screenshot dan PDF)
└── docs/insights.md
```
