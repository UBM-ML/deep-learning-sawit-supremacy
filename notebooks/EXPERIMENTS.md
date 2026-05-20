# Experiment Log

Catat setiap percobaan hyperparameter di sini. **Minimal 5 eksperimen.**

> Tips: ubah **satu hyperparameter pada satu waktu** agar bisa mengisolasi efeknya. Setelah memahami efek tiap variabel, baru gabungkan untuk hasil terbaik.

---

## 📋 Tabel Ringkasan

Isi tabel ini setelah selesai semua eksperimen.

| # | Hidden | Neurons | Activation | Optimizer | LR     | Batch | Epochs | Dropout | Test Acc | Train Time |
|---|--------|---------|------------|-----------|--------|-------|--------|---------|----------|------------|
| 0 | 1      | 64      | relu       | sgd       | 0.01   | 32    | 10     | 0.0     | 85.05%   | 46.3s      |
| 1 | 1      | 128     | relu       | adam      | 0.001  | 64    | 10     | 0.0     | 87.46%   | 25.9s      |
| 2 | 3      | 128     | relu       | adam      | 0.001  | 32    | 20     | 0.3     | 87.24%   | 103.7s     |
| 3 | 2      | 256     | elu        | rmsprop   | 0.001  | 64    | 15     | 0.2     | 88.72%   | 40.3s      |
| 4 | 2      | 64      | tanh       | sgd       | 0.05   | 128   | 20     | 0.0     | 87.27%   | 26.7s      |
| 5 | 4      | 128     | relu       | adamax    | 0.002  | 32    | 25     | 0.2     | 88.32%   | 133.7s     |
| 6 | 2      | 256     | relu       | adam      | 0.01   | 512   | 50     | 0.2     | 86.86%   | 29.5s      |
| 7 | 1      | 128     | relu       | sgd       | 0.1    | 32    | 50     | 0.0     | 87.62%   | 226.6s     |

> **Eksperimen #0** = baseline (jangan ubah, ini patokan kalian).

---

## 🧪 Detail Setiap Eksperimen

Gunakan template di bawah untuk SETIAP eksperimen.

---

### Eksperimen #1

**Apa yang diubah dari baseline:**
> Mengganti optimizer ke `adam`, meningkatkan kapasitas neuron menjadi 128, menyesuaikan learning rate ke 0.001, dan memperbesar batch size ke 64.

**Hipotesis sebelum run:**
> Kapasitas neuron yang lebih medium dipadukan dengan optimizer adaptif (Adam) seharusnya mempercepat konvergensi menuju loss yang lebih rendah dan mendongkrak akurasi secara signifikan dibanding SGD standar.

**Hasil:**
- Test accuracy: 87.46%
- Train accuracy: 91.01%
- Validation accuracy: 88.00%
- Train time: 25.9 detik
- Apakah overfit/underfit? Cenderung mulai sedikit **Overfit** (selisih Train dan Test mencapai ~3.5%).

**Observasi & Insight:**
> Waktu training jauh lebih cepat (hanya 25.9 detik) karena pelebaran *batch size*. Test loss turun menjadi 0.3682. Meskipun akurasi membaik, ketiadaan *dropout* membuat model sedikit mulai menghafal data *training*.

**Rencana eksperimen berikutnya:**
> Menambah kedalaman arsitektur (*hidden layers*) dan menerapkan *dropout* untuk meredam *overfitting*.

---

### Eksperimen #2

**Apa yang diubah:**
> Mengubah hidden layers menjadi 3 lapis, menambahkan Dropout rate sebesar 0.3, mengecilkan batch size kembali ke 32, dan melatih lebih lama hingga 20 epochs.

**Hipotesis:**
> Model yang lebih dalam (*3 layers*) diharapkan dapat mengekstraksi fitur dengan lebih hierarkis, sementara lapisan dropout sebesar 30% akan berperan sebagai regularisasi yang kuat untuk mencegah *overfitting* visual.

**Hasil:**
- Test accuracy: 87.24%
- Train accuracy: 88.39%
- Validation accuracy: 87.58%
- Train time: 103.7 detik
- Apakah overfit/underfit? **Optimal / Seimbang** (gap akurasi train dan test sangat tipis, ~1%).

**Observasi:**
> Penggunaan *Dropout* sangat efektif mencegah *overfit* (terlihat dari kedekatan train dan val accuracy). Sayangnya, tambahan *layer* dan pengembalian *batch size* kecil (32) membuat waktu *training* membengkak menjadi di atas 100 detik.

---

### Eksperimen #3

**Apa yang diubah:**
> Mengatur ulang hidden layers menjadi 2 lapis namun dilebarkan ke 256 neuron. Mengubah aktivasi menjadi `elu`, memakai optimizer `rmsprop`, dropout 0.2, batch size 64, dan dilatih selama 15 epochs.

**Hipotesis:**
> Arsitektur yang lebih mendatar namun lebar (256 neuron) ditambah aktivasi *Exponential Linear Unit* (ELU) dan optimizer RMSprop akan memperbaiki aliran gradien, meningkatkan kapasitas komputasi tanpa jatuh ke *vanishing gradient*, dan mencapai keseimbangan waktu-performa terbaik.

**Hasil:**
- Test accuracy: 88.72%
- Train accuracy: 90.07%
- Validation accuracy: 89.13%
- Train time: 40.3 detik
- Apakah overfit/underfit? **Optimal** (generalization gap sangat kecil dan masih dapat ditoleransi).

