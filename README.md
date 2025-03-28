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
> In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Dalam kasus BambangShop, menurut saya penggunaan struct tunggal saja sudah cukup memadai menggantikan interface. Meskipun buku Head First Design Pattern menjelaskan Subscriber sebagai interface, konteks implementasi Rust memungkinkan pendekatan yang berbeda. Struktur `Subscriber` yang sederhana sudah mencakup seluruh kebutuhan dengan menyimpan informasi id, url, dan tipe produk. Keunggulan sistem tipe Rust yang baik memungkinkan pemeriksaan tipe pada saat kompilasi, sehingga mengurangi kebutuhan akan interface eksplisit. Selain itu, kompleksitas sistem notifikasi BambangShop relatif rendah, sehingga struktur tunggal dapat menangani kebutuhan dengan efisien.

> id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Penggunaan `DashMap` jauh lebih disarankan dibandingkan `Vec` untuk mengelola daftar subscriber. `DashMap` menawarkan keunggulan signifikan dalam hal kompleksitas waktu yang konstan (O(1)), sementara `Vec` memerlukan pencarian linier yang tidak efisien. Selain itu, `DashMap` secara otomatis menjamin keunikan kunci (dalam hal ini ID subscriber) dan memberikan mekanisme akses konkuren yang aman. Dengan `Vec`, developer harus secara manual melakukan pemeriksaan duplikasi dan pencarian, yang memperkenalkan potensi error dan menurunkan performance.

> When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

Meskipun Singleton Pattern bisa dipertimbangkan, penggunaan `DashMap` tetap menjadi pilihan terbaik dalam konteks pemrograman Rust. `DashMap` menyediakan mekanisme concurrent access yang sudah terintegrasi, menghindari kebutuhan implementasi mekanisme sinkronisasi manual yang rumit. Aturan kepemilikan dan peminjaman Rust sudah memberikan jaminan keamanan thread yang kuat, namun `DashMap` menambahkan lapisan perlindungan ekstra. Implementasi Singleton manual akan memerlukan penanganan sinkronisasi yang kompleks, sementara `DashMap` menawarkan solusi yang lebih idiomatis, aman, dan berperforma tinggi untuk menyimpan dan mengelola kumpulan subscriber dalam concurrency.

#### Reflection Publisher-2
> In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Dalam pola Model-View-Controller (MVC), pemisahan antara "Service" dan "Repository" bertujuan untuk menerapkan prinsip *Separation of Concerns*. Dengan memisahkan tugas, Model hanya bertanggung jawab atas representasi data, Service mengelola logika bisnis, dan Repository menangani interaksi dengan database. Hal ini membuat kode menjadi lebih modular, menjaga *Single Resposibility Principle*, mudah di-*maintain* serta di-*test*. Jika semua tanggung jawab diletakkan pada Model, kode akan menjadi terlalu kompleks, sulit dipahami, dan berisiko menyebabkan dependency yang tinggi antar bagian kode.
   
> What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Jika kita hanya menggunakan Model tanpa memisahkan Service dan Repository, semua logika bisnis dan operasi data akan bercampur dalam satu bagian kode. Hal ini akan membuat Model menjadi terlalu besar (*God Object*) dan sulit dikelola. Interaksi antara model seperti Program, Subscriber, dan Notification juga akan saling terkait secara langsung, sehingga menambah kompleksitas kode, membuat debugging dan penyesuaian fitur menjadi lebih rumit, serta menurunkan fleksibilitas dalam development di masa depan.

> Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Menurut saya, Postman adalah alat yang sangat berguna untuk menguji REST API. Dalam proyek ini, Postman membantu saya memastikan fungsi subscription dan unsubscription berjalan dengan baik tanpa perlu membuat antarmuka frontend. Fitur-fitur seperti penyimpanan request, pengelolaan koleksi, inspeksi detail request dan response, serta kemampuan untuk membuat otomatisasi pengujian dengan skrip, sangat membantu dalam pengujian API. Fitur-fitur ini juga akan berguna untuk pengembangan proyek perangkat lunak di masa depan, terutama dalam memastikan API tetap berfungsi dengan baik seiring perkembangan proyek.

#### Reflection Publisher-3
> Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

Dalam tutorial ini, kita menggunakan *Push Model* dari Observer Pattern. Pada model ini, *publisher* secara aktif mendorong (*push*) data berupa notifikasi ke semua *subscriber* setelah terjadi perubahan seperti pembuatan, promosi, atau penghapusan produk. Data tersebut dikirim melalui HTTP POST request ke URL milik *subscriber*.

> What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Jika kita menggunakan *Pull Model*, *subscriber* yang akan secara aktif menarik (*pull*) data dari *publisher*.  
**Keuntungan dari Pull Model:**  
- *Subscribers* bisa lebih fleksibel karena hanya menarik data ketika mereka membutuhkannya. Hal ini bisa mengurangi beban *publisher* karena tidak perlu terus-menerus mendorong notifikasi.  
**Kerugian dari Pull Model:**  
- Dapat menyebabkan latensi tinggi karena *subscriber* perlu terus memeriksa perubahan di *publisher* (polling). Hal ini juga tidak efisien untuk *real-time* notifikasi, di mana informasi terbaru sebaiknya dikirim segera.

> Explain what will happen to the program if we decide to not use multi-threading in the notification process.

Tanpa *multi-threading*, proses notifikasi akan berjalan secara sinkron dan *blocking*, yang berarti setiap notifikasi akan dikirim satu per satu secara berurutan. Hal ini dapat memperlambat kinerja aplikasi, terutama jika jumlah *subscriber* banyak. Dengan menggunakan *multi-threading*, kita dapat mengirim notifikasi secara paralel, yang meningkatkan efisiensi dan memungkinkan aplikasi menangani lebih banyak *subscriber* dengan lebih cepat. Tanpa ini, proses pengiriman notifikasi bisa menyebabkan aplikasi menjadi lambat dan tidak responsif.  