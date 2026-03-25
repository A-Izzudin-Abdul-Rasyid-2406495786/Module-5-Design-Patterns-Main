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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber
is defined as an interface. Explain based on your understanding of Observer design patterns,
do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model
struct is enough?

Dalam kasus BambangShop, penggunaan satu model struct tunggal untuk Subscriber sebenarnya sudah mencukupi karena perilaku notifikasi saat ini bersifat seragam, yaitu mengirimkan data melalui HTTP POST ke URL tertentu. Namun, penggunaan interface atau trait dalam Rust akan sangat dibutuhkan jika di masa depan terdapat kebutuhan untuk mendukung berbagai jenis pelanggan dengan mekanisme penerimaan pesan yang berbeda, seperti melalui Email, SMS, atau sistem antrean pesan, agar kode tetap fleksibel dan memenuhi prinsip keterbukaan terhadap perluasan (Open-Closed Principle).

2. id in Program and url in Subscriber is intended to be unique. Explain based on your
understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently
use is necessary for this case?

Penggunaan DashMap (atau dictionary) lebih diperlukan daripada Vec (list) karena DashMap menawarkan efisiensi pencarian, pembaruan, dan penghapusan data berdasarkan kunci unik (id produk atau url pelanggan) dengan kompleksitas waktu O(1). Jika menggunakan Vec, sistem harus melakukan iterasi linear (O(n)) setiap kali ingin memanipulasi data tertentu, yang akan menjadi tidak efisien seiring bertambahnya jumlah data serta mempersulit penanganan keunikan entitas tersebut secara langsung.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a
thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we
used the DashMap external library for thread safe HashMap. Explain based on your
understanding of design patterns, do we still need DashMap or we can implement Singleton
pattern instead?

Meskipun pola Singleton dapat memastikan hanya ada satu instans repositori atau basis data yang dibuat, kita tetap membutuhkan DashMap karena Rust memiliki aturan kepemilikan dan peminjaman yang ketat terhadap variabel statis yang diakses oleh banyak thread. DashMap menyediakan mekanisme sinkronisasi internal (seperti locking tingkat rendah) yang memungkinkan akses konkuren yang aman secara paralel tanpa menyebabkan data race, sehingga pola Singleton saja tidak cukup untuk menangani aspek keamanan thread tanpa adanya struktur data yang memang dirancang untuk konkurensi.

#### Reflection Publisher-2
1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”.
Model in MVC covers both data storage and business logic. Explain based on your
understanding of design principles, why we need to separate “Service” and “Repository” from
a Model?

Pemisahan Service dan Repository dari Model dilakukan untuk menerapkan prinsip Single Responsibility dan Separation of Concerns. Dalam struktur ini, Model hanya bertugas sebagai representasi data, Repository mengabstraksi detail penyimpanan data (seperti akses ke DashMap atau basis data), sementara Service menangani logika bisnis yang kompleks termasuk koordinasi antar model. Dengan pemisahan ini, kode menjadi lebih mudah diuji secara independen, lebih rapi, dan perubahan pada mekanisme penyimpanan data tidak akan merusak logika bisnis utama aplikasi.

2. What happens if we only use the Model? Explain your imagination on how the interactions
between each model (Program, Subscriber, Notification) affect the code complexity for
each model?

Jika kita hanya menggunakan Model untuk semua fungsi, kode akan menjadi sangat padat dan sulit dikelola karena satu kelas harus menangani skema data, kueri penyimpanan, hingga logika bisnis sekaligus. Interaksi antar model seperti Product, Subscriber, dan Notification akan menciptakan ketergantungan yang sangat erat (high coupling), di mana perubahan kecil pada struktur data Subscriber dapat mengharuskan perubahan besar pada logika pengiriman di Notification. Hal ini meningkatkan kompleksitas kognitif dan risiko bug, karena batas tanggung jawab antar entitas menjadi kabur dan sulit untuk dilacak seiring berkembangnya fitur aplikasi.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current
work. You might want to also list which features in Postman you are interested in or feel like it
is helpful to help your Group Project or any of your future software engineering projects.

Postman sangat membantu dalam memvalidasi fungsionalitas API secara langsung tanpa harus membuat antarmuka pengguna terlebih dahulu, seperti yang terlihat saat saya menguji rute subscribe. Melalui Postman, saya dapat mendeteksi kesalahan konfigurasi rute (seperti typo pada mounting path /notificaation) lebih cepat karena respon error yang jelas, hingga akhirnya berhasil mendapatkan status 201 Created saat mendaftarkan subscriber baru. Fitur seperti "Collections" sangat berguna untuk mengelompokkan berbagai permintaan API proyek grup agar dapat dibagikan dengan rekan tim, sementara fitur "Environments" memungkinkan perpindahan konfigurasi URL antara localhost dan server produksi dengan cepat. Selain itu, fitur pengujian otomatis (Scripts) dan dokumentasi API instan di Postman sangat mendukung efisiensi pengerjaan proyek perangkat lunak di masa depan agar integrasi antar modul berjalan lebih mulus.

#### Reflection Publisher-3
1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and
Pull model (subscribers pull data from publisher). In this tutorial case, which variation of
Observer Pattern that we use?

Dalam kasus tutorial ini, kita menggunakan variasi Push model. Hal ini dibuktikan dengan mekanisme di mana aplikasi (sebagai Publisher) secara aktif mengirimkan data langsung kepada Subscriber melalui HTTP POST request segera setelah terjadi suatu event, seperti pembuatan atau penghapusan produk. Subscriber bersikap pasif dan hanya perlu menyediakan endpoint URL untuk menerima payload notifikasi tersebut, tanpa harus mengecek atau meminta data ke Publisher secara berkala.

2. What are the advantages and disadvantages of using the other variation of Observer Pattern
for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Jika kita menggunakan variasi Pull model, keuntungannya adalah Publisher memiliki beban kerja yang lebih ringan karena ia hanya bertugas memperbarui status di database miliknya tanpa perlu melakukan HTTP request ke eksternal, sementara Subscriber memiliki kendali penuh untuk menarik data hanya saat mereka siap (menghindari kelebihan beban atau overload). Namun, kelemahan utamanya adalah hilangnya sifat real-time; Subscriber akan mengalami latensi dalam mengetahui adanya produk baru. Selain itu, Subscriber harus melakukan pengecekan berkala (polling) ke server Publisher, yang akan sangat memboroskan sumber daya jaringan dan komputasi server jika frekuensi polling tinggi namun tidak ada data baru yang tersedia.

3. Explain what will happen to the program if we decide to not use multi-threading in the
notification process.

Jika kita memutuskan untuk tidak menggunakan multi-threading (misalnya tanpa tokio::spawn pada asynchronous runtime Rocket) dalam proses notifikasi, program akan mengalami blocking atau berjalan secara sekuensial. Artinya, setiap kali sebuah produk dibuat, fungsi ProductService harus menunggu hingga seluruh HTTP POST request ke setiap URL Subscriber selesai dikirim satu per satu. Jika jumlah Subscriber sangat banyak, atau jika ada satu Subscriber yang servernya mati/lambat merespons (timeout), maka keseluruhan proses pembuatan produk akan tertunda sangat lama, sehingga API pembuat produk menjadi sangat tidak responsif dan mengganggu performa utama aplikasi.
