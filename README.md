# Laporan Proyek Machine Learning - Fathur Rahman Al Farizy

## Project Overview

### Latar Belakang

Perkembangan industri buku yang pesat telah menghasilkan ribuan judul baru setiap tahunnya. Pengguna sering kali mengalami kesulitan dalam menemukan buku yang sesuai dengan minat mereka karena banyaknya pilihan yang tersedia. Sistem rekomendasi hadir sebagai solusi penting untuk mempersonalisasi pengalaman membaca dan membantu pengguna menemukan buku yang relevan.

### Tujuan

- Membangun sistem rekomendasi buku yang efektif.
- Meningkatkan kepuasan pengguna dengan memberikan rekomendasi yang relevan.

## Business Understanding

### Problem Statements

- Pengguna kesulitan menemukan buku yang sesuai dengan minat.
- Banyaknya pilihan buku membuat pengguna kewalahan.
- Kurangnya personalisasi dalam penawaran buku.

### Goals

- Memberikan rekomendasi buku yang dipersonalisasi.
- Meningkatkan keterlibatan pengguna dengan rekomendasi yang relevan.
- Membantu pengguna menemukan buku baru yang menarik.

### Solution Approach

- Menggunakan metode Content-Based Filtering (CBF).
- Menggunakan cosine similarity untuk mengukur kemiripan antar buku.

## Data Understanding

### Deskripsi Dataset

