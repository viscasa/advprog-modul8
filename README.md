<div align="center">
    <h1>MODULE 8</h1>
</div>

<div align="center">
    <img src="assets/images/burhan.png" alt="burhan" width="200"/>
</div>

<div align="center">
    <h2>Alwie Attar Elfandra</h2>
    <h2>2306241726</h2>
</div>

### **Apa perbedaan utama antara unary, server streaming, dan bidirectional streaming RPC, serta kapan masing-masing sebaiknya digunakan?**

Dalam *unary RPC*, klien mengirimkan satu permintaan dan menerima satu respons, mirip dengan pola REST konvensional. *Server streaming RPC* memungkinkan server mengirimkan serangkaian respons setelah menerima satu permintaan dari klien. Sementara itu, *bidirectional streaming RPC* memungkinkan klien dan server saling bertukar pesan secara independen dalam aliran terbuka selama koneksi berlangsung.

* **Unary** cocok untuk operasi sederhana seperti autentikasi, pengambilan data tunggal, atau pengiriman formulir.
* **Server streaming** ideal untuk pengiriman data berkelanjutan seperti notifikasi pasar saham atau siaran berita.
* **Bidirectional streaming** sangat sesuai untuk aplikasi interaktif dan real-time seperti sistem obrolan, kolaborasi dokumen, atau game multiplayer.

---

### **Apa saja pertimbangan keamanan saat mengimplementasikan layanan gRPC di Rust, terutama dalam hal otentikasi, otorisasi, dan enkripsi data?**

Dalam implementasi layanan gRPC dengan Rust, penting untuk menerapkan otentikasi dan otorisasi secara berkelanjutan, terutama karena komunikasi bisa berlangsung dalam bentuk streaming. Tidak seperti REST yang hanya memverifikasi kredensial sekali per permintaan, gRPC membutuhkan validasi untuk setiap sesi atau aliran. Selain itu, seluruh lalu lintas data harus dienkripsi, umumnya menggunakan TLS, untuk mencegah kebocoran data dan serangan *man-in-the-middle*. Pendekatan ini membutuhkan manajemen sertifikat dan kontrol akses yang kuat.

---

### **Apa tantangan yang mungkin muncul saat menangani bidirectional streaming di gRPC Rust, khususnya pada aplikasi seperti chat?**

Bidirectional streaming dalam Rust gRPC, seperti untuk aplikasi obrolan, dapat menghadapi berbagai tantangan, antara lain:

* **Masalah sinkronisasi**: klien dan server harus menyelaraskan pengiriman dan penerimaan pesan.
* **Potensi *deadlock***: jika kedua pihak saling menunggu data terlebih dahulu.
* **Buffer overflow**: jika pesan tidak segera dikonsumsi.
* **Koneksi tidak stabil**: membutuhkan strategi pemulihan atau *reconnect*.
* **Pengelolaan konkurensi**: pemrosesan simultan memerlukan pengendalian thread yang aman.
* **Keamanan tambahan**: termasuk enkripsi end-to-end dan autentikasi per sesi streaming.

---

### **Apa kelebihan dan kekurangan menggunakan `tokio_stream::wrappers::ReceiverStream` dalam streaming respons gRPC di Rust?**

Penggunaan `tokio_stream::wrappers::ReceiverStream` memberikan fleksibilitas tinggi dengan integrasi erat terhadap ekosistem asinkron Tokio. Ini memudahkan konversi dari channel atau `mpsc::Receiver` ke stream yang sesuai untuk gRPC. Namun, pendekatan ini bisa memperkenalkan kompleksitas tambahan, terutama bagi pengembang yang belum terbiasa dengan konsep *async/await* dan manajemen channel dalam Tokio. Juga terdapat sedikit overhead dalam membungkus dan memanajemen aliran data secara manual.

---

### **Bagaimana cara menyusun kode Rust gRPC agar modular dan mudah dipelihara?**

Untuk menciptakan arsitektur kode yang bersih dan modular:

