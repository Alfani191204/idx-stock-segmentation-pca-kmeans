# 📈 Segmentasi Saham BEI Menggunakan PCA dan K-Means Clustering

> Mengelompokkan 855 saham aktif Bursa Efek Indonesia ke dalam 5 segmen bermakna berdasarkan 12 indikator fundamental dan performa pasar, menggunakan kombinasi PCA dan K-Means Clustering.

---

## 📊 Hasil Utama

| Klaster | Label | Jumlah Saham | Proporsi |
|---------|-------|-------------|----------|
| 1 | 🔵 Value/Mature | 546 | 63,86% |
| 0 | 🟠 Distress Ringan | 128 | 14,97% |
| 2 | 🟢 Growth Likuid | 114 | 13,33% |
| 4 | 🔴 Distress Berat | 54 | 6,32% |
| 3 | 🟣 Spekulatif Premium | 50 | 5,85% |

**PCA 5 komponen** → menjelaskan **84,35%** total varians data  
**K-Means (k=5)** → unggul vs Agglomerative & DBSCAN pada 3 metrik evaluasi

---

## 🗂️ Struktur Repositori

```
├── Analisis_Saham_IHSG.ipynb                         # Pipeline utama end-to-end
├── Data Saham IHSG.csv                               # Data mentah 892 saham dari Stockbit Screener
├── Segmentasi_Saham_BEI_..._PCA_dan_K-Means.pdf     # Paper publikasi (IJCCS)
└── README.md
```

---

## ⚙️ Pipeline Analisis

```
Raw Data (892 saham)
      │
      ▼
[PREPROCESSING]
  - Deduplikasi berdasarkan kolom Symbol
  - Cleaning tipe data (Market Cap, rasio keuangan)
  - Drop kolom missing value tinggi: Dividend Yield (59,89%), Dividend Payout Ratio (39,14%)
  - Drop Inventory Turnover (bias sektoral — perbankan & jasa)
  - Eliminasi saham zombie (NPM & ROE keduanya NaN)
  - Imputasi KNN (n_neighbors=5)
      │
      ▼
[OUTLIER & TRANSFORMASI]
  - Winsorization 5% pada seluruh variabel rasio
  - Log-transform pada Market Cap (distribusi sangat skewed)
      │
      ▼
[STANDARISASI]
  - RobustScaler (median + IQR) — lebih tahan outlier vs StandardScaler
      │
      ▼
[UJI KELAYAKAN DATA]
  - Bartlett's Test: χ² = 3.807,62, p = 0,0000 ✓
  - KMO Score: 0,6361 (mediocre, layak lanjut)
      │
      ▼
[REDUKSI DIMENSI — PCA]
  - 12 fitur → 5 komponen utama
  - Explained variance kumulatif: 84,35%
  - Dipilih berdasarkan kriteria ≥80% variance (parsimoni > Kaiser criterion)
      │
      ▼
[PENENTUAN KLASTER OPTIMAL]
  - Elbow Method sebagai screening awal
  - Evaluasi: Silhouette Score ↑, Davies-Bouldin Index ↓, Calinski-Harabasz Score ↑
  - k=5 dipilih: balance terbaik antara kualitas klaster & interpretabilitas
      │
      ▼
[KOMPARASI ALGORITMA]
  - K-Means vs Agglomerative (Ward linkage) vs DBSCAN
  - K-Means k=5 menang di semua metrik vs Agglomerative
  - DBSCAN dikecualikan: 7,74% noise, hanya 3 klaster bermakna, CH Score rendah
      │
      ▼
[VISUALISASI & INTERPRETASI]
  - Scatter Plot PCA (PC1 vs PC2, PC1 vs PC3, PC2 vs PC3)
  - Heatmap profil klaster
  - Radar Chart karakteristik per klaster
  - t-SNE Visualization
```

---

## 📐 Variabel Penelitian (12 Fitur Final)

| Kategori | Variabel |
|----------|----------|
| Performa Pasar | Market Capitalization (log-transformed) |
| Valuasi | Current PE Ratio, EV/EBITDA, Price to Book Value |
| Profitabilitas | Gross Profit Margin, Net Profit Margin, ROA, ROE |
| Aktivitas | Asset Turnover |
| Likuiditas | Current Ratio, Quick Ratio |
| Solvabilitas | Debt to Equity Ratio |

