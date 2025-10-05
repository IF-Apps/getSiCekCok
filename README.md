# SiCekCok Data Fetcher 🎓

Sistem asynchronous untuk mengambil data mahasiswa dari GraphQL API SiCekCok (Sistem Informasi Mahasiswa Universitas Muhammadiyah Makassar). Proyek ini menggunakan Python dengan async HTTP requests untuk mengambil data akademik mahasiswa secara efisien dan paralel.

## 📋 Deskripsi

Proyek ini dirancang untuk:
- Mengambil data mahasiswa berdasarkan daftar NIM (Nomor Induk Mahasiswa)
- Query data akademik termasuk KHS (Kartu Hasil Studi) dari API GraphQL SiCekCok
- Memproses dan mengekspor data ke format CSV untuk analisis lebih lanjut
- Menggunakan asynchronous programming untuk performa optimal (1,525+ mahasiswa dalam satu proses)

## 🚀 Fitur Utama

- **Async GraphQL Queries**: Mengambil data mahasiswa secara paralel menggunakan `aiohttp`
- **Data Flattening**: Mengubah nested KHS data menjadi format tabel flat
- **Bulk Processing**: Memproses 1,525+ NIM dalam satu eksekusi
- **CSV Export**: Output otomatis dengan timestamp
- **Error Handling**: Robust handling untuk API responses
- **Data Validation**: Built-in validation untuk memverifikasi data yang diambil

## 📁 Struktur Proyek

```
getSiCekCok/
├── data/
│   └── nim.csv                          # Input: Daftar NIM mahasiswa (1,525 entries)
├── notebooks/
│   └── async-get-mahasiswa.ipynb        # Main notebook untuk data fetching
├── outputs/
│   └── mahasiswa_data_YYYYMMDD_HHMMSS.csv  # Output: Data mahasiswa dengan timestamp
├── requirements.txt                      # Python dependencies
├── LICENSE                               # Lisensi proyek
└── README.md                             # Dokumentasi ini
```

## 🛠️ Instalasi

### Prerequisites
- Python 3.12+
- pip (Python package manager)
- Virtual environment (recommended)

### Langkah Instalasi

1. **Clone repository**
   ```bash
   git clone https://github.com/IF-Apps/getSiCekCok.git
   cd getSiCekCok
   ```

2. **Buat virtual environment** (opsional tapi disarankan)
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # Linux/Mac
   # atau
   .venv\Scripts\activate     # Windows
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

## 📝 Cara Penggunaan

### 1. Persiapan Data Input

Pastikan file `data/nim.csv` berisi daftar NIM dengan format:
```csv
"nim"
"10581232015"
"10581232115"
...
```

### 2. Jalankan Notebook

Buka dan jalankan `notebooks/async-get-mahasiswa.ipynb`:

```bash
jupyter notebook notebooks/async-get-mahasiswa.ipynb
```

Atau gunakan VS Code dengan Jupyter extension.

### 3. Eksekusi Cell Secara Berurutan

Notebook terdiri dari beberapa langkah:

1. **Langkah 0**: Install pustaka yang diperlukan
2. **Langkah 1**: Import libraries
3. **Langkah 2**: Definisi fungsi `query_graphql`
4. **Langkah 3**: Membaca file NIM
5. **Langkah 4**: Async data fetching dan processing
6. **Langkah 5**: Membuat DataFrame
7. **Langkah 6**: Verifikasi data
8. **Validasi**: Lookup NIM spesifik
9. **Simpan**: Export ke CSV dengan timestamp

### 4. Output

Data akan disimpan di `outputs/` dengan nama file:
```
mahasiswa_data_20251005_143022.csv
```

## 📊 Struktur Data Output

Output CSV berisi kolom-kolom berikut:

### Data Mahasiswa

- `kodeProdi`: Kode program studi (contoh: 22202)
- `namaProdi`: Nama program studi (contoh: Pengairan)
- `angkatan`: Tahun angkatan masuk
- `semesterAwal`: Semester pertama masuk (format: YYYYS, contoh: 20151)
- `nim`: Nomor Induk Mahasiswa
- `nama`: Nama lengkap mahasiswa
- `jenisKelamin`: Jenis kelamin (L=Laki-laki, P=Perempuan)
- `tahunAkademikLulus`: Tahun akademik kelulusan (format: YYYYS)
- `tanggalLulus`: Tanggal kelulusan (format: YYYY-MM-DD)
- `lulus`: Status kelulusan (True/False)
- `masaStudi`: Durasi masa studi (contoh: "4 Tahun, 5 Bulan")

