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
| **Tahap 3** | Case Retrieval — Similarity & Matching | 🔲 Direncanakan |
| **Tahap 4** | Case Reuse & Evaluation | 🔲 Direncanakan |

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
│   └── 02_case_representation.ipynb # Tahap 2 — Feature extraction
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

4. Buka `notebooks/01_case_base.ipynb` di VSCode dan jalankan semua cell.

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

## 📄 Lisensi

Hanya untuk penggunaan akademis. Dokumen putusan pengadilan merupakan dokumen publik Republik Indonesia.

---

*Dibuat sebagai bagian dari penelitian akademis project Case-Based Reasoning untuk analisis putusan hukum.*
