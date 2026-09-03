# Pemrosesan Citra Digital

Arsip tugas praktikum **Pemrosesan Citra Digital** menggunakan OpenCV, NumPy,
Matplotlib, dan Pillow.

**Nama:** Dika Aprilio Wibowo &nbsp;&middot;&nbsp; **NIM:** J0403241096

Setiap pertemuan berisi satu notebook yang sudah dieksekusi lengkap dengan
seluruh gambar dan output, plus versi `.html` yang siap dikumpulkan.

## Daftar Pertemuan

| Pertemuan | Topik | Notebook | Siap kumpul |
|-----------|-------|----------|-------------|
| 2 | Dasar manipulasi citra: RGB vs BGR, grayscale manual vs bawaan, resize, crop | [`.ipynb`](pertemuan-2/J0403241096_Dika%20Aprilio%20Wibowo_Pertemuan2.ipynb) | [`.html`](pertemuan-2/J0403241096_Dika%20Aprilio%20Wibowo_Pertemuan2.html) |
| 3 | Operasi titik dan ketetanggaan: thresholding, negatif, brightening, smoothing, sharpening | [`.ipynb`](pertemuan-3/J0403241096_Dika%20Aprilio%20Wibowo_Pertemuan3.ipynb) | [`.html`](pertemuan-3/J0403241096_Dika%20Aprilio%20Wibowo_Pertemuan3.html) |

### Pertemuan 2 - Dasar Manipulasi Citra

1. Menampilkan citra pribadi dengan Matplotlib (array BGR mentah vs RGB hasil konversi).
2. Grayscale dengan formula manual (BT.601 dan rata-rata) **dan** fungsi bawaan,
   dibandingkan lewat MAE, PSNR, serta peta selisih.
3. Resize 200% (`INTER_CUBIC`) dan 25% (`INTER_AREA`), ditampilkan berdampingan.
4. Crop objek utama memakai ROI berbasis rasio, disimpan sebagai berkas baru.

### Pertemuan 3 - Operasi Titik dan Ketetanggaan

1. **Thresholding** pada T = 80 / 128 / 180, diverifikasi terhadap `cv2.threshold`
   dan dibandingkan dengan ambang otomatis Otsu.
2. **Citra negatif** `s = 255 - r`, dengan uji numerik bahwa histogram sesudah
   adalah cerminan sempurna histogram sebelum.
3. **Brightening** dengan b = -100 / -50 / +50 / +100, penghitungan piksel
   ter-*clip*, plus demonstrasi bug *wrap-around* pada `uint8`.
4. **Smoothing** mean filter 3x3 vs 5x5 pada citra ber-noise, diverifikasi
   terhadap `cv2.blur` dan dibandingkan lewat MAE/PSNR dan ukuran ketajaman.
5. **Sharpening** kernel Laplacian K4 dan K8, ketajaman tepi diukur lewat
   simpangan baku Laplacian, gradien Sobel, dan profil intensitas melintang.

## Struktur

```
pemrosesan-citra/
├── images/                     # citra sumber, dipakai bersama semua pertemuan
│   └── foto_kucing.jpg
├── pertemuan-2/
│   ├── ..._Pertemuan2.ipynb    # notebook (sudah dieksekusi)
│   ├── ..._Pertemuan2.html     # versi siap kumpul
│   └── output/                 # hasil olahan (di-generate otomatis)
├── pertemuan-3/
│   ├── ..._Pertemuan3.ipynb
│   ├── ..._Pertemuan3.html
│   └── output/
├── requirements.txt
└── README.md
```

Folder `output/` sengaja di-*ignore* oleh git karena isinya dibuat ulang setiap
kali notebook dijalankan.

## Cara Menjalankan

**1. Pasang dependensi**

```bash
python -m pip install -r requirements.txt
```

> Bentuk `python -m pip` dipakai supaya tetap jalan walaupun `pip.exe` tidak
> terdaftar di PATH, kasus yang umum pada instalasi Python di Windows.

**2. Buka notebook pertemuan yang diinginkan**

```bash
python -m jupyter notebook
```

Masuk ke folder pertemuan, buka notebooknya, lalu jalankan semua sel dari atas
ke bawah (`Kernel -> Restart & Run All`).

Setiap notebook mengambil citra dari folder `images/` di root repository dan
memilih berkas gambar pertama yang ditemukan, jadi nama filenya bebas. Format
yang didukung: `.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`, `.tif`.

**3. Ekspor ulang ke HTML setelah notebook diubah**

```bash
python -m jupyter nbconvert --to html --HTMLExporter.mathjax_url="" "NAMA_NOTEBOOK.ipynb"
```

Semua gambar ditanam sebagai data URI sehingga berkas HTML-nya mandiri dan tetap
tampil normal walau dibuka tanpa koneksi internet. Opsi `mathjax_url=""`
mencegah nbconvert memuat MathJax dari CDN, yang tidak diperlukan karena rumus
di notebook ditulis sebagai teks biasa.

## Catatan Teknis

Poin-poin yang berulang muncul di beberapa pertemuan:

- **BGR vs RGB.** OpenCV membaca citra dalam urutan BGR, hampir semua library
  lain memakai RGB. Lupa `cv2.cvtColor` adalah sumber bug warna paling umum.
- **Rentang 8-bit itu keras.** Operasi aritmetika harus dihitung dalam
  `int16`/`float`, di-`np.clip(0, 255)`, baru dikembalikan ke `uint8`.
  Menjumlahkan langsung pada `uint8` menghasilkan *wrap-around*, bukan clipping.
- **Bobot grayscale.** BT.601 (`0.299R + 0.587G + 0.114B`) mengikuti
  sensitivitas mata manusia; rata-rata sederhana `(R+G+B)/3` tidak.
- **Interpolasi resize.** `INTER_AREA` untuk memperkecil, `INTER_CUBIC` atau
  `INTER_LANCZOS4` untuk memperbesar.
- **Slicing NumPy.** Urutannya `[y1:y2, x1:x2]`, baris dulu baru kolom.
- **Verifikasi manual vs bawaan.** Setiap implementasi manual di notebook
  dibandingkan dengan fungsi bawaan OpenCV/Pillow, supaya analisisnya berpijak
  pada perhitungan yang terbukti benar.
