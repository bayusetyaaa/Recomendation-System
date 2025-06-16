# Recomendation System 
## Project Overview
**Latar Belakang :**

Dalam era digital saat ini, volume konten hiburan yang tersedia secara online tumbuh secara eksponensial. Platform seperti Netflix, Disney+, dan lainnya menyediakan ribuan pilihan film dan serial yang terus bertambah setiap hari. Namun, banyaknya pilihan justru menjadi tantangan tersendiri: pengguna sering merasa kewalahan dan kesulitan dalam menemukan tayangan yang sesuai dengan minat mereka. Hal ini berdampak pada turunnya pengalaman pengguna dan menurunnya loyalitas terhadap platform.

**Mengapa Project ini penting :**

1. **Pengalaman Pengguna yang Lebih Personal**
   Sistem rekomendasi yang baik membantu menyajikan konten yang sesuai dengan preferensi pengguna, tanpa harus mencarinya secara manual.

2. **Efisiensi Navigasi Konten**
   Dengan menyaring konten berdasarkan minat dan riwayat interaksi, pengguna dapat menghemat waktu dalam menemukan tayangan yang diinginkan.

## Business Undrestanding

**Problem Statement :**

1. **Bagaimana mengidentifikasi kesamaan antara film untuk menyarankan konten yang sesuai bagi pengguna?**

   * Menggunakan fitur-fitur seperti genre, popularitas, dan tahun rilis untuk menemukan keterkaitan antar film.

2. **Apa pendekatan terbaik untuk menyusun rekomendasi yang tidak monoton atau terlalu seragam?**

   * Dibutuhkan strategi untuk memperkenalkan keberagaman konten, agar pengguna tidak hanya mendapat saran dari genre atau tipe film yang sama.

3. **Bagaimana menyusun sistem yang tetap relevan meskipun pengguna tidak memberikan banyak umpan balik (seperti rating)?**

   * Fokus pada pendekatan berbasis konten (content-based) yang tidak sepenuhnya bergantung pada interaksi pengguna.

**Goals :**

1. **Membuat prototipe sistem rekomendasi yang mampu mengenali karakteristik film secara otomatis** dan menyarankan tayangan berdasarkan kedekatan atribut.
2. **Menerapkan teknik pengukuran kesamaan (similarity)** untuk menilai sejauh mana satu film mirip dengan film lainnya menggunakan data struktural.
3. **Mengintegrasikan elemen keberagaman dalam sistem rekomendasi**, agar pengguna tidak merasa jenuh dengan saran yang terlalu mirip secara berturut-turut.

