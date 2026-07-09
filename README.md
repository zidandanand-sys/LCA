# Analisis LCA Sederhana: Perbandingan Dampak Lingkungan Kemasan Minuman

Tugas individu — Mata Kuliah Metodologi Penelitian Ilmiah
Topik: Reproducible Research (Analisis Lingkungan — Life Cycle Assessment)

## 1. Tujuan

Proyek ini melakukan **Life Cycle Assessment (LCA) sederhana** untuk
membandingkan dampak lingkungan tiga jenis kemasan minuman yang umum:
**Botol PET**, **Botol Kaca**, dan **Kaleng Aluminium**, dengan unit
fungsional **1 liter minuman terkemas**. Secara spesifik, proyek ini
bertujuan untuk:

1. Menghitung total dampak lingkungan (potensi pemanasan global/GWP,
   penggunaan energi, penggunaan air) untuk memproduksi tiap jenis kemasan.
2. Mengidentifikasi tahap siklus hidup (ekstraksi bahan baku, produksi,
   transportasi, pengolahan akhir/EoL) yang berkontribusi paling besar
   terhadap dampak tiap kemasan.
3. Membuat skor komposit sederhana (normalisasi min-max + bobot setara)
   untuk merangking kemasan mana yang secara keseluruhan paling ramah
   lingkungan.

## 2. Metode

Mengikuti kerangka umum LCA (ISO 14040/14044, disederhanakan untuk tugas
kuliah):

1. **Tujuan & Ruang Lingkup** — unit fungsional = 1 liter minuman terkemas;
   batas sistem = *cradle-to-grave* sederhana (ekstraksi → produksi →
   transportasi → pengolahan akhir), tidak termasuk tahap penggunaan karena
   diasumsikan tidak signifikan untuk kemasan pasif.
2. **Inventarisasi (LCI)** — data pada `data/data_lca_kemasan.csv`.
3. **Penilaian Dampak (LCIA)** — tiga kategori dampak: GWP (kg CO2-eq),
   energi (MJ), air (L); dijumlahkan per tahap dan dinormalisasi untuk
   skor komposit.
4. **Interpretasi** — dirangkum di bagian 7 di bawah.

⚠️ **Catatan penting**: Data LCI yang digunakan bersifat **ilustratif**
(nilai representatif dari kisaran umum literatur LCA kemasan minuman),
**bukan** data pengukuran primer. Untuk skripsi/tesis/riset sesungguhnya,
ganti `data/data_lca_kemasan.csv` dengan data dari database LCI standar
(mis. **ecoinvent**, **GaBi**) atau pengukuran/data sekunder primer sesuai
konteks penelitian Anda.

## 3. Mengapa proyek ini reprodusibel?

| Pilar reproducibility | Implementasi dalam proyek ini |
|---|---|
| Data tersedia | Data mentah disertakan langsung di `data/data_lca_kemasan.csv`. |
| Kode tersedia | Seluruh langkah analisis (perhitungan LCI/LCIA, skor komposit, 3 visualisasi) ada dalam satu skrip `analisis_lca_kemasan.py`. |
| Dokumentasi | Setiap fungsi dalam skrip diberi *docstring*/komentar; README ini menjelaskan tujuan, metode, dan cara menjalankan. |
| Lingkungan komputasi | Versi Python & paket dicatat di `requirements.txt` dan otomatis dicatat ulang di `environment_info.txt` setiap skrip dijalankan. |
| Hasil deterministik | Tidak ada komponen acak → tabel dan gambar yang dihasilkan akan identik di setiap mesin selama data input sama. `np.random.seed(123)` tetap disertakan sebagai praktik baik untuk pengembangan lanjutan (mis. analisis ketidakpastian Monte Carlo). |
| Path relatif | Skrip hanya menggunakan path relatif (`data/...`, `output/...`), tidak ada path absolut. |

## 4. Struktur folder

