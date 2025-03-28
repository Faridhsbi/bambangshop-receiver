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
-   [V] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [V] Commit: `Create Notification model struct.`
    -   [V] Commit: `Create SubscriberRequest model struct.`
    -   [V] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [V] Commit: `Implement add function in Notification repository.`
    -   [V] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [V] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [V] Commit: `Create Notification service struct skeleton.`
    -   [V] Commit: `Implement subscribe function in Notification service.`
    -   [V] Commit: `Implement subscribe function in Notification controller.`
    -   [V] Commit: `Implement unsubscribe function in Notification service.`
    -   [V] Commit: `Implement unsubscribe function in Notification controller.`
    -   [V] Commit: `Implement receive_notification function in Notification service.`
    -   [V] Commit: `Implement receive function in Notification controller.`
    -   [V] Commit: `Implement list_messages function in Notification service.`
    -   [V] Commit: `Implement list function in Notification controller.`
    -   [V] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. > In this tutorial, we used RwLock<> to synchronise the use of Vec of Notifications. Explain why it is necessary for this case, and explain why we do not use Mutex<> instead?

    Mengapa RwLock?:

    - RwLock memungkinkan beberapa thread membaca data secara bersamaan ketika tidak ada thread yang melakukan penulisan sehingga dapat meningkatkan performa saat operasi baca (read) sering dilakukan, seperti pada metode `list_all_as_string()`.

    - Dengan RwLock, resource bersama (Vec of Notification) tetap aman dari race condition karena hanya mengunci akses tulis (write) ketika terjadi modifikasi, sementara akses baca dapat berlangsung paralel.

    Mengapa tidak menggunakan Mutex?:

    - Mutex akan mengunci resource untuk setiap operasi, baik itu read maupun write, sehingga menyebabkan bottleneck ketika banyak thread melakukan operasi baca sekaligus.

    - Penggunaan Mutex mengurangi paralelisme dan dapat menurunkan performa sistem karena setiap akses harus menunggu giliran.

2. > In this tutorial, we used lazy_static external library to define Vec and DashMap as a “static” variable. Compared to Java where we can mutate the content of a static variable via a static function, why did not Rust allow us to do so?

    `lazy_static` pada Rust:

    - Crate `lazy_static` menyediakan cara aman untuk menginisialisasi variabel statik yang memerlukan komputasi runtime, seperti `Vec<Notification>` atau `DashMap`, sehingga hanya diinisialisasi sekali ketika pertama kali diakses.

   -  Variabel statik tersebut dibungkus dengan `synchronization primitive` (misalnya RwLock atau Mutex), yang memungkinkan akses mutable secara aman dan terkontrol di antara banyak thread.

    Perbandingan dengan Java:

    - Di Java, variabel statik bersifat mutable secara default dan bisa diubah melalui metode statik, namun dalam pengelolaan keamanan thread harus dilakukan secara eksplisit (misalnya dengan menggunakan blok synchronized).

    - Sedangkan Rust menegakkan keamanan thread sejak waktu kompilasi sehingga variabel global yang mutable harus dibungkus dalam primitif yang aman terhadap concurrency, seperti `RwLock`, yang memastikan semua akses sudah terjamin keamanannya tanpa pemeriksaan tambahan saat runtime.

#### Reflection Subscriber-2

1. > Have you explored things outside of the steps in the tutorial, for example: src/lib.rs? If not, explain why you did not do so. If yes, explain things that you have learned from those other parts of code.

    Saya telah mengeksplorasi file di luar langkah-langkah tutorial, seperti `src/lib.rs` dan modul-modul terkait lainnya, yang memberikan saya pemahaman mendalam tentang cara inisialisasi konfigurasi global dan client HTTP secara thread-safe menggunakan `lazy_static`. Dari hasil eksplorasi tersebut, saya belajar bagaimana integrasi environment configuration dilakukan melalui penggunaan dotenvy dan Figment untuk menggabungkan variabel lingkungan dengan pengaturan default secara robust. Selain itu, pendekatan error handling yang konsisten dengan custom error type dan fungsi standar untuk response error turut meningkatkan keterbacaan serta konsistensi aplikasi secara keseluruhan. Secara keseluruhan, eksplorasi ini memperkuat pemahaman saya mengenai pemisahan concerns dan desain aplikasi yang scalable, yang sangat bermanfaat dalam pengembangan aplikasi web yang robust dan mudah dikelola.

2. > Since you have completed the tutorial by now and have tried to test your notification system by spawning multiple instances of Receiver, explain how Observer pattern eases you to plug in more subscribers. How about spawning more than one instance of Main app, will it still be easy enough to add to the system?

    Pada tutorial ini, Observer pattern dengan model push memudahkan dalam penambahan subscriber baru karena publisher tidak perlu mengetahui detail implementasi tiap subscriber, setiap subscriber hanya perlu memenuhi antarmuka yang diharapkan, dan `NotificationService` secara otomatis mengirimkan notifikasi dengan cara mengiterasi daftar subscriber dan menjalankan `update()` pada masing-masing secara parallel menggunakan thread. Pendekatan ini memungkinkan skalabilitas tinggi, sehingga ketika ada penambahan instance pada Main App, masing-masing publisher dapat bekerja secara independen tanpa mengganggu mekanisme lainnya, meskipun untuk lingkungan terdistribusi mungkin diperlukan koordinasi tambahan seperti *message broker*.

1. > Have you tried to make your own Tests, or enhance documentation on your Postman collection? If you have tried those features, tell us whether it is useful for your work (it can be your tutorial work or your Group Project).

    Saya telah mencoba untuk membuat test sendiri serta meningkatkan dokumentasi dalam Postman collection, yang terbukti sangat bermanfaat untuk memastikan setiap endpoint berjalan sesuai harapan. Dengan menulis unit dan integration tests, saya dapat secara otomatis memverifikasi alur notifikasi, penanganan data, dan respon error, sehingga memungkinkan deteksi masalah sedini mungkin. Peningkatan dokumentasi Postman dengan menambahkan deskripsi mendetail, contoh payload, dan skrip otomatisasi testing juga mempermudah pemahaman dan kolaborasi antar anggota tim, yang pada akhirnya meningkatkan keandalan sistem dan mendukung pengembangan aplikasi yang lebih efisien.