### Data KHS (Kartu Hasil Studi)
Untuk setiap semester (khs1, khs2, ..., khsN):
- `khs{N}_tahunAkademik`: Tahun akademik semester
- `khs{N}_ips`: Indeks Prestasi Semester
- `khs{N}_sksSmt`: SKS semester
- `khs{N}_ipk`: Indeks Prestasi Kumulatif
- `khs{N}_sksTotal`: Total SKS kumulatif
- `khs{N}_statusMahasiswa`: Status (A=Aktif, N=Non-aktif, X=Cuti, dll)

## 🔧 Dependencies

### Core Dependencies
- **aiohttp** (>=3.9.0): Async HTTP client untuk GraphQL requests
- **pandas** (>=2.2.0): Data processing dan CSV export
- **ipykernel** (>=6.29.0): Jupyter notebook kernel

### Optional Dependencies
- **ipywidgets** (>=8.1.0): Interactive widgets
- **matplotlib** (>=3.8.0): Data visualization
- **tqdm** (>=4.66.0): Progress bars

## 🎯 GraphQL API

Endpoint: `https://sicekcok.if.unismuh.ac.id/graphql`

### Query Structure
```graphql
query($nim: String!) {
  mahasiswa(nim: $nim) {
    nim
    nama
    kodeProdi
    angkatan
    jenisKelamin
    semesterAwal
    tahunAkademikLulus
    tanggalLulus
    lulus
    masaStudi
    khs {
      tahunAkademik
      ips
      sksSmt
      ipk
      sksTotal
      statusMahasiswa
    }
    prodi {
      namaProdi
    }
  }
}
```

## ⚡ Performance

- **Async Processing**: Menggunakan `asyncio.gather()` untuk concurrent requests
- **Batch Size**: 1,525+ NIM diproses secara paralel
- **Execution Time**: ~10-30 detik (tergantung koneksi dan response time API)
- **Memory Efficient**: Streaming data processing

## 🔍 Validasi Data

Notebook menyediakan cell validasi untuk lookup NIM spesifik:

```python
nim_yang_dicari = '105821100817'
data_nim = df.loc[df['nim'] == nim_yang_dicari]
```

## 🤝 Contributing

Kontribusi sangat diterima! Silakan:
1. Fork repository
2. Buat branch baru (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add some AmazingFeature'`)
4. Push ke branch (`git push origin feature/AmazingFeature`)
5. Buat Pull Request

## 📄 License

Lihat file [LICENSE](LICENSE) untuk detail.

## 👥 Authors

- **IF-Apps** - [GitHub Profile](https://github.com/IF-Apps)

## 🙏 Acknowledgments

- Universitas Muhammadiyah Makassar
- SiCekCok System Team
- GraphQL API providers

## 📞 Support

Jika mengalami masalah atau memiliki pertanyaan:
- Buka [Issue](https://github.com/IF-Apps/getSiCekCok/issues)
- Hubungi maintainer

## 📈 Roadmap

- [ ] Tambahkan retry mechanism untuk failed requests
- [ ] Implementasi rate limiting
- [ ] Dashboard visualisasi data
- [ ] Export ke format lain (Excel, JSON)
- [ ] Machine Learning analysis pipeline
- [ ] REST API wrapper
- [ ] Docker containerization

---

**Note**: Proyek ini hanya untuk keperluan akademis dan penelitian. Gunakan dengan bijak dan hormati privasi data mahasiswa.b Codespaces ♥️ Jupyter Notebooks

Welcome to your shiny new codespace! We've got everything fired up and running for you to explore Python and Jupyter notebooks.

You've got a blank canvas to work on from a git perspective as well. There's a single initial commit with what you're seeing right now - where you go from here is up to you!

Everything you do here is contained within this one codespace. There is no repository on GitHub yet. If and when you’re ready you can click "Publish Branch" and we’ll create your repository and push up your project. If you were just exploring then and have no further need for this code then you can simply delete your codespace and it's gone forever.