## Data Understanding
Data yang digunakan adalah **MovieLens Latest Small Dataset** yang tersedia di Kaggle:
🔗 [https://www.kaggle.com/datasets/grouplens/movielens-latest-small](https://www.kaggle.com/datasets/grouplens/movielens-latest-small)

Dataset ini berisi informasi mengenai:

* **movies.csv** – data film termasuk movieId, title, dan genres
* **ratings.csv** – data rating pengguna terhadap film (userId, movieId, rating, timestamp)
* **tags.csv** – data tag yang diberikan pengguna terhadap film
* **links.csv** – penghubung antara movieId dan ID film dari sumber eksternal seperti IMDB dan TMDb

## Univariate Exploratory Data Analysis
**Variabel-variabel pada MovieLens Latest Dataset adalah sebagai berikut:**

* **movies** : merupakan metadata dari setiap film, yang mencakup informasi seperti judul dan genre.
* **links** : merupakan ID referensi eksternal film yang terhubung ke situs IMDb dan TMDb.
* **tags** : merupakan kata kunci atau deskripsi tambahan yang diberikan oleh pengguna terhadap film tertentu.
* **ratings** : berisi data penilaian (rating) yang diberikan pengguna terhadap film, termasuk userId, movieId, rating, dan timestamp.

Data yang tersedia memang terlihat cukup sederhana, namun cukup representatif untuk membangun sistem rekomendasi. Dalam tahap awal ini, kita akan mengambil sampel berdasarkan kombinasi movieId dan userId dari file **ratings.csv**.

1. Dataset movies memiliki **9742 entri (baris)** dan **3 kolom**, yaitu:
   * movieId : ID unik untuk setiap film dengan tipe data int64
   * title : Judul film dengan tipe data object (string)
   * genres : Genre film dengan tipe data object (string), berisi satu atau lebih genre yang dipisahkan dengan simbol |

2. Dataset links memiliki **9742 entri** dan **3 kolom**, yaitu:
   * movieId : ID film yang sama dengan movieId pada dataset movies (tipe int64)
   * imdbId : ID film pada basis data **IMDb** (tipe int64)
   * tmdbId : ID film pada basis data **TMDb (The Movie Database)** (tipe float64)

3. Dataset tags memiliki **3683 entri** dan **4 kolom**, yaitu:
   * userId : ID pengguna yang memberikan tag (tipe int64)
   * movieId : ID film yang diberi tag (tipe int64)
   * tag : Tag atau label yang diberikan pengguna untuk film tersebut (tipe object)
   * timestamp : Waktu saat tag diberikan dalam bentuk timestamp UNIX (tipe int64)
  
4. Dataset rating terdiri atas userId, movieId, rating dan timestamp dengan:
   * Jumlah pengguna unik (userId) yang memberikan rating adalah **610**
   * Jumlah film unik (movieId) yang telah diberi rating adalah **9724**
   * Total jumlah rating yang tercatat dalam dataset adalah **100,836**
  
## Data Preprocessing
### Menggabungkan Data dengan Fitur Nama film
mendefinisikan dataframe rating ke dalam variabel all_movie_rate

![image](https://github.com/user-attachments/assets/9d50fd29-4075-4224-8dfb-df9aa2da51ce)

Menggabungkan DataFrame all_movie_rate dengan DataFrame movies berdasarkan kolom movieId menggunakan metode left join, sehingga setiap baris rating akan dilengkapi dengan informasi judul film (title).

![image](https://github.com/user-attachments/assets/6dc4386a-ebcd-4170-9b80-d61d773b82f2)

### Menggabungkan Data Rating dengan Fitur Genre Film
Menggabungkan Data Rating dengan Fitur Genre Film

![image](https://github.com/user-attachments/assets/07bed82b-b270-45ac-ae09-7be10eb76e1e)

Melakukan pengelompokan data berdasarkan movieId dan title, kemudian menghitung jumlah total rating untuk setiap film. Hasilnya diurutkan dari yang memiliki total rating tertinggi ke yang terendah.

![image](https://github.com/user-attachments/assets/e1a30364-8faf-4ac3-a8f8-bc614ca89335)

## Data Preparation
### Mengatasi Missing Value

![image](https://github.com/user-attachments/assets/2ea4ab17-9b86-4300-a655-331a5f728a55)

berdasarkan hasil pengecekan dataframe movies_ratings dapat diinfokan bahwa tidak terdapat missing value

![image](https://github.com/user-attachments/assets/022280d5-93d2-47ae-be9f-1470d86cd1a6)

berdasarkan hasil pengecekan dataframe all_movie_name dapat diinfokan bahwa tidak terdapat missing value

### Menggabungkan Data Rating dengan Fitur Genre Film
Membuat variabel preparation yang berisi dataframe movies_ratings tanpa nilai movieId yang duplikat

![image](https://github.com/user-attachments/assets/e1c20b27-8a5d-4330-9da1-846e67175549)

Membuang data duplikat pada variabel preparation

![image](https://github.com/user-attachments/assets/8a4e6a0f-7e0d-42a0-9ad3-33c969c3ea54)

Mengonversi kolom movieId, title, dan genres dari DataFrame preparation menjadi list untuk memudahkan pemrosesan data lebih lanjut.

![image](https://github.com/user-attachments/assets/3f5efa8e-97ed-4e58-8bc0-271aae5cf103)

Membuat dictionary untuk data movie_id, movie_title, dan movie_genres

![image](https://github.com/user-attachments/assets/296eb1fa-2175-4fd0-bef7-f44d23415bf4)

## Model Development Content Based Filtering
### TF-IDF Vectorizer
Untuk menganalisis dan merepresentasikan genre film secara numerik, digunakan teknik **TF-IDF Vectorizer** (Term Frequency-Inverse Document Frequency). Teknik ini termasuk ke dalam metode pemrosesan teks yang digunakan untuk mengubah data kategori teks (dalam hal ini genre film) menjadi bentuk numerik yang bisa dipahami oleh mesin. Tujuannya adalah untuk mengetahui seberapa penting suatu genre dalam konteks masing-masing film, bukan hanya berdasarkan keberadaannya, tetapi juga seberapa khas genre tersebut dibandingkan dengan seluruh kumpulan film. Hasil representasi ini kemudian bisa digunakan untuk berbagai keperluan analisis lanjutan, seperti klasifikasi film, pengelompokan film berdasarkan kemiripan genre, hingga pengembangan **sistem rekomendasi berbasis konten** yang mampu menyarankan film dengan genre serupa.

Dalam penerapannya, setiap genre akan diberi bobot berdasarkan dua hal: seberapa sering genre tersebut muncul dalam sebuah film (term frequency), dan seberapa umum genre itu muncul dalam seluruh koleksi film (inverse document frequency). Genre yang muncul dalam banyak film akan diberi bobot lebih rendah karena dianggap kurang informatif, sedangkan genre yang unik atau jarang muncul akan mendapatkan bobot lebih tinggi karena lebih representatif terhadap film tertentu.

Dengan menggunakan TF-IDF Vectorizer, genre film yang awalnya berupa teks seperti “Action|Adventure|Sci-Fi” akan diubah menjadi vektor angka, di mana masing-masing angka menunjukkan tingkat kepentingan (bobot) dari genre tersebut terhadap filmnya. Transformasi ini memudahkan algoritma machine learning untuk memahami dan mengolah informasi genre sebagai fitur numerik, memungkinkan analisis yang lebih akurat dan berbasis data.

1. nisialisasi dan Pelatihan TF-IDF Vectorizer

   ![Screenshot 2025-06-17 014149](https://github.com/user-attachments/assets/375df28c-8105-427e-a8b4-97bdd5ab3ce1)

   * Inisialisasi TfidfVectorizer() dilakukan untuk membuat objek vectorizer yang bertugas mengubah teks menjadi representasi numerik berdasarkan bobot TF-IDF.
   * Fungsi .fit() diterapkan pada kolom 'movie_genres' untuk menghitung Inverse Document Frequency dari setiap genre film.\
   * Fungsi .get_feature_names_out() menampilkan daftar genre unik yang diidentifikasi sebagai fitur—terdapat 24 genre seperti 'action', 'comedy', 'drama', dll.

2. Transformasi Genre Menjadi Matriks TF-IDF

   ![Screenshot 2025-06-17 014156](https://github.com/user-attachments/assets/f2892c95-2891-4ec3-b3b6-9aa4b91882ef)

   * fit_transform() digunakan untuk mengubah data genre menjadi matriks TF-IDF, yaitu representasi bobot genre dalam bentuk angka.
   * tfidf_matrix.shape menghasilkan ukuran (9724, 24) yang berarti 9724 baris = jumlah film, 24 kolom = jumlah genre unik hasil ekstraksi TF-IDF.

3. Konversi Matriks TF-IDF ke Bentuk Dense

  ![Screenshot 2025-06-17 014203](https://github.com/user-attachments/assets/ea86b72b-7fdf-4e08-9a05-0dc008d0aa57)

   * Matriks TF-IDF bersifat sparse (mayoritas bernilai nol). Oleh karena itu, .todense() digunakan untuk mengubahnya ke bentuk matriks penuh agar mudah dibaca.
   * Nilai-nilai pada matriks menunjukkan bobot TF-IDF dari genre terhadap film tertentu. Nilai tinggi berarti genre tersebut lebih representatif untuk film itu.

4. Visualisasi TF-IDF dalam DataFrame

   ![Screenshot 2025-06-17 014236](https://github.com/user-attachments/assets/c8c5f075-4cd1-4007-a973-5d501d9f7f83)

   * Hasil TF-IDF dimasukkan ke dalam DataFrame, dengan baris: judul film (movie_title), kolom: genre unik hasil tokenisasi, nilai: bobot TF-IDF genre pada masing-masing film
   * Fungsi .sample(21, axis=1).sample(10, axis=0) digunakan untuk menampilkan 21 genre dan 10 film secara acak agar tabel tidak terlalu besar.
   * Dari visualisasi ini terlihat bahwa tidak semua genre muncul di setiap film, dan genre yang muncul memiliki bobot yang berbeda tergantung pada seberapa umum genre tersebut di seluruh dataset.
  
### Cosine Similarity
Pada tahap ini, dilakukan perhitungan **cosine similarity** terhadap matriks TF-IDF yang telah dibuat sebelumnya untuk mengetahui tingkat kemiripan antar film berdasarkan genre. Fungsi cosine_similarity(tfidf_matrix) menghasilkan matriks berukuran 9724 x 9724, di mana setiap nilai menunjukkan seberapa mirip satu film dengan film lainnya dalam hal distribusi genre. Nilai tertinggi (1.0) terdapat pada diagonal utama karena setiap film pasti identik dengan dirinya sendiri, sementara nilai di luar diagonal menunjukkan tingkat kemiripan relatif antar film. Matriks tersebut kemudian dikonversi menjadi DataFrame dengan baris dan kolom berupa judul film, sehingga memudahkan analisis dan pencarian film-film yang memiliki genre serupa, misalnya untuk keperluan sistem rekomendasi berbasis konten. 

atahapannya adalah sebagai berikut

1. Menghitung cosine similarity pada matrix tf-idf

   ![image](https://github.com/user-attachments/assets/986c9b10-6487-48e3-83e9-32b1af1edc9d)

2. Membuat dataframe dari variabel cosine_sim dengan baris dan kolom berupa judul film

   ![image](https://github.com/user-attachments/assets/36aa23d7-10ea-437e-a483-5ec59415a4d0)


### Mendapatkan Rekomendasi
Pada tahap ini, dibuat sebuah fungsi bernama movie_recommendations() yang bertujuan untuk memberikan rekomendasi film berdasarkan kemiripan genre menggunakan pendekatan **TF-IDF** dan **cosine similarity**. Fungsi ini menerima input berupa judul film, data kemiripan (dalam bentuk DataFrame dari cosine similarity), data metadata film (judul dan genre), serta jumlah film rekomendasi (k) yang ingin ditampilkan. Ketika pengguna memasukkan judul film, seperti *"Good Time (2017)"* yang diketahui memiliki genre **Crime** dan **Drama**, fungsi akan mengambil skor kemiripan dari baris yang sesuai dalam cosine_sim_df, mengurutkannya dari nilai tertinggi ke terendah (dengan mengecualikan film itu sendiri), lalu menampilkan lima film teratas yang paling mirip berdasarkan skor cosine similarity. Dengan pendekatan ini, sistem berhasil menyarankan film-film yang memiliki distribusi genre serupa, sehingga dapat digunakan dalam pengembangan sistem rekomendasi berbasis konten secara efektif.

![image](https://github.com/user-attachments/assets/80586d10-dd1f-4b4e-ade5-7a7e07dc1d81)

Menampilkan 5 rekomendasi film yang paling mirip dengan "Good Time (2017)" berdasarkan kemiripan genre menggunakan pendekatan TF-IDF dan cosine similarity.

## Model Development dengan Collaborative Filtering
### Data Preparation
Dalam tahap awal pemrosesan data untuk sistem rekomendasi berbasis pembelajaran mesin atau deep learning, dilakukan encoding terhadap kolom **userId** dan **movieId** menjadi bentuk numerik. Hal ini diperlukan karena sebagian besar model hanya menerima input dalam format numerik atau tensor.


1. Mengonversi userId ke dalam list unik dan Encoding userId ke indeks numerik

   ![image](https://github.com/user-attachments/assets/64d28e42-893d-4264-a8c4-0a1d9dcb1db8)

   * Fungsi df['userId'].unique().tolist() digunakan untuk mengambil semua userId tanpa duplikasi.
   * Hasilnya adalah 610 user unik.
   * Dibuat dictionary user_to_user_encoded untuk mengubah userId menjadi angka (0–609).
   * Sebaliknya, user_encoded_to_user menyimpan mapping balik (dari angka ke userId) untuk interpretasi hasil.

2. Proses serupa dilakukan untuk movieId

   ![image](https://github.com/user-attachments/assets/09fab7ad-afbf-4b11-89ee-c5401bf13a81)

   * Diperoleh list movie_ids berisi ID film unik.
   * Dibuat dua dictionary: movie_to_movie_encoded dan movie_encoded_to_movie untuk encoding dan decoding.

4. Mapping hasil encoding ke dataframe utama

   ![image](https://github.com/user-attachments/assets/cb870c6e-afad-4185-8b2a-8998433c280b)

   * Kolom baru user dan movie ditambahkan ke df, masing-masing berisi versi encoded dari userId dan movieId.

### Membagi Data untuk Training dan Validasi

![image](https://github.com/user-attachments/assets/d2f35768-c9a0-4938-95d3-4fa50fcc98b8)

Pada tahap ini, data diacak terlebih dahulu menggunakan fungsi sample(frac=1, random_state=42) untuk mengacak seluruh baris dalam DataFrame secara acak namun tetap reproducible, sehingga hasil pengacakan akan selalu sama jika kode dijalankan ulang. Selanjutnya, dibuat variabel input x yang berisi pasangan nilai numerik dari kolom user dan movie, hasil encoding sebelumnya. Sedangkan variabel target y dibuat dengan menormalisasi nilai rating ke dalam rentang 0 hingga 1 menggunakan metode *min-max normalization*. Proses ini penting agar skala rating sesuai untuk digunakan dalam model pembelajaran mesin. Setelah itu, data dibagi menjadi dua bagian, yaitu 80% untuk data latih (x_train, y_train) dan 20% untuk data validasi (x_val, y_val). Pembagian ini dilakukan dengan slicing berdasarkan jumlah baris dalam data, agar model bisa dilatih menggunakan satu bagian data dan diuji performanya pada data yang tidak dilihat selama pelatihan.

### Evaluasi
Model rekomendasi dibangun menggunakan kelas RecommenderNet, turunan dari tf.keras.Model. Tujuan utama dari arsitektur ini adalah mempelajari hubungan laten antara pengguna dan film dalam bentuk vektor berdimensi rendah yang dapat dihitung kedekatannya (similarity). Representasi ini diperoleh melalui **embedding layer**, di mana setiap pengguna dan film dipetakan ke dalam ruang fitur tersembunyi (latent space) dengan dimensi tertentu, dalam hal ini 50.

Model memiliki empat layer embedding, yaitu dua untuk representasi vektor (user_embedding, movie_embedding) dan dua lainnya untuk mengakomodasi nilai **bias** tiap entitas (user_bias, movie_bias). Bias ini penting untuk menangkap kecenderungan umum dari pengguna atau film tertentu, seperti pengguna yang cenderung memberi rating tinggi, atau film yang umumnya disukai.

Saat model dijalankan (method call()), pasangan [user_id, movie_id] digunakan untuk mengekstrak embedding masing-masing entitas. Embedding user dan film kemudian dikalikan dengan operasi **dot product** menggunakan tf.tensordot(), yang menghasilkan skor mentah interaksi antara keduanya. Skor ini kemudian disesuaikan dengan menambahkan nilai bias dari masing-masing sisi.

Akhirnya, skor tersebut dilewatkan melalui fungsi aktivasi **sigmoid**, yang akan memetakan hasilnya ke rentang [0, 1]. Nilai inilah yang akan ditafsirkan sebagai kemungkinan bahwa pengguna akan menyukai film tersebut. Skala ini juga cocok karena sebelumnya data rating telah dinormalisasi ke dalam rentang yang sama.

Setelah arsitektur selesai dibuat, model dikompilasi dengan konfigurasi sebagai berikut:

- **Loss function**: BinaryCrossentropy. Digunakan untuk mengukur seberapa jauh prediksi dari nilai aktual dalam bentuk probabilistik. Karena skor dipetakan ke [0,1], fungsi ini cocok digunakan dalam konteks prediksi preferensi.
- **Optimizer**: Adam dengan learning rate 0.001. Optimizer ini dipilih karena kemampuannya yang adaptif dalam mengatur laju pembelajaran, serta kestabilannya dalam konvergensi pada data yang memiliki noise atau sparcity tinggi seperti sistem rekomendasi.
- **Metrik evaluasi**: RootMeanSquaredError (RMSE). RMSE menghitung rata-rata kesalahan prediksi dan memberikan penalti lebih besar pada kesalahan besar. Cocok digunakan untuk sistem yang memprediksi angka kontinyu seperti rating.

Model kemudian dilatih selama **25 epoch** dengan **batch size 32**, artinya dalam setiap iterasi model memproses 32 sampel sekaligus. Pengaturan ini memberikan efisiensi yang baik antara kecepatan komputasi dan kualitas pembelajaran. Epoch sebanyak 25 kali dianggap cukup untuk membiarkan model menemukan pola dalam data tanpa terlalu lama dilatih, yang bisa menimbulkan **overfitting**.

Selama proses pelatihan, performa model pada data latih dan data validasi dievaluasi setiap epoch menggunakan RMSE. Nilai RMSE yang menurun secara stabil pada kedua dataset menunjukkan bahwa model belajar dengan baik. Visualisasi hasil pelatihan (plot RMSE) memberikan gambaran apakah model mengalami penurunan error secara bertahap, serta apakah terdapat jarak yang besar antara data train dan validation yang bisa mengindikasikan overfitting.

![download (6)](https://github.com/user-attachments/assets/2af791a9-5982-4237-8f36-fd2c8175f507)

Grafik menunjukkan bahwa nilai RMSE pada data training terus menurun seiring bertambahnya epoch, sementara nilai RMSE pada data validation cenderung stabil setelah beberapa epoch. Ini menandakan model berhasil mempelajari pola dari data.

### Mendapatkan Rekomendasi Film

Pada tahap ini, dilakukan proses pembuatan rekomendasi film untuk satu pengguna secara acak dengan terlebih dahulu mengidentifikasi film yang **belum ditonton** oleh pengguna tersebut. Langkah pertama dimulai dengan memilih satu `userId` secara acak dari dataset `df`, kemudian sistem mencari daftar `movieId` yang **belum pernah ditonton** oleh pengguna tersebut dengan menggunakan operator bitwise `~` untuk mengecualikan film yang sudah pernah dinilai. Setelah itu, hanya ID film yang sudah melalui proses encoding sebelumnya yang dipertahankan agar sesuai dengan struktur input model. Setiap `movieId` yang belum ditonton tersebut kemudian dipasangkan dengan `userId` yang telah di-encode, membentuk array dua kolom (`user`, `movie`) untuk prediksi. Model yang sudah dilatih sebelumnya digunakan untuk memprediksi rating dari semua kombinasi user-movie ini, lalu hasil prediksi diurutkan untuk mendapatkan 10 film dengan skor prediksi tertinggi. Sebelum menampilkan rekomendasi, sistem juga menampilkan 5 film dengan rating tertinggi yang sudah pernah ditonton oleh user tersebut sebagai referensi. Terakhir, sistem mencetak daftar 10 rekomendasi film berdasarkan prediksi skor tertinggi, lengkap dengan judul dan genre, dari dataframe `movie_df`. Proses ini mencerminkan pendekatan sistem rekomendasi berbasis prediksi skor individual untuk item yang belum dilihat pengguna.

![image](https://github.com/user-attachments/assets/407db68f-3682-43b8-9650-d2346e7a822c)

Berikut kesimpulan akhir berdasarkan output rekomendasi untuk user dengan userId 376:

Dari hasil implementasi sistem rekomendasi menggunakan pendekatan **Collaborative Filtering**, kita berhasil memberikan rekomendasi yang relevan dan sesuai dengan preferensi pengguna. Dalam contoh ini, sistem menghasilkan rekomendasi untuk pengguna dengan userId 376, yang sebelumnya memberikan rating tinggi pada film bergenre *Sci-Fi*, *Drama*, *Thriller*, dan *War*, seperti **Twelve Monkeys**, **Apollo 13**, dan **Terminator 2**.

Rekomendasi yang diberikan pun menunjukkan konsistensi dengan pola preferensi pengguna. Film seperti **Band of Brothers**, **Paths of Glory**, dan **The Stunt Man** memiliki elemen genre yang selaras, terutama *Drama*, *War*, dan *Thriller*. Selain itu, sistem juga menyarankan beberapa film dari genre lain seperti *Comedy* dan *Romance*, menunjukkan bahwa model mampu mengenali kemungkinan preferensi tambahan berdasarkan kemiripan perilaku pengguna lain yang serupa.

Secara keseluruhan, hasil ini menunjukkan bahwa sistem **Collaborative Filtering** telah mampu mengidentifikasi pola dan memberikan rekomendasi film yang dipersonalisasi dengan baik, tanpa memerlukan data konten dari masing-masing film secara langsung. Keunggulan pendekatan ini terletak pada kemampuannya untuk menemukan kesamaan antar pengguna dan menggunakannya sebagai dasar rekomendasi, menjadikannya solusi yang efektif untuk sistem dengan interaksi pengguna yang cukup besar.
