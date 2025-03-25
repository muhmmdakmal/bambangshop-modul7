# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

    Di dalam pola Observer, penggunaan interface (atau trait di Rust) dimaksudkan untuk mengabstraksi perilaku subscriber sehingga bisa ada berbagai implementasi yang berbeda. Untuk kasus tutorial ini, jika semua subscriber memiliki perilaku dan struktur data yang sama, satu model struct sudah cukup. Namun, jika nantinya ada kebutuhan untuk variasi perilaku atau tipe subscriber yang berbeda, menggunakan trait akan memberikan fleksibilitas ekstra untuk mengimplementasikan berbagai jenis subscriber.

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

    Menurut saya, karena id di Program dan url di Subscriber harus unik, penggunaan DashMap lebih tepat daripada menggunakan Vec. Dengan DashMap, kita bisa mendapatkan, menambah, atau menghapus data dengan kompleksitas waktu rata-rata O(1) dan memastikan tidak ada duplikasi key. Sedangkan dengan Vec, kita perlu melakukan pencarian secara linear untuk memeriksa keunikan, yang tidak efisien bila jumlah data bertambah.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?
    Menurut saya, DashMap digunakan karena menyediakan mekanisme thread-safe secara built-in untuk operasi baca/tulis pada koleksi secara bersamaan. Meskipun Singleton pattern memang memastikan bahwa hanya ada satu instance dari suatu objek (dalam hal ini SUBSCRIBERS), pola tersebut tidak secara otomatis memberikan jaminan thread-safety bagi operasi-operasi yang dilakukan pada data di dalam instance tersebut.

    Singleton pattern lebih berkaitan dengan pengelolaan instance tunggal, sedangkan DashMap dirancang khusus untuk mengelola akses bersamaan dengan mekanisme locking yang efisien. Jadi, dalam konteks Rust yang memang memperketat aturan thread safety, penggunaan DashMap tetap diperlukan untuk memastikan bahwa operasi pada data collection seperti SUBSCRIBERS berlangsung aman secara thread-safe, meskipun kita mengimplementasikan Singleton pattern untuk instance tunggal.
#### Reflection Publisher-2

1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

    Menurut saya, pemisahan antara Service dan Repository dari Model dilakukan untuk menerapkan prinsip Single Responsibility dan Separation of Concerns. Dengan memisahkan Repository, kita mengisolasi logika akses data (persistence) sehingga modul Model tidak perlu repot menangani detail bagaimana data disimpan atau diambil. Sementara Service bertugas mengatur logika bisnis dan aturan aplikasi, sehingga nantinya apabila ada perubahan dalam business logic atau cara berinteraksi dengan Model, kita tidak perlu mengubah keseluruhan Model. Dengan demikian, pemisahan ini membuat kode menjadi lebih modular, mudah untuk diuji (testable), dan lebih mudah untuk pemeliharaannya

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

    Jika kita hanya menggunakan Model tanpa memisahkan Service dan Repository, maka setiap model seperti Program, Subscriber, dan Notification akan memuat seluruh logika bisnis, aturan validasi, serta cara penyimpanan data di dalam satu entitas. Hal ini menyebabkan:

    - Peningkatan Kompleksitas: Setiap model akan memiliki tanggung jawab ganda (logika bisnis dan akses data), sehingga kode menjadi kompleks dan sulit dipahami.
    - Keterkaitan yang Tinggi: Jika terdapat interaksi atau ketergantungan antar model, misalnya saat Program mengirimkan notifikasi ke Subscriber, maka perubahan di satu model bisa berdampak ke model lainnya, membuat pemeliharaan dan pengujian menjadi sulit.
    - Kesulitan dalam Pengujian: Karena model mencakup banyak tanggung jawab, akan sulit untuk melakukan unit testing secara terisolasi, mengingat beberapa aspek (seperti logika bisnis dan akses data) saling terkait.
    - Resiko Error yang Lebih Tinggi: Kode yang tidak terpisah berdasarkan tanggung jawab meningkatkan resiko terjadinya bug saat terjadi perubahan, karena satu bagian yang mengubah logika bisnis bisa merusak alur penyimpanan data atau sebaliknya.

    Dengan demikian, memisahkan Service dan Repository dari Model membantu menjaga Single Responsibility Principle dan Separation of Concerns, sehingga masing-masing komponen lebih modular, mudah diuji, dan dipelihara.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.
    
    Postman adalah alat yang sangat membantu dalam menguji endpoint API. Dengan Postman, saya bisa:

    - Membuat dan Mengeksekusi Request: Saya dapat dengan mudah membuat request HTTP (GET, POST, PUT, DELETE, dan lain-lain) dan langsung melihat response dari server, yang membantu dalam memastikan API berfungsi sesuai harapan.
    - Collection Testing: Postman memungkinkan saya untuk menyimpan endpoint-endpoint API dalam sebuah collection, sehingga lebih terorganisir dan memudahkan pengujian sekaligus.
    - Environment Variables: Fitur ini memungkinkan saya untuk mengubah konfigurasi dengan mudah (misalnya URL host, token autentikasi, atau variabel lainnya), sehingga saya tidak perlu mengubah setiap request ketika berpindah antar environment (development, testing, production).

    Secara keseluruhan, Postman tidak hanya membantu dalam menguji dan memverifikasi API tetapi juga mempercepat proses debugging dan kolaborasi dalam proyek. Fitur-fitur yang saya sebutkan di atas sangat bermanfaat baik untuk proyek kelompok maupun untuk proyek pengembangan perangkat lunak di masa depan.
