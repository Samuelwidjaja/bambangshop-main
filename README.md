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
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
Dalam pola Observer, Subscriber biasanya didefinisikan sebagai sebuah antarmuka atau trait dalam Rust. Meskipun demikian, dalam kasus BambangShop, penggunaan antarmuka atau trait mungkin tidak sepenuhnya diperlukan jika tidak ada kebutuhan untuk banyak variasi implementasi dari Subscriber. Jika hanya terdapat satu implementasi Subscriber yang diperlukan, maka sebuah Model struct tunggal mungkin sudah cukup untuk menangani fungsi-fungsinya. Namun, penggunaan antarmuka atau trait tetap dapat bermanfaat jika kita ingin memisahkan kontrak antara Publisher dan Subscriber.

Ketika id dalam Program dan url dalam Subscriber dimaksudkan untuk unik, menggunakan Vec (list) tidak cukup karena mencari keunikan memerlukan iterasi melalui seluruh list. Sebaliknya, menggunakan DashMap (map/dictionary) seperti yang digunakan saat ini lebih tepat karena memungkinkan akses langsung dan memastikan keunikan id dengan kompleksitas waktu yang lebih rendah.

Dalam bahasa Rust, compiler menegakkan aturan ketat untuk membuat program yang aman secara bersamaan. Dalam kasus variabel statis SUBSCRIBERS, menggunakan DashMap memang merupakan pilihan yang baik karena memastikan operasi pada variabel tersebut dapat dilakukan dengan aman oleh beberapa thread secara bersamaan. Implementasi Singleton pattern mungkin tidak cukup untuk memastikan keamanan thread dalam konteks ini.

#### Reflection Publisher-2
1. Dalam pola compound Model-View-Controller (MVC), tidak terdapat "Service" dan "Repository". Model dalam MVC mencakup penyimpanan data dan logika bisnis. Namun, seiring dengan kompleksitas aplikasi yang berkembang, terkadang diperlukan pemisahan yang lebih jauh dengan memperkenalkan konsep "Service" dan "Repository". Ini karena:

- Pemisahan Concerns: Dengan memisahkan Model menjadi lapisan Service dan Repository, kita lebih ketat dalam memisahkan concern. Lapisan Service menangani logika bisnis aplikasi, sedangkan lapisan Repository menangani akses data dan persistensi. Pemisahan ini membuat basis kode lebih mudah dipahami, dipelihara, dan diskalakan.
- Fleksibilitas dan Keterulangan: Memisahkan Model menjadi lapisan Service dan Repository memudahkan dalam menukar implementasi.
- Pengujian: Pemisahan concern memudahkan dalam pengujian unit berbagai bagian aplikasi secara terisolasi.

2. Jika kita hanya menggunakan Model tanpa memisahkan menjadi lapisan Service dan Repository, interaksi antara model yang berbeda (seperti Program, Subscriber, dan Notification) kemungkinan akan menjadi sangat terikat. Ini dapat menyebabkan peningkatan kompleksitas kode dan penurunan keberlanjutan saat aplikasi berkembang. Misalnya:

- Tanpa pemisahan concern yang jelas, logika bisnis terkait Program, Subscriber, dan Notification mungkin menjadi terjalin dalam lapisan Model.
- Saat aplikasi berkembang dan fitur-fitur baru ditambahkan, mengelola interaksi dan ketergantungan antara model yang berbeda dapat menjadi tantangan.
- Kode terkait akses dan manipulasi data juga mungkin memenuhi lapisan Model, membuatnya sulit dipahami dan dipelihara.

3. Saya telah menggunakan Postman untuk menguji API dan berkolaborasi dalam pengembangan API. Postman menyediakan antarmuka yang ramah pengguna untuk mengirim permintaan ke API dan memeriksa respons. Biasanya, saya menggunakan Postman untuk mengirim permintaan HTTP ke ujung server dan memverifikasi apakah respons sesuai dengan harapan. Postman memungkinkan pengguna untuk mengatur permintaan ke dalam koleksi dan mendefinisikan lingkungan dengan variabel. Ini memudahkan dalam mengelola dan menjalankan kumpulan permintaan terkait, menyederhanakan proses pengujian.

#### Reflection Publisher-3
