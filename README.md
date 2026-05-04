## Reflection
### 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

Perbedaan utama antara ketiga metode ini terletak pada aliran datanya, di mana unary RPC mengikuti model satu permintaan untuk satu respons yang sangat cocok untuk operasi sederhana seperti validasi login. Server streaming memungkinkan server mengirimkan aliran data berkelanjutan setelah menerima satu permintaan dari klien, menjadikannya pilihan ideal untuk fitur seperti pemantauan stok atau riwayat transaksi besar. Sementara itu, bi-directional streaming memungkinkan kedua belah pihak mengirimkan pesan secara simultan dan independen dalam satu koneksi, yang merupakan skenario wajib untuk aplikasi interaktif real-time seperti chatting atau sistem kolaborasi jarak jauh.

### 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Dalam mengimplementasikan gRPC dengan Rust, keamanan harus menjadi prioritas utama yang mencakup enkripsi data menggunakan TLS/mTLS untuk melindungi jalur komunikasi dari penyadapan. Autentikasi dapat dilakukan dengan memanfaatkan interceptors pada library `tonic` untuk memverifikasi token keamanan seperti JWT pada metadata setiap permintaan. Selain itu, otorisasi yang ketat harus diterapkan untuk memastikan bahwa pengguna hanya dapat mengakses prosedur yang sesuai dengan peran mereka, serta validasi input yang kuat untuk mencegah serangan injeksi melalui pesan Protocol Buffers.

### 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Menangani bidirectional streaming di Rust menghadirkan tantangan teknis terkait manajemen siklus hidup koneksi asinkron dan pencegahan kebocoran memori pada task yang berjalan di latar belakang. Pengembang harus menangani skenario di mana salah satu pihak memutus koneksi secara tidak terduga, yang memerlukan logika pembersihan state yang rapi. Selain itu, pengaturan backpressure sangat penting agar server tidak kewalahan saat menerima aliran data yang lebih cepat daripada kemampuan pemrosesannya, terutama dalam aplikasi chat dengan volume pesan tinggi.

### 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

Penggunaan `tokio_stream::wrappers::ReceiverStream` menawarkan keuntungan berupa integrasi yang mudah antara sistem channel asinkron milik Tokio dengan antarmuka streaming gRPC, sehingga memudahkan pengiriman data dari berbagai bagian kode. Namun, kekurangannya terletak pada ketergantungan yang kuat pada manajemen buffer channel. Jika kapasitas buffer tidak dikelola dengan benar, hal ini dapat menyebabkan pengiriman pesan terhambat atau bahkan kegagalan sistem jika salah satu sisi berhenti mengonsumsi data secara efisien.

### 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Untuk mempromosikan skalabilitas dan pemeliharaan jangka panjang, kode gRPC Rust sebaiknya disusun dengan memisahkan definisi servis ke dalam modul-modul independen atau crate terpisah untuk setiap domain bisnis. Implementasi logika bisnis harus dipisahkan dari definisi transport gRPC, misalnya dengan menggunakan trait atau harus dipisahkan dari definisi transport gRPC, misalnya dengan menggunakan trait atau pola layering (middleware) untuk menangani aspek umum seperti logging dan pemantauan. Dengan cara ini, pengembang dapat memperbarui satu servis tanpa mengganggu integritas servis lainnya dalam ekosistem microservices.

### 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Dalam menangani pemrosesan pembayaran yang lebih kompleks, langkah tambahan yang diperlukan mencakup integrasi dengan basis data persisten untuk mencatat setiap transaksi dan statusnya secara akurat. Perlu ditambahkan logika penanganan kesalahan yang komprehensif menggunakan kode status gRPC yang tepat, serta mekanisme idempotency untuk memastikan bahwa permintaan pembayaran yang diulang akibat kegagalan jaringan tidak menyebabkan pemotongan saldo ganda pada pengguna.

### 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

Adopsi gRPC mengubah paradigma desain sistem menjadi berbasis kontrak (contract-first design), di mana file `.proto` menjadi sumber kebenaran tunggal yang menjamin interoperabilitas antar berbagai platform dan bahasa pemrograman. Hal ini mengurangi ambiguitas dalam komunikasi antar layanan dan mempercepat pengembangan melalui pembuatan kode otomatis, meskipun hal ini juga menuntut koordinasi yang lebih ketat antara tim saat terjadi perubahan skema pada protokol komunikasi.

### 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Penggunaan HTTP/2 sebagai fondasi gRPC memberikan keunggulan teknis melalui fitur multiplexing yang memungkinkan banyak permintaan dikirim melalui satu koneksi TCP, serta kompresi header yang mengurangi beban overhead jaringan. Dibandingkan dengan HTTP/1.1 yang bersifat tekstual dan sering kali memerlukan banyak koneksi paralel atau penggunaan WebSocket yang kompleks untuk komunikasi dua arah, HTTP/2 jauh lebih efisien dalam penggunaan sumber daya dan memiliki latensi yang lebih rendah untuk komunikasi antar layanan.

### 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Model permintaan-respons tradisional pada REST API sering kali terasa lambat untuk komunikasi real-time karena adanya latensi tambahan dalam pembentukan koneksi baru untuk setiap permintaan. Sebaliknya, kemampuan bidirectional streaming pada gRPC memungkinkan komunikasi yang jauh lebih responsif dan bersifat full-duplex, di mana data dapat didorong secara instan tanpa menunggu permintaan formal, sehingga menciptakan pengalaman pengguna yang jauh lebih lancar pada aplikasi yang sensitif terhadap waktu.

### 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Pendekatan berbasis skema menggunakan Protocol Buffers memberikan keuntungan berupa ukuran muatan data yang jauh lebih kecil dan proses serialisasi yang lebih cepat dibandingkan JSON yang berbasis teks. Meskipun JSON menawarkan fleksibilitas karena sifatnya yang schema-less dan mudah dibaca manusia, ketatnya tipe data pada gRPC meminimalkan kesalahan saat runtime dan memastikan bahwa setiap layanan yang berkomunikasi mematuhi aturan struktur data yang telah disepakati sejak tahap kompilasi.
