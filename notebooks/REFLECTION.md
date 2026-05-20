# Refleksi Tim

> Jawaban dalam Bahasa Indonesia. Maksimal 1 halaman (~500 kata). Yang dinilai adalah **kedalaman pemahaman**, bukan panjang tulisan.

---

## 1. Parameter vs Hyperparameter

Berdasarkan eksperimen yang kalian lakukan, jelaskan dengan **kata-kata kalian sendiri**:
- Apa yang termasuk **parameter** dalam model kalian, dan apa yang termasuk **hyperparameter**?
- Manakah yang berubah saat training berjalan, dan manakah yang ditentukan oleh kalian sebelum training?

**Jawaban:**
> **Parameter** adalah nilai-nilai di dalam model yang dipelajari dan disesuaikan secara otomatis oleh algoritma, yaitu **bobot (*weights*)** dan **bias** dari setiap neuron pada *hidden layers*. 
> Sedangkan **hyperparameter** adalah konfigurasi eksternal yang kita atur secara manual untuk mengontrol jalannya proses belajar, seperti *learning rate*, *batch size*, *jumlah hidden layers*, ukuran *neuron* (64/128/256), *dropout rate*, dan jenis *optimizer* (SGD, Adam, RMSprop).
> Saat proses *training* berjalan, **parameter akan terus berubah** melalui *back propagation*, sedangkan **hyperparameter ditentukan sebelum training dimulai** dan nilainya tetap (kecuali kita menggunakan mekanisme dinamis seperti *learning rate scheduler*).

---

## 2. Hyperparameter dengan Dampak Terbesar

Dari semua hyperparameter yang kalian eksperimen, mana yang menurut kalian memberikan **dampak paling besar** terhadap akurasi? Mengapa demikian — apa yang kalian amati pada kurva loss/accuracy?

**Jawaban:**
> **Learning Rate** dan kombinasinya dengan jenis **Optimizer** memberikan dampak paling besar. Ketika *learning rate* diatur terlalu agresif (misalnya 0.1 dengan optimasi SGD murni tanpa *Dropout*), model langsung terjebak dalam *overfitting* yang parah. Hal ini terlihat pada kurva di mana *training accuracy* meroket hingga 96.34%, namun *test accuracy* tertinggal di 87.62% dan nilai *test loss* membengkak. Sebaliknya, ketika menggunakan *learning rate* yang lebih moderat (0.001) dengan RMSprop, kurva *loss* menurun dengan mulus dan teratur, menghasilkan akurasi validasi yang stabil.

---

## 3. Learning Rate

Coba set `LEARNING_RATE = 1.0` (atau bahkan lebih besar) dan jalankan sekali. Apa yang terjadi pada kurva loss? Hubungkan jawaban kalian dengan rumus:

$$W_j = W_j - \lambda \frac{\partial F(W_j)}{\partial W_j}$$

**Jawaban:**
> Jika `LEARNING_RATE` ($\lambda$) diatur ke 1.0, kurva *loss* menjadi sangat tidak stabil (melonjak-lonjak ekstrem) atau bahkan meledak (*exploding* / gagal konvergen). 
> Berdasarkan rumus tersebut, $\lambda$ berfungsi sebagai ukuran langkah (*step size*) dalam memperbarui bobot ($W_j$). Jika $\lambda$ terlalu besar, pengurangan terhadap gradien $\frac{\partial F(W_j)}{\partial W_j}$ menjadi terlalu masif. Bukannya bergerak turun secara bertahap menuju titik minimum (*loss* terendah), model justru "melompati" lembah minimum tersebut secara berlebihan, membuat bobot ($W_j$) terpantul ke nilai-nilai ekstrem yang membuat prediksi semakin acak dan *loss* semakin membesar.

---

## 4. Batch Size & Trade-off

Bandingkan eksperimen dengan **batch size kecil** (misal 16) vs **batch size besar** (misal 256). Apa yang kalian amati dari sisi:
- Waktu training?
- Stabilitas kurva loss?
- Akurasi akhir?

Apakah pengamatan ini sesuai dengan teori di slide kuliah?

**Jawaban:**
> - **Waktu training:** *Batch size* kecil membuat waktu komputasi per *epoch* jauh lebih lama karena pembaruan bobot terjadi lebih sering. Sebaliknya, *batch size* besar jauh lebih cepat karena memanfaatkan paralelisasi memori GPU/CPU dengan optimal.
> - **Stabilitas kurva loss:** *Batch size* kecil (misal 32 pada baseline awal) menghasilkan kurva *loss* yang sangat fluktuatif (*noisy*) karena gradien dihitung berdasarkan sampel yang sangat terbatas. *Batch size* yang lebih besar (misal 64 atau 128) memperlihatkan penurunan kurva *loss* yang jauh lebih mulus.
> - **Akurasi akhir:** *Batch size* kecil sesungguhnya terkadang memberikan sedikit efek regularisasi yang membantu model keluar dari *local minima*, namun *batch size* moderat (64) pada eksperimen kami memberikan stabilitas perhitungan gradien (*gradient descent*) terbaik sehingga model mencapai keseimbangan *test accuracy* tertinggi (88.72%).
> Pengamatan ini selaras dengan teori *trade-off* di perkuliahan antara *stochasticity* (acak/fluktuatif) pada *batch* kecil melawan arah gradien yang akurat namun butuh memori besar pada *batch* raksasa.