* Pisahkan definisi `.proto` ke dalam modul sendiri.
* Gunakan *trait-based abstraction* agar implementasi server mudah diganti atau diuji.
* Letakkan logika bisnis di lapisan terpisah dari handler gRPC.
* Gunakan crate seperti `tonic-build` untuk mengotomatisasi proses generate kode.
* Gunakan modul seperti `services/`, `handlers/`, dan `models/` untuk memisahkan tanggung jawab dan meningkatkan *reusability*.

---

### **Langkah tambahan apa yang diperlukan dalam `MyPaymentService` untuk menangani logika pembayaran yang kompleks?**

Untuk mendukung logika pembayaran kompleks, implementasi `MyPaymentService` perlu dilengkapi dengan:

* Validasi input dan penanganan kesalahan yang robust.
* Otentikasi dan otorisasi berdasarkan identitas pengguna.
* Integrasi dengan gateway pembayaran eksternal seperti Stripe atau Midtrans.
* Pengelolaan status transaksi dan *retry mechanism* untuk transaksi yang gagal.
* Penerapan aturan bisnis (misal: batas transaksi, diskon).
* Mekanisme notifikasi transaksi (email, webhook).
* Logging dan audit trail untuk pelacakan.
* Pengujian menyeluruh, termasuk uji beban dan skenario edge-case.

---

### **Apa dampak adopsi gRPC terhadap arsitektur sistem terdistribusi, terutama dari sisi interoperabilitas teknologi?**

Mengadopsi gRPC mengubah arsitektur dari komunikasi berbasis HTTP/JSON menjadi pendekatan *binary RPC* yang lebih efisien. Ini menyederhanakan interaksi antar layanan dengan kontrak eksplisit yang ditentukan dalam file `.proto`. Meskipun interoperabilitas sangat baik di lingkungan lintas bahasa berkat dukungan multi-bahasa Protocol Buffers, integrasi dengan sistem non-gRPC (seperti REST-only frontend) mungkin membutuhkan adapter atau gateway tambahan.

---

### **Apa kelebihan dan kekurangan HTTP/2 (protokol dasar gRPC) dibandingkan HTTP/1.1 atau WebSocket untuk API REST?**

**Kelebihan HTTP/2:**

* Mendukung multiplexing: banyak permintaan-respons dalam satu koneksi.
* Kompresi header yang mengurangi ukuran data.
* Server push untuk notifikasi aktif.

**Kekurangan:**

* Lebih kompleks secara teknis dan memerlukan TLS.
* Mungkin tidak sepenuhnya didukung oleh semua alat atau infrastruktur lama.

**WebSocket** unggul untuk komunikasi dua arah real-time, tetapi tidak memiliki struktur standar seperti gRPC dan sering memerlukan protokol khusus di atasnya.

---

### **Bagaimana perbedaan model permintaan-respons REST dengan streaming dua arah gRPC dalam konteks komunikasi real-time?**

REST API menggunakan model komunikasi sinkron: klien mengirim permintaan, lalu menunggu respons. Ini cocok untuk operasi stateless dan data yang tidak terlalu dinamis. Sebaliknya, gRPC mendukung *bidirectional streaming*, memungkinkan pertukaran data secara simultan antara klien dan server, sangat cocok untuk kebutuhan komunikasi real-time, seperti obrolan, game, atau notifikasi langsung.

---

### **Apa implikasi penggunaan skema berbasis Protocol Buffers pada gRPC dibanding pendekatan JSON yang fleksibel di REST?**

Protocol Buffers mendefinisikan skema eksplisit yang dikompilasi menjadi kode, menghasilkan format biner yang ringan dan efisien. Hal ini mempercepat proses parsing dan mengurangi ukuran data. Namun, ia kurang fleksibel dibandingkan JSON yang dapat berubah secara dinamis tanpa perlu regenerasi kode. JSON cocok untuk prototyping cepat dan sistem yang sering berubah, sedangkan Protobuf lebih sesuai untuk sistem besar dan stabil yang menuntut kinerja tinggi dan validasi tipe yang kuat.