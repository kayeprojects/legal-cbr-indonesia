# 📚 project-cbr — Case-Based Reasoning untuk Putusan Pengadilan Indonesia

Sistem akademis Case-Based Reasoning (CBR) untuk analisis dokumen putusan pengadilan Indonesia.

---

## 🎯 Overview Project

Project ini mengimplementasikan pipeline CBR untuk menganalisis dan melakukan penalaran (*reasoning*) terhadap putusan pengadilan Indonesia. Sistem memproses dokumen PDF putusan pengadilan, mengekstrak representasi kasus yang terstruktur, serta memfasilitasi retrieval dan perbandingan kasus.

## 📋 Tahapan Project

| Tahap | Deskripsi | Status |
|-------|-----------|--------|
| **Tahap 1** | Case Base — Data Acquisition & Preprocessing | ✅ Selesai |
| **Tahap 2** | Case Representation — Feature Extraction | ✅ Selesai |
| **Tahap 3** | Case Retrieval — Similarity & Matching | ✅ Selesai |
| **Tahap 4** | Case Reuse & Evaluation | ✅ Selesai |

## 📁 Struktur Project

```text
project-cbr/
│
├── datasets uu-ite/    # PDF putusan pengadilan (input)
│
├── data/
│   ├── raw/            # Hasil extraction teks bersih (.txt)
│   ├── processed/      # Representasi kasus terstruktur
│   ├── eval/           # Dataset evaluasi
│   └── results/        # Summary hasil pipeline
│
├── notebooks/
│   ├── 01_case_base.ipynb          # Tahap 1 — Pipeline preprocessing
│   ├── 02_case_representation.ipynb # Tahap 2 — Feature extraction
│   ├── 03_case_retrieval.ipynb      # Tahap 3 — TF-IDF retrieval & SVM
│   ├── 04_case_reuse.ipynb          # Tahap 4 — Prediksi & Reuse
│   └── 05_model_evaluation.ipynb    # Tahap 5 — Evaluasi metrics
│
├── logs/
│   └── cleaning.log   # Log preprocessing
│
├── requirements.txt
└── README.md
```

## 🚀 Memulai

### Prasyarat

- Python 3.10+
- VSCode dengan extension Jupyter
- File PDF putusan pengadilan di dalam folder `datasets uu-ite/`

### Setup

1. Clone repository:
   ```bash
   git clone <repo-url>
   cd project-cbr
   ```

2. Buat dan aktifkan virtual environment:
   ```bash
   python -m venv .venv
   # Windows
   .venv\Scripts\activate
   # macOS/Linux
   source .venv/bin/activate
   ```

3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Jalankan pipeline secara utuh dengan membuka dan mengeksekusi (Run All) notebook berikut di VSCode secara berurutan:
```
- (`01_case_base.ipynb`) Tahap 1: Preprocessing teks
- (`02_case_representation.ipynb`) Tahap 2: Ekstraksi fitur terstruktur
- (`03_case_retrieval.ipynb`) Tahap 3: Sistem pencarian & pelatihan model
- (`04_case_reuse.ipynb`) Tahap 4: Prediksi & pengulangan kasus
- (`05_model_evaluation.ipynb`) Tahap 5: Perhitungan metrik evaluasi 
```
### Dataset

Tempatkan file PDF putusan pengadilan di dalam folder `datasets uu-ite/`. Pipeline akan mendeteksi seluruh file `.pdf` secara otomatis — tidak ada nama file yang di-hardcode.

## 📊 Tahap 1 — Pipeline Preprocessing

Notebook preprocessing (`01_case_base.ipynb`) melakukan proses berikut:

1. **PDF Extraction** — Mengekstrak teks dari PDF putusan pengadilan menggunakan `pdfplumber`.
2. **Text Cleaning** — Menghapus watermark, header/footer, serta menormalisasi whitespace.
3. **Validation** — Memeriksa file kosong, extraction failures, dan anomali lainnya.
4. **Logging** — Membuat log preprocessing secara detail.

### Keputusan Desain Utama

- **Tanpa stemming/lemmatization** — Teks hukum memerlukan preservasi konteks yang utuh.
- **Tanpa stopword removal** — Kata-kata umum (seperti "dan", "atau", "tidak") memiliki bobot hukum yang krusial.
- **Watermark-aware cleaning** — Menangani watermark dan disclaimer khas Mahkamah Agung secara khusus.
- **Encoding UTF-8** — Dukungan penuh untuk karakter Bahasa Indonesia.

## 🛠️ Tech Stack

| Komponen | Library |
|-----------|---------|
| PDF Extraction | `pdfplumber` |
| Data Handling | `pandas` |
| Progress Display | `tqdm` |
| Environment | VSCode + Jupyter |

## 📊 Tahap 2 — Case Representation

Notebook `02_case_representation.ipynb` mengekstrak fitur terstruktur dari 44 file teks mentah:

1. **Metadata** — `case_id`, `nomor_putusan`, `pengadilan`, `tanggal_putusan`, `terdakwa`, `text_full`.
2. **Fitur Retrieval** — `ringkasan_fakta` (primer), `pasal`, `amar_putusan` (sekunder), `pidana_penjara`, `denda`.
3. **Output** — `data/processed/cases.csv` (44 baris, 11 kolom).

Field kritis (`nomor_putusan`, `pasal`, `amar_putusan`) mencapai 100% extraction coverage.

## 🔍 Tahap 3 — Case Retrieval

Notebook `03_case_retrieval.ipynb` mengimplementasikan sistem retrieval kasus:

1. **Train/Test Split** — 80:20 (35 train, 9 test) dengan stratifikasi berdasarkan pasal primer.
2. **TF-IDF Vectorization** — 3012 fitur (unigrams + bigrams) dari `ringkasan_fakta`.
3. **Baseline** — TF-IDF + Cosine Similarity (Avg Precision@5 = 0.24).
4. **ML Model** — TF-IDF + Linear SVM (Avg Precision@5 = 0.33).
5. **API** — `retrieve(query_text, k=5, method='cosine'/'svm')`.
6. **Output** — `data/results/retrieval_baseline.json`, `data/eval/queries.json`.

## 📊 Tahap 4 — Case Reuse

Notebook `04_case_reuse.ipynb` menangani prediksi solusi:
1. **Case Reuse** — Majority-vote prediction dari top-K retrieved cases (`amar_putusan` dan `primary_pasal`).

## 📈 Tahap 5 — Model Evaluation

Notebook `05_model_evaluation.ipynb` menutup pipeline dengan evaluasi:
1. **Evaluation Metrics** — Accuracy, Precision, Recall, F1-Score.
2. **Confusion Matrix** — Heatmap untuk visualisasi prediksi.

| Metode | Accuracy | Precision (W) | Recall (W) | F1-Score (W) |
|--------|----------|---------------|------------|---------------|
| TF-IDF + Cosine Similarity | 0.3333 | 0.2778 | 0.3333 | 0.2963 |
| TF-IDF + Linear SVM | 0.3333 | 0.2407 | 0.3333 | 0.2794 |

**Output:**
- `data/results/predictions.csv`
- `data/results/evaluation_report.json`
- `data/results/confusion_matrix.png`
- `data/eval/retrieval_metrics.csv`
- `data/eval/prediction_metrics.csv`

## 📄 Lisensi

Hanya untuk penggunaan akademis. Dokumen putusan pengadilan merupakan dokumen publik Republik Indonesia.

---

*Dibuat sebagai bagian dari penelitian akademis project Case-Based Reasoning untuk analisis putusan hukum.*