**Observasi:**
> Ini adalah hasil paling seimbang. Konfigurasi 256 neuron dengan ELU dan RMSprop sukses mencapai skor Test Accuracy tertinggi sejauh ini (88.72%). Model memiliki kapasitas generalisasi yang tangguh sekaligus waktu eksekusi yang ideal.

---

### Eksperimen #4

**Apa yang diubah:**
> Pendekatan eksperimen klasik: Hidden layers 2 dengan 64 neuron, tanpa dropout (0.0). Menggunakan aktivasi `tanh` dan optimizer `sgd` dengan *learning rate* lebih agresif di 0.05. Memperbesar batch size ke 128 untuk kompensasi LR yang tinggi, dilatih 20 epochs.

**Hipotesis:**
> Bereksperimen dengan metode klasik menggunakan `tanh` (agar gradien *zero-centered*) dan *Stochastic Gradient Descent*. *Batch size* yang dibesarkan (128) dipadukan dengan *learning rate* besar (0.05) diharapkan membuat langkah iterasi lebih stabil dan cepat.

**Hasil:**
- Test accuracy: 87.27%
- Train accuracy: 89.41%
- Validation accuracy: 87.95%
- Train time: 26.7 detik
- Apakah overfit/underfit? **Sedikit Overfit** (karena tidak ada Dropout).

**Observasi:**
> Konfigurasi *old-school* ini menuntaskan kalkulasi dengan sangat cepat (26.7 detik) karena *batch size* besar. Hasil akurasinya baik secara umum, namun gagal menembus akurasi 88% seperti yang dicapai oleh optimizer adaptif modern (Adam/RMSprop).

---

### Eksperimen #5

**Apa yang diubah:**
> Menguji arsitektur yang sangat dalam dengan 4 hidden layers (128 neuron/lapis) menggunakan aktivasi ReLU dengan Dropout 0.2. Menggunakan optimizer `adamax` dengan LR 0.002 dan batch size 32 selama 25 epochs.

**Hipotesis:**
> Adamax dikenal handal menangani optimasi model dengan representasi matriks tak terbatas atau arsitektur yang mendalam. Dengan 4 layers, kapasitas pembelajaran fitur akan sangat kompleks.

**Hasil:**
- Test accuracy: 88.32%
- Train accuracy: 90.81%
- Validation accuracy: 89.10%
- Train time: 133.7 detik
- Apakah overfit/underfit? **Optimal / Sedikit Overfit**.

**Observasi:**
> Walaupun model sukses mencapai akurasi yang luar biasa stabil dan meminimalisir nilai test loss hingga 0.3406, beban komputasi arsitektur 4 *layers* ditambah *batch* 32 ini sangat berat hingga memakan waktu eksekusi terlama (133.7 detik).

---

### Eksperimen #6

**Apa yang diubah:**
> Kondisi ekstrem untuk konvergensi kilat: LR Adam dinaikkan drastis (0.01) namun diredam menggunakan Batch Size maksimal (512). 2 Hidden layers @ 256 neuron, dilatih hingga 50 epochs.

**Hipotesis:**
> Batch size yang berukuran masif (512) akan membuat waktu epoch sangat singkat. Untuk mengkompensasi pergerakan gradien yang sedikit, Learning Rate dibesarkan menjadi 0.01 agar langkah menuju konvergensi tetap cepat.

**Hasil:**
- Test accuracy: 86.86%
- Train accuracy: 88.46%
- Validation accuracy: 87.38%
- Train time: 29.5 detik
- Apakah overfit/underfit? **Underfit / Terhenti (Stagnan)**.

**Observasi:**
> Waktu eksekusi sangat cepat (29.5 detik) meskipun melintasi 50 epochs. Sayangnya, LR sebesar 0.01 ternyata terlalu radikal sehingga model terjebak, terlempar (*bouncing*), dan gagal memperbaiki akurasi optimal (tertahan di 86.86%).

---

### Eksperimen #7

**Apa yang diubah:**
> Kondisi ekstrem untuk *update* agresif: Menggunakan LR SGD yang sangat raksasa (0.1) ditambah dengan Batch kecil (32). Menggunakan 1 Hidden layer @ 128 neuron tanpa dropout (0.0) selama 50 epochs.

**Hipotesis:**
> Pembaruan bobot (*weight update*) yang super masif di setiap batch kecil diharap bisa memaksa model "mempelajari" data train dengan secepat mungkin tanpa kompromi.

**Hasil:**
- Test accuracy: 87.62%
- Train accuracy: 96.34%
- Validation accuracy: 88.38%
- Train time: 226.6 detik
- Apakah overfit/underfit? **Overfit Sangat Parah**.

**Observasi:**
> Kombinasi SGD murni dengan *learning rate* 0.1 di *batch* kecil mendorong model mengalami spesialisasi (*overfitting*) ekstrem pada data latih (akurasi train meroket ke 96.34%). Karena kehilangan generalisasi, *test accuracy* tertinggal jauh di 87.62% dan nilai loss pada test menjadi salah satu yang tertinggi (0.5216).

---

## 🏆 Konfigurasi Terbaik

Setelah semua eksperimen, salin konfigurasi terbaik kalian ke sini:

```python
HIDDEN_LAYERS     = 2
NEURONS_PER_LAYER = 256
ACTIVATION        = 'elu'
DROPOUT_RATE      = 0.2
OPTIMIZER         = 'rmsprop'
LEARNING_RATE     = 0.001
BATCH_SIZE        = 64
EPOCHS            = 15