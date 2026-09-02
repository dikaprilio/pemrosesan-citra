# Pemrosesan Citra Digital

Notebook praktikum pemrosesan citra digital menggunakan **OpenCV**, **NumPy**,
**Matplotlib**, dan **Pillow**.

## Info Tugas

1. Pilih satu citra pribadi, tampilkan menggunakan Matplotlib (asli & RGB yang benar).
2. Buat versi grayscale dengan formula manual **DAN** dengan fungsi bawaan — bandingkan hasilnya.
3. Resize citra menjadi 200% dan 25% dari ukuran asli, tampilkan berdampingan (subplot).
4. Crop bagian objek utama pada citra, simpan sebagai file baru.

## Struktur

```
pemrosesan-citra/
├── pemrosesan_citra.ipynb   # notebook utama (semua tugas)
├── pemrosesan_citra.html    # versi HTML siap kumpul (output ikut tersimpan)
├── images/                  # ← simpan foto pribadi di sini
├── output/                  # hasil olahan (dibuat otomatis)
├── requirements.txt
└── README.md
```

## Cara Menjalankan

**1. Pasang dependensi**

```bash
python -m pip install -r requirements.txt
```

> Bentuk `python -m pip` dipakai supaya tetap jalan walaupun `pip.exe` tidak
> terdaftar di PATH — kasus yang umum pada instalasi Python di Windows.

**2. Simpan foto pribadi ke folder `images/`**

Notebook otomatis mengambil file gambar pertama yang ditemukan di folder
tersebut, jadi nama filenya bebas. Format yang didukung: `.jpg`, `.jpeg`,
`.png`, `.bmp`, `.webp`, `.tif`.

**3. Buka notebook**

```bash
python -m jupyter notebook pemrosesan_citra.ipynb
```

Jalankan semua sel dari atas ke bawah (`Kernel → Restart & Run All`).

**4. Sesuaikan area crop (Tugas 4)**

Di sel Tugas 4 ada variabel `ROI` berisi rasio `(x1, y1, x2, y2)` dengan nilai
`0.0`–`1.0`. Nilai bawaannya sudah disetel untuk foto yang ada di `images/`.
Kalau kamu mengganti fotonya, ubah nilai ini lalu jalankan sel pratinjau untuk
mengecek posisi kotaknya sebelum di-crop.

```python
ROI = (0.00, 0.49, 0.74, 0.93)   # kiri, atas, kanan, bawah
```

## Ekspor ke HTML

Berkas [`pemrosesan_citra.html`](pemrosesan_citra.html) adalah versi notebook
yang sudah dieksekusi lengkap dengan seluruh gambar dan output — tinggal dibuka
di browser, tidak perlu Python atau Jupyter.

Untuk membuat ulang setelah notebook diubah:

```bash
python -m jupyter nbconvert --to html --HTMLExporter.mathjax_url="" pemrosesan_citra.ipynb
```

Semua gambar ditanam sebagai data URI sehingga berkasnya mandiri dan tetap
tampil normal walau dibuka tanpa koneksi internet. Opsi `mathjax_url=""`
mencegah nbconvert memuat MathJax dari CDN, yang tidak diperlukan karena rumus
di notebook ditulis sebagai teks biasa.

## Berkas Keluaran

Setelah notebook dijalankan, folder `output/` akan berisi:

| Berkas | Keterangan |
|--------|------------|
| `grayscale_manual.jpg` | Grayscale hasil formula manual BT.601 |
| `grayscale_bawaan.jpg` | Grayscale hasil `cv2.cvtColor` |
| `resize_200persen.jpg` | Citra diperbesar 200% (`INTER_CUBIC`) |
| `resize_25persen.jpg` | Citra diperkecil 25% (`INTER_AREA`) |
| `crop_objek_utama.jpg` | Hasil crop objek utama |

## Catatan Teknis

- **BGR vs RGB** — OpenCV membaca citra dalam urutan BGR, sementara Matplotlib
  mengharapkan RGB. Tanpa `cv2.cvtColor(..., cv2.COLOR_BGR2RGB)` kanal merah dan
  biru akan tertukar.
- **Bobot grayscale** — Formula BT.601 (`0.299R + 0.587G + 0.114B`) menghasilkan
  citra yang praktis identik dengan `cv2.cvtColor` (selisih hanya 0–1 level,
  akibat pembulatan integer di OpenCV). Rata-rata sederhana `(R+G+B)/3` berbeda
  jauh karena mengabaikan sensitivitas mata manusia.
- **Interpolasi resize** — Gunakan `INTER_AREA` untuk memperkecil (mencegah
  aliasing) dan `INTER_CUBIC`/`INTER_LANCZOS4` untuk memperbesar.
- **Crop** — Slicing NumPy dengan urutan `[y1:y2, x1:x2]`: baris dulu, baru kolom.

> Folder `output/` sengaja di-*ignore* oleh git karena isinya di-generate ulang
> setiap kali notebook dijalankan.