#### Reflection Publisher-3

1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?
    
    Menurut saya, dalam kasus tutorial ini yang kita gunakan adalah variasi Push Model. Hal ini terlihat karena publisher secara aktif mengirimkan notifikasi (melalui HTTP POST) dengan payload data ke setiap subscriber, sehingga data langsung didorong ke subscriber tanpa harus menunggu permintaan dari pihak subscriber.

2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)
    
    Berikut adalah keunggulan dan kekurangan jika kita menggunakan model pull :
    
    **Keunggulan:**
    - Kontrol Penuh pada Subscriber: Subscriber dapat menentukan kapan mereka akan mengambil data, sehingga bisa mengelola frekuensi update sesuai dengan kapasitas atau kebutuhan mereka.
    - Pengurangan Notifikasi Tidak Perlu: Karena data hanya diambil saat diperlukan, hal ini bisa menghindari penerimaan notifikasi yang tidak diinginkan jika subscriber tidak sedang siap atau tidak membutuhkan pembaruan secara real time.

    **Kekurangan:**
    - Polling Berulang: Subscriber harus secara berkala melakukan polling ke publisher untuk mengecek apakah ada data baru, yang bisa menyebabkan overhead jaringan dan beban pemrosesan yang tidak efisien.
    - Tidak Real-Time: Dibandingkan dengan Push Model, metode pull cenderung memiliki keterlambatan karena bergantung pada interval polling, sehingga notifikasi tidak diterima secara instan.
    - Kompleksitas Implementasi: Mengatur mekanisme polling yang optimal dan sinkronisasi data antara publisher dan banyak subscriber dapat meningkatkan kompleksitas implementasi sistem.

    Meskipun pull model memiliki keunggulan dalam hal kontrol dan potensi pengurangan notifikasi yang tidak perlu, untuk kasus tutorial ini yang lebih menekankan update secara real-time, Push Model lebih sesuai karena notifikasi didorong langsung dari publisher kepada subscriber.

3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.
    
    Jika tidak menggunakan multi-threading, proses pengiriman notifikasi akan dilakukan secara synchronous (berurutan) dalam thread yang sama. Artinya, publisher harus menunggu setiap notifikasi terkirim ke subscriber sebelum melanjutkan proses utama. Hal ini bisa mengakibatkan:

    - Respon API menjadi lambat karena proses notifikasi menambah waktu blocking.
    - Jika terdapat masalah pada salah satu notifikasi (misalnya, koneksi lambat atau timeout), seluruh proses bisa terhambat.
    - Beban kerja pada thread utama meningkat, sehingga mengurangi performa sistem secara keseluruhan.