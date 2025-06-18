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
**Variabel-variabel pada MovieLens Latest Dataset adalah sebagai berikut:**

* **movies** : merupakan metadata dari setiap film, yang mencakup informasi seperti judul dan genre. dataset memiliki 9742 baris dan 3 kolom
* **links** : merupakan ID referensi eksternal film yang terhubung ke situs IMDb dan TMDb. dataset memiliki 9734 baris dan 3 kolom
* **tags** : merupakan kata kunci atau deskripsi tambahan yang diberikan oleh pengguna terhadap film tertentu. dataset memiliki 3683 baris dan 3 kolom
* **ratings** : berisi data penilaian (rating) yang diberikan pengguna terhadap film, termasuk userId, movieId, rating, dan timestamp. dataset memiliki 9089 baris

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

Pada tahap ini juga dilakukan pengecekan dan penghapusan missing value pada tiap-tiap dataset

![image](https://github.com/user-attachments/assets/8e4d1fe6-ccea-4a9f-b58e-34a60ad4561a)
![image](https://github.com/user-attachments/assets/950acafa-6d52-4647-b2a0-476538a2588c)
![image](https://github.com/user-attachments/assets/7d31e753-1c0a-42ef-9d49-1b945be2f45f)
![image](https://github.com/user-attachments/assets/3fa1c872-fac7-4815-86f0-dac137d63848)

## Data Preprocessing
### Menggabungkan Data Rating dengan Fitur Genre Film
Menggabungkan Data Rating dengan Fitur Genre Film

![image](https://github.com/user-attachments/assets/07bed82b-b270-45ac-ae09-7be10eb76e1e)

Melakukan pengelompokan data berdasarkan movieId dan title, kemudian menghitung jumlah total rating untuk setiap film. Hasilnya diurutkan dari yang memiliki total rating tertinggi ke yang terendah.

![image](https://github.com/user-attachments/assets/e1a30364-8faf-4ac3-a8f8-bc614ca89335)

## Data Preparation
Untuk menganalisis dan merepresentasikan genre film secara numerik, digunakan teknik TF-IDF Vectorizer (Term Frequency-Inverse Document Frequency). Teknik ini termasuk dalam metode pemrosesan teks yang digunakan untuk mengubah data kategori teks (dalam hal ini genre film) menjadi bentuk numerik yang dapat diproses oleh mesin. Tujuannya adalah untuk mengetahui seberapa penting suatu genre dalam konteks masing-masing film—bukan hanya berdasarkan keberadaannya, tetapi juga berdasarkan kekhasannya dibandingkan dengan seluruh kumpulan film. Genre yang umum dan sering muncul dalam banyak film akan diberi bobot lebih rendah karena dianggap kurang informatif, sementara genre yang langka atau unik akan mendapat bobot lebih tinggi karena lebih representatif.

Dalam implementasinya, data genre seperti “Action|Adventure|Sci-Fi” terlebih dahulu diproses dengan mengganti tanda pemisah | menjadi spasi agar sesuai dengan format masukan TF-IDF Vectorizer. Setelah itu, TF-IDF Vectorizer akan mengubah teks genre menjadi vektor numerik, di mana setiap elemen mewakili bobot kepentingan genre tertentu terhadap suatu film. Representasi numerik ini digunakan sebagai fitur konten dalam pengembangan sistem rekomendasi berbasis konten.

Selain itu, dilakukan juga tahap data preparation untuk menyiapkan data yang akan digunakan oleh model. Proses ini mencakup Encoding userId dan movieId secara manual menggunakan mapping dictionary. Setiap nilai unik dari userId dan movieId dipetakan ke angka yang merepresentasikan ID numerik. Hal ini dilakukan agar data kategorikal tersebut dapat diproses oleh model pembelajaran mesin. Pembagian data ke dalam data latih (training) dan data validasi (validation) dilakukan menggunakan metode slicing manual. Data diacak terlebih dahulu (shuffled), lalu dibagi berdasarkan indeks ke dalam dua bagian: sebagian besar digunakan untuk pelatihan, dan sisanya untuk validasi. Teknik ini menggantikan penggunaan fungsi train_test_split.

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

### TF-IDF Vectorizer
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

## Model Development Content Based Filtering
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

### Modeling
Model rekomendasi dibangun menggunakan kelas **RecommenderNet**, turunan dari tf.keras.Model. Tujuan utama dari arsitektur ini adalah mempelajari hubungan **laten** antara pengguna dan film dalam bentuk **vektor berdimensi rendah** yang dapat dihitung skor interaksinya. Representasi ini diperoleh melalui **embedding layer**, di mana setiap pengguna dan film dipetakan ke dalam ruang fitur tersembunyi (**latent space**) dengan dimensi tertentu, dalam hal ini **50**.

Model memiliki **empat layer embedding**, yaitu dua untuk representasi vektor (user_embedding, movie_embedding) dan dua lainnya untuk mengakomodasi nilai **bias** tiap entitas (user_bias, movie_bias). Bias ini penting untuk menangkap kecenderungan umum dari pengguna atau film tertentu, seperti pengguna yang cenderung memberi rating tinggi, atau film yang umumnya disukai.

Saat model dijalankan (melalui method call()), pasangan [user_id, movie_id] digunakan untuk mengekstrak embedding masing-masing entitas. Embedding pengguna dan film kemudian dikalikan menggunakan operasi **dot product** (tf.tensordot()), yang menghasilkan skor mentah dari interaksi antara keduanya. Skor ini kemudian disesuaikan dengan menambahkan nilai **bias** dari masing-masing sisi.

Akhirnya, skor tersebut dilewatkan melalui fungsi aktivasi **sigmoid**, yang memetakan hasilnya ke rentang \[0, 1]. Nilai ini ditafsirkan sebagai kemungkinan bahwa pengguna akan menyukai film tersebut. Skala ini sesuai karena sebelumnya data rating telah dinormalisasi ke dalam rentang yang sama.

Setelah arsitektur selesai dibuat, model dikompilasi dengan konfigurasi sebagai berikut:

* **Loss function**: BinaryCrossentropy. Digunakan untuk mengukur seberapa jauh prediksi dari nilai aktual dalam bentuk probabilistik. Karena skor dipetakan ke \[0,1], fungsi ini cocok untuk konteks prediksi preferensi.
* **Optimizer**: Adam dengan learning rate 0.001. Optimizer ini dipilih karena kemampuannya yang adaptif dalam mengatur laju pembelajaran, serta kestabilannya dalam konvergensi pada data dengan sparsitas tinggi seperti sistem rekomendasi.
* **Metrik evaluasi**: RootMeanSquaredError (RMSE). RMSE menghitung rata-rata kesalahan prediksi dan memberikan penalti lebih besar pada kesalahan besar. Cocok digunakan untuk sistem yang memprediksi nilai kontinyu seperti rating.

Model kemudian dilatih selama **25 epoch** dengan **batch size 32**, artinya dalam setiap iterasi model memproses 32 sampel sekaligus. Pengaturan ini memberikan efisiensi yang baik antara kecepatan komputasi dan kualitas pembelajaran. Jumlah epoch sebanyak 25 dianggap cukup untuk membiarkan model menemukan pola dalam data tanpa terlalu lama dilatih, yang dapat menimbulkan **overfitting**.

Selama proses pelatihan, performa model pada data latih dan data validasi dievaluasi setiap epoch menggunakan RMSE. Nilai RMSE yang menurun secara stabil pada kedua dataset menunjukkan bahwa model belajar dengan baik. Visualisasi hasil pelatihan (plot RMSE) memberikan gambaran apakah model mengalami penurunan error secara bertahap, serta apakah terdapat jarak yang besar antara data train dan validation yang bisa mengindikasikan **overfitting**.

### Evaluation
Evaluasi Kinerja Model dengan Classification Report

![image](https://github.com/user-attachments/assets/ff21cb12-7374-446d-9ed4-816ef7b24fa9)

Interpretasi singkat:
* Model cukup baik dalam mengenali sentimen positif (Precision & Recall > 0.85).
* Namun masih lemah dalam mengklasifikasikan sentimen negatif, dengan F1-Score hanya 0.48.
* Macro avg menunjukkan ketidakseimbangan performa antar kelas.
* Accuracy 81%, tetapi tidak cukup untuk menyimpulkan bahwa model baik tanpa memperhatikan distribusi kelas.

Agar evaluasi sistem content-based recommendation menjadi terukur, kita menggunakan metrik Precision@K dan Recall@K, yang didefinisikan sebagai berikut:

![image](https://github.com/user-attachments/assets/450e93ac-7de9-4e7b-a62f-35037724858d)

Contoh Perhitungan Precision@K:
Misalnya Sistem merekomendasikan 5 film teratas (K = 5). Dari 5 rekomendasi tersebut, 4 film memiliki genre yang sama/serupa dengan film yang dicari (misal sama-sama "Sci-Fi"). Maka Precision@5= 4/5 =0.8
Artinya, 80% rekomendasi berada dalam genre yang sesuai dengan preferensi pengguna (berdasarkan item acuan).

![download (6)](https://github.com/user-attachments/assets/2af791a9-5982-4237-8f36-fd2c8175f507)

Grafik menunjukkan bahwa nilai RMSE pada data training terus menurun seiring bertambahnya epoch, sementara nilai RMSE pada data validation cenderung stabil setelah beberapa epoch. Ini menandakan model berhasil mempelajari pola dari data.

### Mendapatkan Rekomendasi Film

Pada tahap ini, dilakukan proses pembuatan rekomendasi film untuk satu pengguna secara acak dengan terlebih dahulu mengidentifikasi film yang **belum ditonton** oleh pengguna tersebut. Langkah pertama dimulai dengan memilih satu userId secara acak dari dataset df, kemudian sistem mencari daftar movieId yang **belum pernah ditonton** oleh pengguna tersebut dengan menggunakan operator bitwise ~ untuk mengecualikan film yang sudah pernah dinilai. Setelah itu, hanya ID film yang sudah melalui proses encoding sebelumnya yang dipertahankan agar sesuai dengan struktur input model. Setiap movieId yang belum ditonton tersebut kemudian dipasangkan dengan userId yang telah di-encode, membentuk array dua kolom (user, movie) untuk prediksi. Model yang sudah dilatih sebelumnya digunakan untuk memprediksi rating dari semua kombinasi user-movie ini, lalu hasil prediksi diurutkan untuk mendapatkan 10 film dengan skor prediksi tertinggi. Sebelum menampilkan rekomendasi, sistem juga menampilkan 5 film dengan rating tertinggi yang sudah pernah ditonton oleh user tersebut sebagai referensi. Terakhir, sistem mencetak daftar 10 rekomendasi film berdasarkan prediksi skor tertinggi, lengkap dengan judul dan genre, dari dataframe movie_df. Proses ini mencerminkan pendekatan sistem rekomendasi berbasis prediksi skor individual untuk item yang belum dilihat pengguna.

![image](https://github.com/user-attachments/assets/fa56e5a6-4d69-4461-ad08-0a93c3540ee4)

Dari hasil implementasi sistem rekomendasi menggunakan pendekatan Collaborative Filtering, kita berhasil memberikan rekomendasi yang relevan dan sesuai dengan preferensi pengguna. Dalam contoh ini, sistem menghasilkan rekomendasi untuk pengguna dengan userId 438, yang sebelumnya memberikan rating tinggi pada film bergenre Action, Crime, Thriller, dan War, seperti Heat (1995), 300 (2007), The Manchurian Candidate (1962), serta film klasik seperti Ben-Hur (1959) dan Snakes on a Plane (2006).

Rekomendasi yang dihasilkan oleh sistem menunjukkan konsistensi dengan pola preferensi pengguna. Film seperti Paths of Glory, Memories of Murder, dan Three Billboards Outside Ebbing, Missouri mencerminkan kecenderungan pengguna terhadap genre Crime, Drama, dan Thriller. Di sisi lain, sistem juga menyarankan film dari genre yang sedikit berbeda seperti Comedy dan Western, misalnya Night on Earth, Kolya, dan High Noon. Hal ini menunjukkan bahwa model tidak hanya fokus pada kesamaan genre semata, tetapi juga mempertimbangkan kemungkinan preferensi tersembunyi berdasarkan perilaku pengguna lain yang serupa.

Secara keseluruhan, hasil ini menunjukkan bahwa sistem Collaborative Filtering mampu mengidentifikasi pola interaksi pengguna dan memberikan rekomendasi yang dipersonalisasi dengan baik, meskipun tanpa melihat konten film secara eksplisit. Keunggulan dari pendekatan ini adalah kemampuannya untuk menemukan kesamaan antar pengguna berdasarkan data rating historis, menjadikannya solusi yang efektif dan efisien untuk sistem rekomendasi pada skala data pengguna yang besar.
