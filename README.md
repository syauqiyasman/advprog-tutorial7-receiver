# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. RwLock vs Mutex:

    Dalam tutorial ini, kita menggunakan RwLock<Vec<Notification>> untuk menyinkronkan akses ke koleksi notifikasi kita. RwLock (Read-Write Lock) diperlukan karena memungkinkan beberapa pembaca untuk mengakses data secara bersamaan, sementara memastikan akses eksklusif saat menulis. Ini sangat penting untuk sistem notifikasi kita di mana:

   - Membaca notifikasi (menampilkannya) terjadi sering dan dapat dilakukan secara paralel.

   - Menambahkan notifikasi baru (menulis) terjadi lebih jarang tetapi memerlukan akses eksklusif.

    Kita tidak menggunakan Mutex karena akan kurang efisien dalam skenario ini. Mutex menyediakan akses eksklusif untuk operasi membaca dan menulis, yang berarti hanya satu thread yang dapat membaca notifikasi pada satu waktu. Ini akan membatasi kinerja secara tidak perlu ketika ada beberapa permintaan baca yang bersamaan, karena mereka harus menunggu satu sama lain meskipun tidak mengubah data.

2. Variabel Statik di Rust vs Java:

    Di Rust, variabel statik diperlakukan berbeda dari Java karena prinsip kepemilikan dan keamanan di Rust. Di Java, kita dapat dengan bebas mengubah variabel statik melalui metode statik tanpa banyak pertimbangan. Namun, Rust secara ketat menegakkan keamanan memori dan mencegah race data bahkan dengan variabel statik.

    Perbedaan utamanya adalah bahwa Rust memerlukan mekanisme sinkronisasi eksplisit (seperti RwLock atau Mutex) saat memodifikasi data statik yang dapat diubah untuk menjamin keamanan thread. Inilah mengapa kita menggunakan lazy_static yang digabungkan dengan RwLock - ini memungkinkan kita untuk:

   - Memiliki variabel "static" yang dapat diinisialisasi saat runtime.

   - Dengan aman mengubah isinya di beberapa thread.

   - Menjamin akses thread-safe ke data tersebut.

    Tanpa mekanisme ini, Rust tidak akan mengizinkan perubahan variabel statik karena dapat menyebabkan race data dan akses memori yang tidak aman dalam lingkungan multithreaded. Ini mencerminkan filosofi Rust tentang "konkurensi tanpa rasa takut" di mana kompiler menegakkan keamanan thread pada waktu kompilasi daripada menangani bug konkurensi pada waktu runtime.

#### Reflection Subscriber-2
1. Mengeksplorasi Lebih Lanjut di Luar Langkah-Langkah Tutorial:

    Ya, saya mengeksplorasi lebih jauh di luar langkah-langkah tutorial, khususnya di src/lib.rs. File ini berfungsi sebagai titik masuk aplikasi dan menunjukkan bagaimana framework Rocket dikonfigurasi. Melalui eksplorasi ini, saya belajar tentang:

    - Bagaimana sistem fairing Rocket bekerja untuk menambahkan fungsionalitas tambahan ke server

    - Bagaimana environtment variable dimuat dan digunakan di seluruh aplikasi

   - Bagaimana arsitektur aplikasi disusun dengan pemisahan yang jelas antara controller, service, dan repository

2. Pola Observer dan Skalabilitas:

    Pola Observer secara signifikan menyederhanakan penambahan lebih banyak subscriber ke sistem. Dengan implementasi kita:

    - Setiap instance Receiver baru dapat secara independen berlangganan ke jenis produk yang diminatinya
    - Publisher (aplikasi utama) tidak perlu mengetahui apa pun tentang Receiver baru sebelumnya
    - Menambahkan Receiver baru sesederhana memulai instance baru dengan port dan nama sendiri

    Pemisahan antara subject dan observer ini adalah kekuatan utama dari pola ini.

    Namun, menskalakan Publisher (aplikasi utama) akan lebih kompleks. Menjalankan beberapa instance aplikasi utama memerlukan:

    - Database bersama untuk informasi produk di seluruh instance Publisher
    - Mekanisme untuk menyinkronkan daftar subscription antara instance Publisher
    - Penyeimbangan beban untuk permintaan yang masuk

    Pola Observer saja tidak menyelesaikan kompleksitas ini, karena pada dasarnya dirancang untuk sistem notifikasi satu-ke-banyak. Untuk sistem banyak-ke-banyak dengan beberapa Publisher, kita akan memerlukan pola arsitektur tambahan seperti message broker atau event bus.

3. Pengujian dan Dokumentasi:

    Saya meningkatkan koleksi Postman dengan contoh dan deskripsi terperinci untuk setiap endpoint. Ini sangat berguna saat menguji aplikasi di beberapa instance. Dokumentasi membantu saya:

    - Mengingat tujuan dan kebutuhan setiap endpoint
    - Memahami format respons yang diharapkan
    - Memecahkan masalah dengan membandingkan respons aktual vs. yang diharapkan

    Untuk pekerjaan tutorial, memiliki panggilan API yang terdokumentasi dengan baik membuatnya lebih mudah untuk memverifikasi bahwa pola Observer berfungsi dengan benar. Untuk proyek kelompok, dokumentasi semacam itu akan sangat penting agar anggota tim memahami bagaimana berinteraksi dengan API tanpa perlu meninjau kode.