Dataset berasal dari [Goodbooks-10k](https://www.kaggle.com/datasets/zygmunt/goodbooks-10k). Dataset ini berisi informasi tentang 10000 buku dan lebih dari 900.000 pengguna. Dataset ini terdiri dari beberapa file:

- **`books.csv`**: File ini berisi informasi detail mengenai setiap buku. data books terdiri dari 10.000 baris dan 22 kolom. Setelah dilakukan analisis awal, ditemukan bahwa dataset ini memiliki beberapa nilai yang hilang (missing values) pada kolom-kolom seperti `isbn`, `isbn13`, `original_title`, `original_publication_year`, dan `language_code`. Selain itu, tidak ditemukan adanya data duplikat berdasarkan `book_id`. Berikut adalah deskripsi lengkap untuk setiap variabel:

  - `book_id`: ID unik yang mengidentifikasi setiap buku dalam dataset. Tipe datanya adalah integer.
  - `goodreads_book_id`: ID buku yang sesuai dengan platform Goodreads. Tipe datanya adalah integer.
  - `best_book_id`: ID buku terbaik menurut Goodreads. Tipe datanya adalah integer.
  - `work_id`: ID unik untuk edisi karya buku. Tipe datanya adalah integer.
  - `books_count`: Jumlah edisi buku yang berbeda. Tipe datanya adalah integer.
  - `isbn`: Nomor ISBN buku (International Standard Book Number). Tipe datanya adalah objek (string), terdapat nilai yang hilang.
  - `isbn13`: Nomor ISBN-13 buku. Tipe datanya adalah objek (string), terdapat nilai yang hilang.
  - `authors`: Nama penulis buku. Tipe datanya adalah objek (string).
  - `original_publication_year`: Tahun publikasi asli buku. Tipe datanya adalah float, terdapat nilai yang hilang.
  - `original_title`: Judul asli buku (jika berbeda dengan judul saat ini). Tipe datanya adalah objek (string), terdapat nilai yang hilang.
  - `title`: Judul buku. Tipe datanya adalah objek (string).
  - `language_code`: Kode bahasa buku. Tipe datanya adalah objek (string), terdapat nilai yang hilang.
  - `average_rating`: Rata-rata rating buku dari semua pengguna. Tipe datanya adalah float.
  - `ratings_count`: Jumlah rating yang diberikan untuk buku ini. Tipe datanya adalah integer.
  - `work_ratings_count`: Jumlah total rating untuk semua edisi karya buku. Tipe datanya adalah integer.
  - `work_text_reviews_count`: Jumlah total _text review_ untuk semua edisi karya buku. Tipe datanya adalah integer.
  - `ratings_1`: Jumlah rating bintang 1 yang diberikan. Tipe datanya adalah integer.
  - `ratings_2`: Jumlah rating bintang 2 yang diberikan. Tipe datanya adalah integer.
  - `ratings_3`: Jumlah rating bintang 3 yang diberikan. Tipe datanya adalah integer.
  - `ratings_4`: Jumlah rating bintang 4 yang diberikan. Tipe datanya adalah integer.
  - `ratings_5`: Jumlah rating bintang 5 yang diberikan. Tipe datanya adalah integer.
  - `image_url`: URL gambar sampul buku berukuran kecil. Tipe datanya adalah objek (string).
  - `small_image_url`: URL gambar sampul buku berukuran sangat kecil. Tipe datanya adalah objek (string).

- **`ratings.csv`**: File ini berisi informasi mengenai rating yang diberikan oleh pengguna untuk buku. Data ratings memiliki 3 kolom dan 981.756 baris. Seluruh kolomnya bertipe int64 dan tidak ditemukan adanya nilai yang hilang atau data duplikat berdasarkan kombinasi `user_id` dan `book_id`. Fitur-fitur dalam file ini adalah:

  - `user_id`: ID unik yang mengidentifikasi setiap pengguna yang memberikan rating.
  - `book_id`: ID buku yang diberi rating oleh pengguna.
  - `rating`: Rating yang diberikan oleh pengguna untuk buku (skala 1-5).

- **`book_tags.csv`**: File ini berfungsi sebagai jembatan yang menghubungkan setiap buku dengan tag yang relevan. Data book_tags memiliki 3 kolom dan 999.912 baris. Seluruh kolomnya bertipe int64 dan tidak ditemukan nilai yang hilang. Fitur-fiturnya adalah:

  - `goodreads_book_id`: ID buku dari Goodreads
  - `tag_id`: ID unik untuk setiap tag.
  - `count`: Berapa kali tag tersebut diberikan ke buku

- **`tags.csv`**: File ini berisi daftar lengkap ID tag dan nama tag yang sesuai. Data tags memiliki 2 kolom dan 34.252 baris dan tidak ditemukan adanya nilai yang hilang atau data duplikat berdasarkan `tag_id`. Fitur-fiturnya adalah:

  - `tag_id`: ID unik untuk setiap tag. Tipe datanya adalah integer.
  - `tag_name`: Nama deskriptif dari tag. Tipe datanya adalah objek (string).

- **`to_read.csv`**: File ini mencatat buku-buku yang masuk dalam daftar "ingin dibaca" setiap pengguna. `to_read` memiliki 2 kolom dan 912.705 baris. Seluruh kolomnya bertipe int64 dan tidak terdapat missing values. Fitur-fiturnya adalah:
  - `user_id`: ID unik pengguna yang menambahkan buku ke daftar "ingin dibaca".
  - `book_id`: ID buku yang ditambahkan ke daftar "ingin dibaca".

## Data Preparation

Tahapan _data preparation_ yang dilakukan meliputi:

1.  **Penggabungan Data:**
    Pada tahap ini akan dilakukan penggabungan data. Hal yang akan digabungkan pada dataset ini adalah data deskriptif buku (books_cb) dan juga data rating (ratings_cb). Permodelan akan menggunakan data deskriptif buku dan evaluasinya akan melibatkan juga data rating dari pengguna.

    **1.1 books_cb**

    - Merge book_tags dengan tags berdasarkan tag_id untuk dapatkan nama tag.
    - Group tags berdasarkan goodreads_book_id dan gabungkan tag_name jadi 1 string per buku
    - Gabungkan tags ke books berdasarkan book_id.
    - Plih kolom penting ('book_id', 'title', 'authors', 'average_rating', 'tags') dan hapus buku yang tidak punya tag.
    - Memastikan kembali data yang digabungkan tidak memiliki missing value dan data duplikat.

    **1.2 ratings_cb**

    - Ambil data rating yang diberikan user pada buku-buku yang terdapat pada data buku yang sudah digabungkan datanya sebelumnya.
    - Hapus user yang merating nuku kurang dari 3 rating dan hapus buku dengan jumlah rating yang diberikan kurang dari 5 rating untuk mengurangi noise.
    - Memeriksa apakah ada user memberi rating ke buku yang sama lebih dari sekali dan jika ada maka berikan nilai rata-rata ratingnya.
    - Memeriksa missing value dan setelah diperiksa tidak terdapat missing value.

2.  **Ekstraksi Fitur (untuk CBF):**
    - Menggunakan TF-IDF (_Term Frequency-Inverse Document Frequency_) untuk mengekstraksi fitur dari kolom 'tags'.
    - TF-IDF memberikan bobot pada setiap tag berdasarkan frekuensinya dalam dokumen dan invers dari frekuensi dokumen tag di seluruh korpus.
    - Ini membantu mengidentifikasi tag yang paling relevan untuk setiap buku.

Alasan dilakukannya tahapan ini:

- Penggabungan data diperlukan untuk menggabungkan informasi buku dengan tagnya, yang penting untuk CBF.
- Ekstraksi fitur TF-IDF mengubah data tekstual menjadi representasi numerik yang dapat diproses oleh model.

## Modeling

**Content-Based Filtering (CBF)**  
Metode yang digunakan untuk membangun model sistem rekomendasi kali ini adalah content-based filtering. Cara kerja dari metode ini adalah dengan merekomendasikan item yang memiliki fitur serupa dengan item yang disukai pengguna di masa lalu. Fitur-fitur ini bisa berupa genre, penulis, deskripsi, atau dalam kasus ini, tag buku. Dengan begitu sistem rekomendasi ini bisa digunakan untuk merekomendasikan buku baru yang belum pernah dibaca oleh pengguna manapun. Pengguna yang baru memulai kebiasaan baru membaca buku dan masih belum tahu tentang beragam judul buku bisa mendapatkan rekomendasi berdasarkan fitur buku tersebut.

**Cosine Similarity:**

- Menghitung _cosine similarity_ antara vektor TF-IDF dari setiap buku untuk mengukur kemiripan konten.
- _Cosine similarity_ menghasilkan nilai antara 0 dan 1, di mana 1 berarti buku-buku tersebut sangat mirip, 0 berarti tidak mirip.

**Rekomendasi:**

- Untuk setiap buku, sistem mengurutkan buku lain berdasarkan _cosine similarity_.
- Sistem merekomendasikan _N_ buku teratas yang paling mirip dengan buku yang disukai pengguna.
- Contoh: Untuk buku "To Kill a Mockingbird", sistem merekomendasikan buku-buku seperti "The Great Gatsby", "The Catcher in the Rye", dll.

**Contoh 10 Rekomendasi Berdasarkan Kemiripannya dengan Buku "To Kill a Mockingbird":**

Berdasarkan implementasi model Content-Based Filtering dengan fitur tag dan perhitungan cosine similarity, berikut adalah 10 buku teratas yang direkomendasikan untuk buku "To Kill a Mockingbird":

1.  _The Great Gatsby_
2.  _The Catcher in the Rye_
3.  _Lord of the Flies_
4.  _Of Mice and Men_
5.  _The Grapes of Wrath_
6.  _Their Eyes Were Watching God_
7.  _The Old Man and the Sea_
8.  _The Scarlet Letter_
9.  _The Adventures of Huckleberry Finn_
10. _The Awakening_

## Evaluation

### Metrik Evaluasi

- Precision@K: Seberapa relevan rekomendasi yang diberikan dalam K teratas.
- Recall@K: Seberapa banyak item relevan berhasil ditemukan dari semua item relevan.
- MAP@K (Mean Average Precision): Rata-rata presisi kumulatif dari rekomendasi yang relevan.

### Analisis Keberagaman Rekomendasi

- Mengukur apakah rekomendasi yang diberikan bervariasi dari segi penulis atau genre.
- Berdasarkan hasil rekomendasi yang diberikan, 9 dari 10 buku dalam rekomendasi memiliki penulis yang berbeda.

### Hasil Evaluasi

Berdasarkan hasil rekomendasi yang diberikan, hasil evaluasi dengan metriknya adalah sebagai berikut:

- Nilai Average Precision@10 sebesar 0.0921 menunjukkan bahwa, secara rata-rata, dari 10 buku yang direkomendasikan kepada setiap pengguna, hanya sekitar 9.21% di antaranya yang relevan.
- Nilai Average Recall@10 sebesar 0.2216 berarti bahwa, secara rata-rata, sistem rekomendasi berhasil menemukan sekitar 22.16% dari total buku relevan yang mungkin disukai oleh pengguna dalam 10 rekomendasi teratas.
- Nilai MAP@10 sebesar 0.0232 adalah nilai rata-rata dari Average Precision untuk semua pengguna. Angka ini sangat rendah, mengkonfirmasi temuan dari Precision@10 bahwa sistem kesulitan dalam memberikan rekomendasi yang tepat dan menempatkannya di urutan atas.

### Kelebihan dan Kekurangan

- Kelebihan:

  - Tidak memerlukan data pengguna lain.
  - Dapat memberikan rekomendasi untuk item baru (cold-start user).

- Kekurangan:
  - Rekomendasi cenderung terbatas pada jenis konten yang mirip.

### Potensi Pengembangan

- Menggabungkan dengan Collaborative Filtering untuk sistem hybrid.
- Menggunakan NLP lanjutan (misalnya BERT) untuk representasi konten buku.
- Menambahkan konteks pengguna seperti genre favorit, histori bacaan, dsb.