```
proyek-lca-kemasan/
├── README.md
├── requirements.txt
├── LICENSE                                  # Lisensi MIT untuk kode
├── DATA_LICENSE.md                          # Keterangan lisensi & sumber data
├── environment_info.txt                     # Dicatat ulang otomatis saat skrip dijalankan
├── analisis_lca_kemasan.py                  # Skrip analisis utama
├── data/
│   └── data_lca_kemasan.csv                 # Data LCI (ilustratif)
└── output/                                  # Dihasilkan otomatis oleh skrip
    ├── total_dampak_per_material.csv
    ├── kontribusi_tahap_siklus_hidup.csv
    ├── skor_komposit_dan_peringkat.csv
    ├── gambar_1_kontribusi_tahap_GWP.png
    ├── gambar_2_perbandingan_total_dampak.png
    └── gambar_3_skor_komposit.png
```

## 5. Cara instalasi

```bash
pip install -r requirements.txt
```

## 6. Cara menjalankan

```bash
python analisis_lca_kemasan.py
```

Skrip akan mencetak ringkasan ke konsol, lalu menyimpan seluruh tabel (CSV)
dan gambar (PNG) ke folder `output/`, serta menuliskan ulang
`environment_info.txt`.

## 7. Hasil & Interpretasi (dari data ilustratif ini)

**Total dampak per 1 liter minuman terkemas:**

| Kemasan | GWP (kg CO2-eq) | Energi (MJ) | Air (L) |
|---|---|---|---|
| Botol PET | 1.62 | 46.5 | 8.30 |
| Botol Kaca | 1.80 | 49.0 | 7.50 |
| Kaleng Aluminium | 1.88 | 61.5 | 6.15 |

**Skor komposit (0 = terbaik, 1 = terburuk, bobot setara antar kategori):**

| Peringkat | Kemasan | Skor Komposit |
|---|---|---|
| 1 | Botol PET | 0.333 |
| 2 | Botol Kaca | 0.496 |
| 3 | Kaleng Aluminium | 0.667 |

**Interpretasi:**
- **Kaleng Aluminium** memiliki dampak GWP & energi tertinggi — didominasi
  oleh tahap **ekstraksi bauksit & pemurnian** (85–89% dari total GWP dan
  energi), meskipun daur ulangnya memberi "kredit" negatif yang cukup besar
  dan menghasilkan penggunaan air paling rendah.
- **Botol Kaca** paling banyak dipengaruhi oleh tahap **produksi/peleburan**
  (61–80% dari total), sesuai karakteristik industri kaca yang boros energi
  pada tahap manufaktur.
- **Botol PET** menunjukkan skor komposit terbaik pada data ilustratif ini,
  meskipun penggunaan airnya tertinggi — menekankan pentingnya melihat
  **beberapa kategori dampak sekaligus**, bukan hanya satu (mis. GWP saja),
  karena kemasan "terbaik" dapat berbeda tergantung kategori dampak mana
  yang diprioritaskan.
- Hasil ini **sangat bergantung pada asumsi bobot setara** dan nilai LCI
  ilustratif; dalam LCA nyata, pembobotan dan sumber data LCI perlu
  ditentukan secara eksplisit dan dijustifikasi.

## 8. Data

Data pada `data/data_lca_kemasan.csv` bersifat ilustratif, disusun
berdasarkan kisaran nilai yang umum dikutip dalam literatur LCA kemasan
minuman (lihat referensi). Lihat `DATA_LICENSE.md` untuk keterangan lebih
lanjut.

## 9. Lisensi

- Kode: [MIT License](LICENSE)
- Data: lihat [DATA_LICENSE.md](DATA_LICENSE.md) — data ilustratif ini
  dilisensikan **CC-BY 4.0** (bebas digunakan ulang untuk pendidikan dengan
  atribusi).

## 10. Referensi

- International Organization for Standardization. (2006). *ISO 14040:
  Environmental management — Life cycle assessment — Principles and
  framework*.
- International Organization for Standardization. (2006). *ISO 14044:
  Environmental management — Life cycle assessment — Requirements and
  guidelines*.
- Wilkinson, M. D., et al. (2016). The FAIR Guiding Principles for
  scientific data management and stewardship. *Scientific Data*, 3, 160018.
- Wilson, G., et al. (2017). Good enough practices in scientific computing.
  *PLoS Computational Biology*, 13(6), e1005510.
