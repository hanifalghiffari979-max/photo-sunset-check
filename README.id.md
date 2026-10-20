# Photo Sunset Check

[English](README.md) · **Bahasa Indonesia**

Memeriksa apakah jam pada foto masuk akal untuk tanggal dan tempatnya, serta menunjukkan bagaimana asumsi zona waktu bisa membalikkan kesimpulan.

- Repositori: https://github.com/hanifalghiffari979-max/photo-sunset-check
- Demo web: https://hanifalghiffari979-max.github.io/photo-sunset-check/gui/ (aktif setelah GitHub Pages dinyalakan)

## Mengapa proyek ini dibuat

Bayangkan sebuah foto bertanda jam 18:38 di suatu kota pada 12 Oktober. Apakah saat itu masih terang, senja, atau gelap?

Jawabannya tergantung "18:38" yang mana. Beberapa negara mengubah aturan jam musim panas dari tahun ke tahun. Meksiko memajukan jam di musim panas sampai 2022, lalu menghapusnya. Jadi 18:38 pada 2013 dan 18:38 pada 2026 di tempat yang sama adalah dua saat yang berbeda bagi matahari, selisihnya satu jam.

Alat ini menghitung posisi matahari lalu membandingkannya dengan jam foto dalam beberapa asumsi jam kamera. Jawabannya bukan "benar" atau "salah", melainkan: **dalam asumsi apa foto ini masuk akal?**

| Tahun | Sunset (jam dinding) | Jam foto 18:38 |
|---|---|---|
| 2013 | 19:26 | Masih terang (matahari 10,4° di atas horizon) |
| 2026 | 18:26 | Senja (matahari 3,4° di bawah horizon) |

Tempat, tanggal, dan jam kamera sama. Hanya tahun, dan karenanya aturan zona waktu, yang berbeda.

## Fitur

- Tiga status, bukan vonis ya/tidak: sebelum sunset, senja, gelap.
- Empat skenario jam kamera: jam lokal sesuai aturan tahun itu, tetap UTC−6, tetap UTC−5, kamera disetel ke UTC.
- Antarmuka web yang membaca EXIF dari foto (waktu, offset, GPS, kecerahan). Foto tetap di perangkat Anda.
- Grafik elevasi matahari dengan penanda jam kamera.
- Uji otomatis dan contoh output yang terekam.

## Cara menjalankan

```bash
git clone https://github.com/hanifalghiffari979-max/photo-sunset-check.git
cd photo-sunset-check
pip install -r requirements.txt
python -m src.sunset_check
python -m pytest
```

Dengan input sendiri:

```bash
python -m src.sunset_check --lat 19.87 --lon -101.92 --date 2013-10-12 --time 18:38
```

Antarmuka web: buka `gui/index.html` di browser.

## Struktur repositori

| Jalur | Fungsi |
|---|---|
| `src/sunset_check.py` | Alat baris perintah Python |
| `gui/index.html` | Antarmuka web dengan pembaca EXIF |
| `tests/` | Uji otomatis (kasus 2013 vs 2026) |
| `examples/original_v0_1.py` | Skrip awal v0.1, disimpan sebagai pembanding |
| `REPORT.md`, `docs/REPORT.pdf` | Laporan (Inggris) |
| `LAPORAN.md`, `docs/LAPORAN.pdf` | Laporan (Indonesia) |
| `docs/sample_output.txt` | Output asli dari satu kali eksekusi |

## Yang TIDAK dilakukan alat ini

- Tidak membuktikan foto asli atau palsu.
- Tidak tahu apakah jam kamera pernah disetel dengan benar.
- Koordinat bawaan adalah asumsi dan perlu diverifikasi sebelum menarik kesimpulan.
- Kecerahan EXIF (EV100) hanya petunjuk kasar dan menyesatkan untuk foto dalam ruangan atau memakai flash.
- EXIF mudah diubah dan sering dihapus aplikasi pesan.
- Belum menganalisis arah dan panjang bayangan, yang buktinya lebih kuat daripada jam.
- Antarmuka web belum diuji dengan beragam foto sungguhan.

## Teknologi

Python 3.12, `astral` 3.2, `zoneinfo`/`tzdata`, `pytest`. Antarmuka web: JavaScript murni (algoritma posisi matahari NOAA) dan `exifr`.

## Rencana lanjutan

- [ ] Verifikasi koordinat bawaan dari sumber yang bisa dicek
- [ ] Prediksi arah dan panjang bayangan
- [ ] Rentang ketidakpastian, bukan satu angka
- [ ] Pencocokan EV100 dengan elevasi matahari yang diharapkan
- [ ] Input CSV untuk banyak kasus

## Penulis

Mohammad Hanif Alghifari · [@hanifalghiffari979-max](https://github.com/hanifalghiffari979-max)

## Lisensi

MIT, © 2026 Mohammad Hanif Alghifari. Lihat `LICENSE`.
