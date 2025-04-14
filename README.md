# Belajar-Pengambangan-Machine-Learning
**Pengenalan Dataset**

1. **Sumber Dataset**:  
   Dataset bersumber dari hasil Scrapping ulasan di aplikasi Google Play Store, dimana ulasan yang diambil adalah ulasan dari aplikasi M-banking BCA
   
2. **Kriteria Dataset**:
   - **Jumlah Baris**: Minimal 144000 baris untuk memastikan dataset cukup besar untuk analisis yang bermakna.
   - **Tipe Data**: Harus mengandung data **kategorikal** dan **numerikal**.
     - *Kategorikal*: reviewId, Username, userImage, content, reviewCreatedVersion, replyContent, dan AppVersion
     - *Numerikal*: Misalnya thumbsUpCount, score.

**Pengenalan Preprocessing**

## 1. Cleaning Text
Membersihkan teks dari:
- Mention (@username)
- Hashtag (#hashtag)
- Retweet (RT)
- URL
- Angka
- Tanda baca
- Karakter aneh dan whitespace berlebih

**Fungsi:** `cleaningText(text)`

## 2. Case Folding
Mengubah semua huruf menjadi huruf kecil (lowercase) agar konsisten.
**Fungsi:** `casefoldingText(text)`
---
## 3. Slang Words Normalization
Mengganti kata-kata gaul/slang menjadi bentuk baku, contoh:
- `abis` ➔ `habis`
- `wtb` ➔ `beli`
- `masi` ➔ `masih`

**Fungsi:** `fix_slangwords(text)`

---

## 4. Tokenizing
Memecah kalimat menjadi daftar kata.

**Fungsi:** `tokenizingText(text)`

---

## 5. Stopword Removal
Menghapus kata-kata yang tidak memiliki makna penting (seperti: *dan, yang, di, iya, gak*).

**Fungsi:** `filteringText(text)`

---

## 6. Stemming
Mengubah kata ke bentuk dasar (root word) menggunakan library **Sastrawi**.

**Fungsi:** `stemmingText(text)`

---

## 7. Final Cleaning
Menggabungkan kembali list kata menjadi 1 kalimat bersih.

**Fungsi:** `toSentence(list_words)`

---

# Lexicon-Based Sentiment Analysis

Menggunakan kamus lexicon dari URL berikut:
- Positive words: `lexicon_positive.csv`
- Negative words: `lexicon_negative.csv`

**Fungsi:** `sentiment_analysis_lexicon_indonesia(text)`

- Skor dihitung berdasarkan jumlah bobot kata positif dan negatif.
- Hasil akhir: **Positive**, **Negative**, atau **Neutral**.
