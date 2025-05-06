# Module 8 Reflection

## 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

### Unary RPC

Beroperasi dengan model satu permintaan/satu respons, klien mengirim satu permintaan dan server merespons dengan tepat satu respons, ideal untuk operasi sederhana seperti autentikasi, pengambilan data, dan transaksi diskrit, dan paling cocok untuk operasi yang tidak memerlukan pertukaran data berkelanjutan

### Server Streaming RPC

Klien mengirim satu permintaan sementara server mengirim beberapa respons secara bertahap, aliran data terutama dari server ke klien dalam aliran sekuensial, sangat efektif untuk sistem notifikasi, pemantauan data, dan proses yang berjalan lama, dan kasus penggunaan termasuk langganan peristiwa dan pembaruan progres

### Bi-directional Streaming RPC

Klien dan server sama-sama mengirim aliran pesan secara terus-menerus dan bersamaan, memungkinkan komunikasi real-time sejati dengan koneksi persisten, optimal untuk aplikasi interaktif yang membutuhkan pertukaran data konstan, dan sangat berharga untuk aplikasi real-time seperti platform chat, game multipemain, dan alat kolaboratif

## 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

### Enkripsi

gRPC menyertakan dukungan Transport Layer Security (TLS) bawaan dan memungkinkan transmisi data terenkripsi yang aman antar endpoint

### Autentikasi

Tidak ada sistem autentikasi bawaan dalam kerangka kerja gRPC dan pengembang harus mengimplementasikan mekanisme autentikasi khusus seperti JSON Web Tokens (JWT), verifikasi API Key, integrasi token OAuth, dan autentikasi berbasis sertifikat

### Otorisasi

Memerlukan implementasi khusus sistem kontrol akses, dapat mengimplementasikan Role-Based Access Control (RBAC), dan manajemen izin harus dikembangkan secara manual

## 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

### Masalah Konkurensi

Potensi kondisi balapan (race condition) dan kebuntuan (deadlock) dalam lingkungan multi-klien, memerlukan struktur data thread-safe seperti `Arc<Mutex<T>>` atau `Arc<RwLock<T>>` untuk berbagi status yang dapat diubah, dan membutuhkan `tokio::sync` atau saluran `mpsc` untuk komunikasi antar-tugas yang andal

### Manajemen Sumber Daya

Risiko akumulasi buffer saat menangani volume permintaan tinggi, dapat menyebabkan kehabisan memori, kelebihan beban server, atau crash sistem, dan dapat dimitigasi dengan membatasi kapasitas saluran dengan `mpsc(jumlah_saluran)` dengan potensi kondisi balapan (race condition) dan kebuntuan (deadlock) dalam lingkungan multi-klien

## 4. What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?

### Kelebihan

Integrasi mulus antara saluran `tokio::mpsc` dengan gRPC, menyederhanakan penghubungan logika asinkron dengan aliran respons, dan mengurangi kode boilerplate dalam penanganan aliran

### Kekurangan

Berbasis pada arsitektur saluran dengan batas/tanpa batas, dapat menciptakan bottleneck kinerja jika pengelolaan buffer tidak optimal, dan memerlukan konfigurasi yang cermat untuk menghindari masalah memori

## 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

### Pemisahan Layer yang Jelas

Pemisahan yang jelas antara definisi proto, implementasi layanan, dan handler serta antarmuka yang terdefinisi dengan baik antar komponen sistem

### Abstraksi Perilaku

Ekstrak fungsionalitas umum menjadi abstraksi yang dapat digunakan kembali serta membuat antarmuka berbasis trait untuk fleksibilitas

### Pola Desain

Implementasikan pola desain yang sesuai berdasarkan kebutuhan proyek, pertimbangkan dependency injection untuk kemudahan pengujian, dan gunakan pola repositori untuk abstraksi akses data

## 6. In the `MyPaymentService` implementation, what additional steps might be necessary to handle more complex payment processing logic?

### Validasi Input yang Ditingkatkan

Validasi komprehensif kredensial pengguna dan detail pembayaran, verifikasi jumlah dan pemeriksaan batas, dan penanganan konversi mata uang

### Pencatatan Transaksi

Jejak audit terperinci untuk semua aktivitas transaksi, pencatatan operasi sensitif yang aman untuk kepatuhan, dan kemampuan analitik untuk pola transaksi

### Pengelolaan Kesalahan

Penanganan kesalahan yang kuat untuk berbagai skenario kegagalan, batas waktu koneksi, kegagalan autentikasi, dan kesalahan pemrosesan, dan pelaporan kesalahan yang jelas dan mekanisme pemulihan

## 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

### Manfaat Kinerja

Efisiensi yang ditingkatkan dalam komunikasi klien-server, konsumsi bandwidth yang lebih rendah dibandingkan dengan protokol berbasis teks, dan transmisi data lebih cepat dengan format biner

### Kompatibilitas Lintas Platform

Komunikasi agnostik bahasa melalui Protocol Buffers serta dukungan di berbagai lingkungan pemrograman (Rust, Python, Java, dll.)

### Pertimbangan Arsitektur

Ketergantungan pada infrastruktur HTTP/2, potensi tantangan integrasi dengan sistem lama, dan kebutuhan alat khusus

## 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

### Keunggulan HTTP/2

Multiplexing sejati yang memungkinkan permintaan dan respons paralel, kompresi header yang mengurangi overhead, dan kemampuan streaming bawaan tanpa negosiasi ulang koneksi

### Manfaat Streaming gRPC

Client streaming (aliran data berkelanjutan ke server), server streaming (pengiriman data progresif), dan bidirectional streaming (pertukaran data simultan)

### Keterbatasan

Dukungan browser terbatas untuk permintaan HTTP/2 langsung, memerlukan gRPC-Web sebagai perantara untuk integrasi browser, dan kompleksitas implementasi yang lebih tinggi

## 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

### Model REST API

Paradigma permintaan-respons tradisional, klien memulai setiap interaksi secara independen, sangat cocok untuk operasi CRUD sederhana, dan kemampuan terbatas untuk pembaruan real-time

### Bidirectional Streaming gRPC

Koneksi persisten dengan pertukaran data berkelanjutan, komunikasi simultan dalam bidirectional, latensi yang lebih rendah untuk aplikasi real-time, dan kinerja superior untuk sistem interaktif

## 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

### Keunggulan Protocol Buffers

Validasi data otomatis melalui definisi skema, format biner ringkas dengan ukuran payload lebih kecil, kinerja serialisasi/deserialisasi yang lebih baik, dan tipe yang kuat dengan pemeriksaan waktu kompilasi

### Keterbatasan Protocol Buffers

Kurang fleksibel dibandingkan format tanpa skema, memerlukan regenerasi kode ketika skema berubah, dan kurva pembelajaran yang lebih curam untuk tim pengembangan

### Keunggulan JSON

Struktur yang sangat fleksibel tanpa skema kaku, format yang dapat dibaca manusia mempermudah debugging, tidak diperlukan kompilasi ulang untuk perubahan format, dan dukungan universal di seluruh platform

### Keterbatasan JSON

Persyaratan validasi manual, ukuran payload lebih besar (format berbasis teks), dan kinerja lebih rendah dibandingkan dengan format biner