---

## 5. Feed Forward & Back Propagation

Pada saat kalian menekan `model.fit(...)`, sebenarnya proses feed forward dan back propagation berjalan **ribuan kali**. Hitung kira-kira berapa kali back propagation terjadi pada salah satu eksperimen kalian.

> Petunjuk: `(jumlah_sample_training / batch_size) × epochs`

Jelaskan apa yang terjadi pada **bobot** dan **bias** model kalian di antara iterasi pertama dan terakhir.

**Jawaban:**
> Mengambil eksperimen konfigurasi terbaik (asumsi dataset Fashion-MNIST memiliki 60.000 sampel latih):
> `(60.000 sampel / 64 batch size) × 15 epochs = 937.5 × 15 ≈ 14.062 kali back propagation`.
> Pada **iterasi pertama**, bobot dan bias masih berupa angka acak (*random initialization*), sehingga hasil *feed forward* menghasilkan prediksi yang keliru dan *loss* yang sangat tinggi. Di setiap iterasi, gradien dihitung dari *loss* tersebut dan disalurkan kembali ke belakang (*back propagation*) untuk merevisi nilai-nilai bobot dan bias sedikit demi sedikit sesuai *learning rate*. 
> Pada **iterasi terakhir** (setelah ~14.062 kali perbaikan), kombinasi seluruh bobot dan bias pada lapisan 256 neuron telah "terbentuk" secara spesifik. Konfigurasi angka-angka ini kini mampu memetakan input (piksel gambar) menjadi probabilitas kelas *output* dengan tingkat kesalahan seminimal mungkin.

---

## 6. Kapan Deep Learning Tepat Digunakan?

Berdasarkan pengalaman kalian dengan Fashion-MNIST, menurut kalian apakah masalah ini *benar-benar* membutuhkan deep learning, atau bisa diselesaikan dengan machine learning klasik (misal Logistic Regression atau Random Forest)? Beri argumen.

**Jawaban:**
> Masalah klasifikasi Fashion-MNIST sebenarnya berada di area transisi. Kita **masih bisa** menyelesaikannya dengan *machine learning* klasik seperti Random Forest atau SVM dengan akurasi yang cukup baik. Namun, algoritma klasik memperlakukan gambar sekadar sebagai array angka 1D dan membutuhkan ekstraksi fitur manual (*feature engineering*) jika ingin menangkap pola visual secara optimal.
> *Deep Learning* menjadi sangat tepat digunakan di sini—dan esensial untuk gambar resolusi tinggi—karena kemampuannya dalam **representational learning**. Jaringan saraf tiruan secara hierarkis belajar mengekstraksi fitur kompleks secara otomatis. Lapisan pertama mungkin mengenali garis dan tepi bentuk pakaian, sedangkan lapisan berikutnya mampu mengenali pola tekstur atau siluet utuh tanpa perlu kita memprogram aturan tersebut secara manual.

---

## 7. Refleksi Tim

- Tantangan apa yang paling sulit?
- Apa pelajaran terpenting yang kalian dapat dari aktivitas ini?
- Jika diberi waktu lebih, apa yang ingin kalian coba lagi?

**Jawaban:**
> - **Tantangan tersulit:** Menemukan titik penyeimbang (*sweet spot*) antara mencegah model dari *overfitting* dan mempertahankan kapasitas jaringannya. Sangat menantang ketika melihat akurasi data latih terus menembus 95%, tetapi akurasi *test* tertahan di 87%.
> - **Pelajaran terpenting:** Pendekatan arsitektur yang terukur dan seimbang lebih ampuh daripada pendekatan agresif yang asal ekstrem. Menambah *neuron* besar (256 buah) hanya akan berguna secara optimal jika dikawal dengan metode regularisasi (*Dropout* 0.2) serta dibantu *optimizer* yang stabil seperti RMSprop atau Adamax (bukan sekadar SGD + *learning rate* tinggi).
> - **Eksperimen ke depan:** Jika diberi waktu lebih, kami ingin merancang arsitektur model Convolutional Neural Network (CNN) 2D untuk membandingkan seberapa besar lonjakan keefektifan pola spasial dibandingkan model klasifikasi mendatar (MLP/Dense) biasa, serta bereksperimen dengan fungsi *Learning Rate Scheduler*.