> Dividend Yield, Dividend Payout Ratio, dan Inventory Turnover dieliminasi pada tahap preprocessing.

---

## 🏷️ Profil 5 Klaster

| Klaster | Karakteristik Utama |
|---------|---------------------|
| **Value/Mature** | Emiten mapan, profitabilitas stabil, Asset Turnover tertinggi (0,80), Market Cap besar, DER rendah (0,57) |
| **Distress Ringan** | NPM/ROA/ROE negatif moderat, likuiditas masih memadai (CR 2,60) — berpotensi pulih |
| **Growth Likuid** | Likuiditas sangat tinggi (CR 8,78, QR 6,29), DER terendah (0,26), profitabilitas positif |
| **Spekulatif Premium** | Valuasi ekstrem (PE 99,14, EV/EBITDA 76,83, PBV 4,02), Market Cap terbesar — pasar ekspektasikan pertumbuhan tinggi |
| **Distress Berat** | Profitabilitas negatif, DER tertinggi (1,06), Market Cap terkecil — risiko finansial paling tinggi |

---

## 📏 Komparasi Algoritma Clustering (k=5)

| Metode | Silhouette ↑ | Davies-Bouldin ↓ | Calinski-Harabasz ↑ |
|--------|-------------|-----------------|---------------------|
| **K-Means** | **0,3369** | **1,1417** | **300,53** |
| Agglomerative | 0,3282 | 1,3846 | 256,22 |
| DBSCAN | 0,4399* | 1,9414 | 87,23 |

*DBSCAN Silhouette tinggi tapi menghasilkan 7,74% noise dan CH Score sangat rendah — tidak sesuai untuk segmentasi komprehensif.

---

## 🛠️ Teknologi

| Kategori | Library |
|----------|---------|
| Data Processing | `pandas`, `numpy` |
| Statistik | `scipy`, `factor_analyzer` |
| Machine Learning | `scikit-learn` (PCA, KMeans, AgglomerativeClustering, DBSCAN, KNNImputer) |
| Visualisasi | `matplotlib`, `seaborn` |
| Dimensionality Reduction | `sklearn.manifold.TSNE` |
| Environment | Google Colab |

---

## 🚀 Cara Menjalankan

1. Buka notebook di Google Colab dan upload `Data Saham IHSG.csv`

2. Install dependensi tambahan:
   ```bash
   pip install factor_analyzer
   ```

3. Jalankan cell secara berurutan — semua library lainnya sudah tersedia di Colab by default.

> **Catatan:** Data bersumber dari **Stockbit Screener** per Juni 2026 (892 emiten aktif BEI). Hasil segmentasi bersifat cross-sectional — tidak mencerminkan perubahan kondisi fundamental secara temporal.

---

## 📋 Keterbatasan

- Data cross-sectional (satu titik waktu, Juni 2026) — tidak menangkap dinamika perubahan klaster secara temporal
- Survivorship bias — hanya mencakup saham aktif, bukan seluruh emiten historis
- Fitur dividen dieksklusi akibat missing value tinggi (>39%)

---

## 📚 Referensi

- Siregar, B. & Yosia, Y. (2024). Implementation of K-Means Clustering Algorithm for the Indonesian Stock Exchange. *Jurnal Sisfotek Global*, 14(1). doi:10.38101/sisfotek.v14i1.10860
- Mulyaningsih, S. & Heikal, J. (2022). K-Means Clustering Using PCA Indonesia Multi-Finance Industry Performance. *Asia Pacific Management and Business Application*, 011(02), 131–142.
- Pranata, K. S., Gunawan, A. A. S., & Gaol, F. L. (2022). Development clustering system IDX company with K-Means and DBSCAN based on fundamental indicator and ESG. *Procedia Computer Science*, 216, 319–327.
- Jollife, I. T. & Cadima, J. (2016). Principal component analysis: A review and recent developments. *Philosophical Transactions of the Royal Society A*, 374(2065).
