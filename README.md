
# Pengembangan Machine Learning - Sentiment Analysis

## 1. Pengenalan Dataset

- **Sumber Dataset**:  
  Dataset diperoleh melalui web scraping ulasan pengguna dari aplikasi **M-Banking BCA** di Google Play Store.

- **Kriteria Dataset**:
  - **Jumlah Baris**: Minimal **144.000** baris untuk memastikan dataset cukup besar dan representatif.
  - **Tipe Data**:
    - **Kategorikal**:
      - `reviewId`
      - `Username`
      - `userImage`
      - `content`
      - `reviewCreatedVersion`
      - `replyContent`
      - `AppVersion`
    - **Numerikal**:
      - `thumbsUpCount`
      - `score`

---

## 2. Pengenalan Preprocessing

### 2.1 Cleaning Text
Membersihkan teks dari:
- Mention (@username)
- Hashtag (#hashtag)
- Retweet (RT)
- URL
- Angka
- Tanda baca
- Karakter aneh
- Whitespace berlebih  

**Fungsi**:  
```python
cleaningText(text)
```

---

### 2.2 Case Folding
Mengubah seluruh huruf dalam teks menjadi huruf kecil (lowercase) untuk konsistensi.  

**Fungsi**:  
```python
casefoldingText(text)
```

---

### 2.3 Slang Words Normalization
Mengganti kata-kata tidak baku atau kata gaul dengan padanan baku.  
Contoh:
- `abis` → `habis`
- `wtb` → `beli`
- `masi` → `masih`

**Fungsi**:  
```python
fix_slangwords(text)
```

---

### 2.4 Tokenizing
Memecah teks menjadi daftar kata (tokens).  

**Fungsi**:  
```python
tokenizingText(text)
```

---

### 2.5 Stopword Removal
Menghapus kata-kata umum yang tidak berkontribusi signifikan terhadap makna teks.  
Contoh stopwords: *dan, yang, di, iya, gak*.  

**Fungsi**:  
```python
filteringText(text)
```

---

### 2.6 Stemming
Mengubah kata-kata ke bentuk dasarnya menggunakan library **Sastrawi**.  

**Fungsi**:  
```python
stemmingText(text)
```

---

### 2.7 Final Cleaning
Menggabungkan daftar kata menjadi satu kalimat bersih.  

**Fungsi**:  
```python
toSentence(list_words)
```

---

## 3. Lexicon-Based Sentiment Analysis

Menggunakan pendekatan berbasis kamus (lexicon) untuk menentukan sentimen teks.

- **Kamus**:
  - **Positive words**: `lexicon_positive.csv`
  - **Negative words**: `lexicon_negative.csv`

**Fungsi**:  
```python
sentiment_analysis_lexicon_indonesia(text)
```

**Metode**:
- Menghitung jumlah kata positif dan negatif dalam teks.
- Menghitung skor sentimen berdasarkan jumlah kata positif dan negatif.
- Menentukan hasil akhir sentimen:
  - **Positive**
  - **Negative**
  - **Neutral**

---

## 4. Data Labeling dan Visualisasi Wordcloud

### 4.1 Penyesuaian Skor Rating

- Membuat fitur baru `thumbsUpCount_norm` untuk menormalkan jumlah likes dari review pengguna.
- Membuat `rating_score` yang memperhitungkan skor review (`score`) dan penguatan dari banyaknya likes (`thumbsUpCount`).

```python
df['thumbsUpCount_norm'] = df['thumbsUpCount'] / df['thumbsUpCount'].max()
df['rating_score'] = df.apply(
    lambda x: x['score'] if x['thumbsUpCount'] < 5 else x['score'] * x['thumbsUpCount_norm'],
    axis=1
)
```

---

### 4.2 Normalisasi Rating Score

- Menggunakan **MinMaxScaler** untuk menormalkan `rating_score` agar berada dalam rentang 0 hingga 1.

```python
from sklearn.preprocessing import MinMaxScaler

scaler = MinMaxScaler()
df['rating_score_normalized'] = scaler.fit_transform(df[['rating_score']])
```

---

### 4.3 Perhitungan Total Score

- Membuat skor akhir (`total_score`) dengan kombinasi:
  - **80%** dari hasil analisis sentimen (polarity)
  - **20%** dari normalized rating score

```python
df['total_score'] = 0.8 * df['polarity_score'] + 0.2 * df['rating_score_normalized']
```

---

### 4.4 Penentuan Final Sentiment

- Menentukan label sentimen akhir berdasarkan nilai `total_score`:
  - `score > 0.5` → **Positive**
  - `score < -0.5` → **Negative**
  - Selain itu → **Neutral**

```python
def final_sentiment(score):
    if score > 0.5:
        return 'positive'
    elif score < -0.5:
        return 'negative'
    else:
        return 'neutral'

df['final_sentiment'] = df['total_score'].apply(final_sentiment)
```

---

### 4.5 Visualisasi Wordcloud

- Membuat Wordcloud terpisah untuk masing-masing sentimen (**positive**, **neutral**, **negative**) berdasarkan kolom `text_akhir`.

```python
from wordcloud import WordCloud
import matplotlib.pyplot as plt

fig, axes = plt.subplots(1, 3, figsize=(20, 6))
sentiments = ['positive', 'neutral', 'negative']

for i, sentiment in enumerate(sentiments):
    text = ' '.join(df[df['final_sentiment'] == sentiment]['text_akhir'].astype(str))
    wordcloud = WordCloud(width=800, height=400, background_color='white').generate(text)
    axes[i].imshow(wordcloud, interpolation='bilinear')
    axes[i].axis('off')
    axes[i].set_title(f'{sentiment.capitalize()} Sentiment', fontsize=16)

plt.tight_layout()
plt.show()
```

---
