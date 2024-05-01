# gRPC Tutorial in Rust

Nama : Ilham Abdillah Alhamdi <br>
NPM : 2206081194

Link Tutorial : [Tutorial 9](https://docs.google.com/document/d/1kIL1vOI3MiMQK3hdVuFuq0XPY3JWxayk/edit)

## Reflection

1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

    **_Unary RPC_**:
    Dalam _unary RPC_, klien mengirim satu permintaan ke server dan menerima satu respons.
    Cocok untuk permintaan sederhana yang sekali jalan di mana klien membutuhkan respons dari server.
    Contohnya seperti mengambil data pengguna, mengirimkan formulir, atau melakukan perhitungan sederhana di server.

    **_Server Streaming RPC_**:
    Dalam _server streaming RPC_, klien mengirim satu permintaan ke server dan menerima beberapa respons sekaligus.
    Cocok untuk skenario di mana server perlu mengirim jumlah data yang besar sebagai respons terhadap satu permintaan dari klien.
    Contoh seperti mengambil dataset besar dari server, mengunduh file, atau menerima _update_ secara _real-time_ dari server.

    **_Bi-directional Streaming RPC_**:
    Dalam _bi-directional streaming RPC_, baik klien maupun server dapat mengirim aliran/banyak pesan secara independen.
    Cocok untuk skenario di mana klien dan server perlu berkomunikasi secara asinkron dan bertukar banyak pesan melalui satu koneksi.
    Contoh termasuk aplikasi _realtime chatting_, _multiplayer games_, atau edit dokumen secara kolaboratif.

2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

    Berikut adalah beberapa pertimbangan keamanan yang terlibat dalam penerapan layanan gRPC di Rust, khususnya terkait dengan autentikasi, otorisasi, dan enkripsi data:

    1. Autentikasi:

        - Implementasikan autentikasi yang kuat, biasanya melalui SSL (Secure Sockets Layer) dan TLS (Transport Layer Security), untuk memastikan baik klien maupun server terotentikasi menggunakan sertifikat. Pada gRPC sendiri sudah terdapat _built-in support_ untuk mekanisme ini.
        - Untuk autentikasi pengguna, gunakan protokol seperti OAuth 2.0

    2. Otorisasi:

        - Terapkan kontrol akses, seperti _role-based access control_ (RBAC) atau _attribute-based access control_ (ABAC), untuk membatasi akses berdasarkan identitas dan peran pengguna.
        - Pastikan pemeriksaan otorisasi yang konsisten di semua _endpoint_

    3. Enkripsi Data:
        - Amankan data selama transit dan saat disimpan, menggunakan enkripsi yang kuat dengan TLS untuk data dalam transit.
        - Lindungi data yang disimpan dengan algoritma enkripsi yang kuat dan manajemen kunci yang baik.
        - Untuk informasi yang sangat sensitif, pertimbangkan untuk mengenkripsi pada tingkat aplikasi sebelum penyimpanan.

3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

    Beberapa tantangan atau masalah yang mungkin muncul saat menangani streaming bi-directional dalam gRPC Rust, terutama dalam skenario seperti aplikasi obrolan, adalah:

    - Pengelolaan Memori: <br>
      Rust membutuhkan pengelolaan memori yang hati-hati. Dalam _streaming bi-directional_, ada kemungkinan kebocoran memori jika pesan tidak dikelola dengan benar.

    - _Channel control_: <br>
      Mengontrol aliran pesan untuk mencegah kelebihan beban pada server atau klien bisa menjadi tantangan, terutama ketika ada banyak pesan yang dikirim dan diterima secara bersamaan.

    - _Error Handling_: <br>
      Dalam _streaming bi-directional_, _error_ seperti kegagalan jaringan atau pesan yang hilang harus dikelola dengan cermat untuk memastikan kelancaran komunikasi.

    - Sinkronisasi Pesan: <br>
      Menjaga urutan pesan yang benar dan sinkronisasi antara klien dan server bisa menjadi kompleks, terutama dalam skenario real-time.

4. What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?

    **Keuntungan menggunakan `tokio_stream::wrappers::ReceiverStream`:**

    - **_Asynchronous Streaming_:** `ReceiverStream` memungkinkan untuk streaming asinkron dari respons. Ini berarti server dapat menangani beberapa permintaan secara bersamaan, meningkatkan _throughput_ secara keseluruhan.

    - **Integrasi dengan Tokio Runtime:** `ReceiverStream` adalah bagian dari ekosistem Tokio, yang banyak digunakan dalam Rust untuk pemrograman _async_. Ini berarti itu terintegrasi dengan baik dengan komponen Tokio lainnya dan runtime Tokio.

    - **Penanganan Backpressure:** `ReceiverStream` menyediakan penanganan backpressure secara otomatis. Jika penerima tidak bisa mengikuti kecepatan pengirim, pengirim akan diperlambat, mencegah potensi masalah out-of-memory.

    **Kekurangan menggunakan `tokio_stream::wrappers::ReceiverStream`:**

    - **Kompleksitas:** Pemrograman asinkron dalam Rust bisa rumit, terutama untuk pemula. Menggunakan `ReceiverStream` membutuhkan pemahaman yang baik tentang async/await, futures, dan streams.

    - **_Error Handling_:** _Error handling_ bisa lebih kompleks dengan `ReceiverStream`. Kesalahan perlu ditangani untuk kedua sisi pengiriman dan penerimaan dari stream.

    - **Dependensi pada Tokio:** Menggunakan `ReceiverStream` berarti menambahkan dependensi Tokio ke proyek. Ini bisa meningkatkan waktu kompilasi dan ukuran biner.

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

    Untuk memfasilitasi **reuse** dan **modularity** dalam kode gRPC Rust, ada beberapa praktik terbaik yang bisa diikuti:

    - **Penggunaan Crates yang Tepat**: Memilih crates seperti `tonic` yang sudah menyediakan implementasi gRPC yang efisien dan mendukung async/await secara native. Crates ini juga mendukung streaming unidirectional dan bidirectional.

    - **Pembagian Modul yang Jelas**: Membagi kode menjadi modul-modul yang jelas dengan tanggung jawab yang terdefinisi. Misalnya, memisahkan definisi service, implementasi, dan model data.

    - **Protobuf Files**: Menyimpan file-file protobuf dalam struktur direktori yang terorganisir dan menggunakan `build script` untuk mengkompilasi protobuf ke kode Rust, sehingga memudahkan pengelolaan dan update interface gRPC¹[1].

    - **Implementasi Trait secara Terpisah**: Mendefinisikan trait untuk service dan implementasinya secara terpisah, memungkinkan penggunaan mock untuk testing dan memudahkan penggantian implementasi tanpa mengubah definisi service.

    - **Error Handling yang Konsisten**: Menggunakan pendekatan yang konsisten untuk **error handling** di seluruh kode, seperti menggunakan `Result` dan `Option` types, serta mendefinisikan custom error types jika diperlukan.

    - **Dependency Injection**: Memanfaatkan dependency injection untuk mengurangi ketergantungan langsung antar komponen dan memudahkan pengujian unit.

    - **Automated Testing**: Membangun suite tes yang komprehensif untuk memastikan bahwa perubahan kode tidak mempengaruhi fungsi yang ada.

    - **Continuous Integration/Continuous Deployment (CI/CD)**: Mengintegrasikan proses CI/CD untuk memastikan kode yang berkualitas tinggi dan mempercepat proses deployment.

    Dengan mengikuti praktik-praktik ini, kode Rust gRPC Anda akan lebih mudah untuk di-maintain dan di-extend seiring dengan waktu.

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

    Dalam implementasi **MyPaymentService**, fungsi `process_payment` saat ini hanya mencetak permintaan pembayaran yang diterima dan langsung mengembalikan hasil yang sukses. Untuk logika pemrosesan pembayaran yang lebih kompleks, berikut beberapa step yang mungkin dibutuhkan:

    -   **Validasi Data PaymentRequest**: Periksa apakah data permintaan valid. Ini bisa termasuk memeriksa apakah field yang diperlukan ada dan apakah nilai-nilainya berada dalam rentang yang diharapkan. Secara umum, tahap ini sudah diotomasi menggunakan Proto Buffer dari gRPC. Namun tentu kita tetap perlu melakukan validasi sesuai dengan _business requirement_ yang diinginkan.

    -   **Interaksi dengan Database**: Mungkin perlu memeriksa saldo pengguna, memperbaruinya setelah pembayaran, dan menyimpan detail transaksi.

    -   **Interaksi dengan Payment Gateway**: Jika memproses pembayaran yang nyata, kita perlu berinteraksi dengan payment gateway atau API bank. Ini biasanya melibatkan pengiriman permintaan ke gateway dan penanganan respons.

    -   **Menangani Berbagai Status Pembayaran**: Pembayaran dapat gagal karena berbagai alasan. Kita harus menangani berbagai status pembayaran (seperti 'pending', 'failed', 'successful') dan mengembalikan respons yang sesuai.

    -   **Error Handling**: Tambahkan penanganan error yang tepat untuk memastikan bahwa setiap masalah selama proses pembayaran tertangkap dan ditangani dengan tepat.

    -   **Logging**: Untuk keperluan debugging dan audit, Anda mungkin ingin mencatat langkah-langkah pemrosesan pembayaran dan hasilnya.

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

    Mengadopsi gRPC sebagai protokol komunikasi dapat memiliki beberapa dampak pada arsitektur dan desain sistem terdistribusi secara keseluruhan:

    -   **Interoperabilitas**: gRPC bersifat agnostik bahasa, artinya dapat menghasilkan kode klien dan server untuk berbagai bahasa. Ini membuatnya sangat interoperabel dengan teknologi dan platform lain.

    -   **Efisiensi**: gRPC menggunakan Protocol Buffers (protobuf) sebagai bahasa definisi antarmuka, yang memungkinkan serialisasi data terstruktur yang efisien. Ini dapat menyebabkan peningkatan kinerja dibandingkan dengan protokol lain seperti REST yang biasanya menggunakan JSON.

    -   **Streaming**: gRPC mendukung permintaan dan respons streaming, yang dapat bermanfaat untuk kasus penggunaan tertentu yang memerlukan komunikasi waktu nyata atau transfer data besar.

    -   **Desain Berbasis Kontrak**: Dengan gRPC, Anda mendefinisikan kontrak layanan terlebih dahulu dalam bentuk file protobuf. Ini dapat mengarah pada desain yang lebih baik karena memaksa Anda untuk memikirkan kontrak API di awal.

    -   **HTTP/2**: gRPC dibangun di atas protokol HTTP/2. Ini membawa manfaat seperti multiplexing (beberapa permintaan secara paralel melalui satu koneksi TCP), server push, kompresi header, dan lainnya.

    -   **Tooling**: gRPC dilengkapi dengan ekosistem alat dan perpustakaan yang kaya, yang dapat membantu dengan tugas-tugas seperti load balancing, autentikasi, logging, dan tracing.

    Namun, perlu dicatat bahwa gRPC mungkin bukan pilihan terbaik untuk semua skenario. Misalnya, tidak se-friendly browser seperti REST, dan bisa lebih kompleks untuk diatur dan digunakan. Juga, meskipun sangat efisien, format biner dari protobuf bisa lebih sulit untuk debug dibandingkan format teks seperti JSON.

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

    **HTTP/2** adalah protokol yang mendasari **gRPC**, sedangkan **HTTP/1.1** dan **HTTP/1.1 dengan WebSocket** digunakan untuk **REST APIs**. Berikut adalah kelebihan dan kekurangan penggunaan **HTTP/2** dibandingkan dengan **HTTP/1.1** atau **HTTP/1.1 dengan WebSocket** untuk **REST APIs**:

    1. **Kelebihan HTTP/2 untuk gRPC**:

        - **Multiplexing**: HTTP/2 memungkinkan **multiplexing**, yang memungkinkan beberapa permintaan dan respons untuk berbagi satu koneksi. Ini mengurangi latensi dan meningkatkan efisiensi komunikasi.
        - **Kompresi Header**: HTTP/2 mengompresi header permintaan dan respons, mengurangi overhead data dan meningkatkan kinerja.
        - **Server Push**: HTTP/2 mendukung **server push**, di mana server dapat mengirimkan sumber daya tambahan (misalnya, file CSS atau gambar) kepada klien tanpa permintaan khusus. Ini mengurangi latensi dan mempercepat waktu muat halaman.
        - **Prioritas Stream**: HTTP/2 memungkinkan pengaturan prioritas untuk setiap channel, memastikan bahwa permintaan yang lebih penting diberikan prioritas lebih tinggi.

    2. **Kekurangan HTTP/2 untuk gRPC**:
        - **Kompleksitas**: Implementasi HTTP/2 lebih kompleks daripada HTTP/1.1. Ini memerlukan pemahaman yang lebih mendalam tentang protokol dan konfigurasi yang tepat.
        - **Dukungan Browser**: Beberapa browser (terutama versi lama) belum sepenuhnya mendukung HTTP/2. Ini membatasi penggunaan gRPC di sisi klien. Solusi seperti **gRPC-web** dan lapisan proxy mungkin diperlukan untuk mengonversi antara HTTP/1.1 dan HTTP/2.

    Dalam konteks **REST APIs**, HTTP/2 memiliki keunggulan dalam hal kinerja dan fitur, tetapi juga memerlukan pemahaman yang lebih mendalam dan memperhatikan dukungan browser. Sementara itu, HTTP/1.1 dengan WebSocket memungkinkan komunikasi real-time, tetapi tidak memiliki fitur seperti multiplexing dan kompresi header yang dimiliki oleh HTTP/2.

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

    **Model request-response** dari **REST APIs** dan kemampuan streaming **gRPC** berbeda secara signifikan dalam hal komunikasi real-time dan responsivitas.

    -   **REST APIs** beroperasi dengan model request-response yang bersifat sinkron. Klien mengirim permintaan ke server dan menunggu respons. Model ini dapat tidak efisien untuk komunikasi real-time karena memerlukan perjalanan bolak-balik untuk setiap pasangan request-response, yang dapat menyebabkan latensi.

    -   **gRPC** mendukung streaming dua arah. Ini berarti baik klien maupun server dapat mengirim aliran pesan satu sama lain secara independen, tanpa menunggu respons. Model ini lebih efisien untuk komunikasi real-time karena memungkinkan pertukaran data yang berkelanjutan, mengurangi latensi dan meningkatkan responsivitas.

    Secara ringkas, meskipun **REST APIs** sederhana dan banyak digunakan, mekanisme tersebut mungkin bukan pilihan terbaik untuk komunikasi real-time karena sifat sinkron mereka. **gRPC**, dengan dukungannya terhadap streaming dua arah, dapat memberikan solusi yang lebih efisien dan responsif untuk kasus penggunaan semacam ini.

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

    **gRPC** menggunakan pendekatan berbasis skema dengan **Protocol Buffers (protobuf)**, sementara **REST API** menggunakan format **JSON** yang lebih fleksibel dan tidak memiliki skema yang ketat. Berikut adalah beberapa implikasi dari perbandingan ini:

    1. **Protokol Buffers (protobuf)**:

        - **_Strong Typing_**: Protobuf memaksa pesan mengikuti skema yang telah ditentukan sebelumnya. Ini memastikan tipe data yang ketat dan validasi pada waktu kompilasi. Jika ada perubahan skema, perubahan ini harus diterapkan di kedua sisi (klien dan server).
        - **Serialisasi Efisien**: Protobuf menghasilkan representasi biner yang lebih ringkas dibandingkan dengan JSON. Ini mengurangi ukuran payload dan mempersingkat waktu parsing, sehingga mempercepat transmisi data.
        - **Kontrak API yang Jelas**: Skema Protobuf berfungsi sebagai kontrak API yang jelas antara klien dan server. Ini memudahkan pengembang untuk memahami format pesan dan memastikan kesesuaian antara keduanya.

    2. **JSON (REST API)**:
        - **Fleksibilitas**: JSON lebih fleksibel karena tidak memerlukan skema yang ketat. Pengembang dapat dengan mudah menambahkan atau mengubah bidang tanpa mempengaruhi klien yang ada. Ini cocok untuk kasus di mana struktur data dapat berubah seiring waktu.
        - **Lebih Mudah Dibaca**: JSON mudah dibaca oleh manusia dan dapat diuraikan oleh berbagai bahasa pemrograman. Ini memudahkan pengembangan dan debugging.
        - **Kekurangan Validasi**: Karena JSON tidak memiliki skema yang ketat, tidak ada validasi pada waktu kompilasi. Kesalahan dalam format data mungkin hanya terdeteksi saat runtime.

    **Kesimpulannya**, penggunaan Protobuf dalam gRPC memberikan ketatnya tipe data, validasi pada waktu kompilasi, dan efisiensi serialisasi. Namun, JSON dalam REST API lebih fleksibel dan mudah digunakan, meskipun kurang ketat dalam hal validasi.
