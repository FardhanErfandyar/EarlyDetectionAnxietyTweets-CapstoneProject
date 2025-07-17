# Early Anxiety Detection Using Tweets Dataset with Large Language Model

## Project Overview

Proyek ini bertujuan untuk membangun sebuah sistem cerdas yang mampu menganalisis kumpulan tweet dari seorang pengguna untuk mengklasifikasikannya sebagai "Anxious" (Cemas) atau "Not Anxious" (Tidak Cemas). Berbeda dengan metode statistik tradisional yang mengekstraksi fitur numerik, proyek ini memanfaatkan kemampuan pemahaman kontekstual dari *Large Language Model* (LLM) **openai/o4-mini**. Dengan menggunakan teknik *few-shot prompting* yang, model diberikan instruksi, definisi, dan contoh pembelajaran secara langsung untuk melakukan analisis holistik terhadap jejak digital pengguna, menghasilkan klasifikasi yang disertai penalaran kualitatif yang transparan.

---

## Link Dataset

Dataset yang digunakan adalah hasil crawling data menggunakan tweet-harvest.

**Link Dataset Repositori:** [https://drive.google.com/drive/folders/1qy7ZGMFAfDwSgzimG0KGGQLSXmVlBS7a?usp=sharing](https://drive.google.com/drive/folders/1qy7ZGMFAfDwSgzimG0KGGQLSXmVlBS7a?usp=sharing) 

---

## Insight & findings

Analisis yang dilakukan menghasilkan beberapa temuan kunci mengenai perbedaan pola bahasa antara pengguna cemas dan tidak cemas:

* **Pembedaan Kontekstual:** Model AI berhasil membedakan antara keluhan sehari-hari atau stres situasional (misalnya, frustrasi akademis) dengan pola kecemasan yang lebih persisten dan mengakar (misalnya, kepanikan berulang, keraguan diri, dan pemikiran berlebihan).
* **Pola Perilaku Pengguna Cemas:** Sesuai dengan studi referensi, ditemukan bahwa pengguna dengan indikasi kecemasan menunjukkan pola perilaku spesifik:
    * Mereka cenderung mengirim tweet lebih jarang daripada pengguna lain. 
    * Meskipun lebih jarang, teks yang mereka tulis cenderung lebih panjang. 
    * Bahasa yang digunakan lebih sering mengandung sentimen negatif yang kuat. 
* **Pentingnya Analisis Holistik:** Keberhasilan klasifikasi sangat bergantung pada analisis keseluruhan riwayat tweet pengguna, bukan pada satu tweet tunggal. Ini memungkinkan identifikasi pola yang berulang, yang merupakan indikator kunci dari kondisi kecemasan.
* **Transparansi melalui Penalaran:** Fitur "Reasoning" yang dihasilkan oleh LLM memberikan justifikasi kualitatif yang logis untuk setiap klasifikasi, membuat proses pengambilan keputusan AI menjadi transparan dan mudah dipahami.

---

## AI support explanation

AI berfungsi sebagai analis kontekstual yang menjalankan tugas klasifikasi.

### Metode

Proyek ini menggunakan **Large Language Model (LLM)** yang diarahkan melalui **rekayasa *prompt***. Sebuah *prompt* (perintah) yang sangat terstruktur dibangun untuk setiap analisis, berisi:
1.  **Pemberian Peran:** Menginstruksikan AI untuk bertindak sebagai "analis psikologis".
2.  **Definisi:** Memberikan definisi yang jelas untuk kategori "Anxious" dan "Not Anxious".
3.  **Contoh Pembelajaran (*Few-Shot Learning*):** Memberikan beberapa contoh kasus yang sudah terklasifikasi untuk "mengajari" AI secara instan.

### *Prompt* Final

Berikut adalah templat lengkap dari *prompt* yang dikirimkan ke model AI untuk setiap analisis. Variabel `{example_text}` dan `{user_tweets_str}` diisi secara dinamis oleh skrip.

```python
final_prompt = f"""You are a highly skilled psychological analyst specializing in detecting signs of anxiety from social media text.
Your task is to analyze a collection of recent tweets from a single user and determine if their overall posting pattern indicates potential anxiety.

**Definitions:**
- **Anxious:** The user's tweets, when viewed together, show a recurring pattern of worry, fear, panic, overthinking, social withdrawal, or direct mentions of an anxiety diagnosis. The sentiment is often negative or tense.
- **Not Anxious:** The user's tweets cover a variety of general topics without a recurring pattern of the anxious traits mentioned above.

**Instructions:**
1. Carefully read all the provided tweets from the user.
2. Analyze the overall pattern, tone, and topics. Do not judge based on a single tweet.
3. Provide your reasoning in one short sentence.
4. Provide your final classification in the specified format.

**LEARNING EXAMPLES:**
{example_text}
---
**USER TO CLASSIFY:**
**User's Tweets:**
{user_tweets_str}

**YOUR ANALYSIS:**
- **Reasoning:**
- **Classification:**"""
````
### Konfigurasi Teknis Model

Kinerja model **openai/o4-mini** dioptimalkan menggunakan parameter spesifik untuk memastikan hasil yang akurat dan relevan.

| Parameter            | Nilai | Deskripsi dan Alasan                                                                                                                                                              |
| :------------------- | :---- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `temperature`        | 0.5   | **Deskripsi:** Mengontrol tingkat keacakan output.<br>**Alasan:** Nilai 0.5 dipilih untuk membuat respons AI lebih fokus dan konsisten, yang krusial untuk tugas klasifikasi agar hasilnya dapat diandalkan. |
| `max_new_tokens`     | 150   | **Deskripsi:** Menetapkan batas maksimal jumlah token yang dihasilkan.<br>**Alasan:** Batas 150 token memastikan penalaran yang diberikan tetap ringkas dan langsung ke intinya.             |
| `top_k`              | 50    | **Deskripsi:** Membatasi pilihan kata model pada 50 kata dengan probabilitas tertinggi.<br>**Alasan:** Ini digunakan untuk menjaga agar kalimat yang dihasilkan tetap koheren dan logis.         |
| `top_p`              | 0.95  | **Deskripsi:** Menggunakan metode *nucleus sampling* dari total probabilitas 95%.<br>**Alasan:** Menyeimbangkan antara relevansi dan sedikit variasi agar penalaran terdengar alami.         |
| `repetition_penalty` | 1.1   | **Deskripsi:** Memberikan penalti pada token yang sudah digunakan.<br>**Alasan:** Nilai 1.1 secara aktif mencegah model mengulang kata, mendorongnya untuk menghasilkan kalimat yang lebih dinamis.    |
