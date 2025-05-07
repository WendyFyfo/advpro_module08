# PEMROGRAMAN LANJUT - MODULE 8
Muhammad Wendy Fyfo Anggara - 2306223906

## Reflection
1. What are the key differences between unary, server streaming, and bi-directional streaming
RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?
> Unary RPC adalah method di mana client mengirimkan satu request dan menerima satu response dari server. Method Unary cocok digunakan untuk skenario seperti mengambil data dari database, autentikasi pengguna, atau perhitungan yang hasilnya tunggal.

> Server Streaming RPC adalah method dimana client mengirim satu permintaan, namun server mengirimkan beberapa respons secara berurutan dalam satu koneksi. Metode ini cocok digunakan ketika server perlu mengirimkan data dalam jumlah besar atau perlu mengirimkan data secara terus-menerus (stream), misalnya update real-time harga saham, kondisi cuaca, atau saat mengirim file berukuran besar dalam beberapa bagian. 

> Bi-directional Streaming RPC adalah method yang memungkinkan client dan server untuk saling mengirimkan beberapa pesan (response dan request) secara independen dan bersamaan dalam stream yang terus-menerus dalam satu koneksi yang sama tanpa harus bergantian giliran. Method ini berguna untuk aplikasi yang membutuhkan komunikasi dua arah secara real-time, seperti aplikasi chatting atau aplikasi untuk bekerja bersamaan seperti google docs di mana kedua pihak saling mengirim dan menerima data secara bersamaan.

2. What are the potential security considerations involved in implementing a gRPC service in
Rust, particularly regarding authentication, authorization, and data encryption?
> Dalam mengimplentasi layanan gRPC di Rust, ada beberapa pertimbangan keamanan penting yang perlu diperhatikan, khususnya terkait autentikasi, otorisasi, dan enkripsi data. Untuk autentikasi, dapat menggunakan TLS mutual authentication atau atau autentikasi berbasis token seperti JWT. Untuk otorisasi, kita perlu membuat setiap service handler menangani logika otorisasi karena tidak ada mekanisme bawaan. Untuk Ekripsi data di gRPC  ditangani melalui TLS, dan bisa menggunakan crate seperti rustls. 

3. What are the potential challenges or issues that may arise when handling bidirectional
streaming in Rust gRPC, especially in scenarios like chat applications?
> Salah satu tantang yang muncul adalah bagaimana mengelola komunikasi dua arah yang aktif secara bersamaan karena kita harus mengatur alur program agar bisa mengirim dan menerima data secara paralel.

> Tantangan lainnya adalah menangani koneksi putus atau error selama komunikasi berlangung. Kita perlu mengimplementasikan logika untuk mengecek apakah koneksi masih hidup atau membuat cara penanganan error atau gangguan selama komunkasi.

4. What are the advantages and disadvantages of using the
tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?
> Kelebihan penggunaan `tokio_stream::wrappers::ReceiverStream` adalah mudahnya integrasi dengan tonic, karena `ReceiverStream` mengimplementasikan trait `Stream`, sehingga bisa digunakan untuk mengirim data bertahap senagai response dalam server streaming atau bidirectional streaming. 

> Kekurangannya adalah `ReceiverStream` dapat meeyebabkan memory leak atau task yang menggantung jika channel tidak ditutup dengan benar. Misalnya jika `Sender` tidak didrop setelah selesai digunakan atau jika `Receiver` tidak dijalankan secara benar.

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?
> Agar kode gRPC Rust memiliki struktur yang modular dan reusable, hal pertama yang dapat dilakukan adalah tidak menaruh semua logika langsung ke fungsi main di file client dan server. Pada implementasi saya, seluruh proses tiap servis ditaruh langsung di fungsi main yang mempersulit pemeliharaan. Agar lebih modular dan reusable, setiap alur komunikasi seharusnya saya pisaj menjadi fungsi-fungsi asyncnya sendiri. Dengan begitu, kode akan jadi lebih bersih, mudah dibaca, dan memungkinkan untuk digunakan di tempat lain. 

6. In the MyPaymentService implementation, what additional steps might be necessary to
handle more complex payment processing logic?
> Implementasi sekarang hanya mencetak request dan langsung mengembalikan response sukses tanpa validasi atau proses nyata. Untuk skenario pembayaran yang lebih kompleks, perlu ditambahkan validasi input seperti user_id, nominal pembayaran yang tidak negatif dan tidak lebih besar dari saldo (misal ada implementasi saldo), mengurangi saldo, dan mencatat transaksi ke database. Selain itu, error handling perlu dikembangkan lagi untuk berbagai macam skenario gagal.

7. What impact does the adoption of gRPC as a communication protocol have on the overall
architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?
> Adopsi gRPC sebagai protokol komunikasi memberikan dampak signifikan terhadap arsitektur dan desain sistem terdistribusi, terutama dalam hal performa, struktur layanan, dan interoperabilitasnya. Secara arsitektur, gRPC mendorong desain berbasis microservice karena tiap layanan dapat dikembangkan, dideploy, dan diskalakan secara independen menggunakan kontrak protokol yang didefisnisikan lewat Protobuf. Di sisi Interoperabilitas,gRPC mendukunng berbagai bahasa pemrograman, tetapi integrasi dengan platform yang tidak mendukung HTTP/2 atau streaming sulit.

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for
gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?
> Salah satu keunggulannya adalah dukungan multiplexing dengan websocket, yang memungkinkan banyak request dan response dikirm secara paralel dalam satu koneksi TCP, sehingga lebih efisien dan cepat dibanding HTTP/1.1 yang hanya memproses satu request per koneksi. Selain itu, HTTP/2 menggunakan binary framing sehingga proses parsing lebih cepat dan ukuran data lebih kecil.

> Di sisi lain, HTTP/2 memiliki masalahnya dengan kompatibilitas. Tidak semua infrastruktur mendukung penuh gRPC dan browser tidak mendukung gRPC secara langsung, sehingga integrasi dengan frontend perlu tambahan gateway REST atau fallback yang memperumit arsitektur.

9. How does the request-response model of REST APIs contrast with the bidirectional streaming
capabilities of gRPC in terms of real-time communication and responsiveness?
> REST API menggunakan model request-response, di mana client mengirim permintaan dan menunggu respons dari server. Ini tidak cocok untuk komunikasi real-time karena server hanya merespons setelah menerima permintaan baru, meningkatkan latency.

>gRPC dengan bidirectional streaming memungkinkan komunikasi dua arah dalam satu koneksi terbuka, sehingga client dan server dapat saling mengirim pesan kapan saja. Oleh karena itu, gRPC lebih efisien untuk aplikasi real-time seperti chat atau sistem monitoring yang membutuhkan pertukaran data cepat dan terus-menerus.

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers,
compared to the more flexible, schema-less nature of JSON in REST API payloads?
> gRPC menggunakan Protocol Buffer memerlukan skema yang sudah ditentukan, sehingga komunikasi lebih efisien dan terstruktur. Namun menjadi kurang fleksibel karena setiap perubahan skema perlu pembaruan di client dan server. Protobuf juga lebih ringan dan cepat dalam serialisasi dibandingkan JSON.

> REST API dengan lebih fleksibel dan schema-less, sehingga adanya perubahan data tidak perlu dibarengi dengan pembaruan skema, tetapi bisa menyebabkan ketidaksesuaian data dan lebih berat dalam ukuran payload serta kecepatan serialisasi. 