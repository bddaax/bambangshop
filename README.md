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

> In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Untuk kasus BambangShop saat ini, saya rasa cukup menggunakan satu Model struct saja, tidak perlu menggunakan interface (trait). Alasannya adalah karena saat ini hanya ada satu jenis Subscriber, yaitu struct Subscriber, yang berisi url dan name sebagai atribut utama. Dalam pola desain Observer, penggunaan interface atau trait biasanya diperlukan jika kita memiliki banyak jenis Subscriber yang berbeda perilakunya, misalnya ada yang menerima notifikasi melalui API, ada yang menerima notifikasi melalui email, atau ada yang memproses notifikasi secara lokal dengan cara yang berbeda.

Namun, dalam implementasi yang kita gunakan di BambangShop, semua Subscriber memiliki perilaku yang sama, yaitu hanya menyimpan informasi dan menerima notifikasi melalui mekanisme yang seragam. Oleh karena itu, menambahkan trait saat ini akan membuat kode menjadi lebih kompleks tanpa manfaat yang jelas. Jika ke depannya ada kebutuhan untuk mendukung berbagai jenis Subscriber dengan metode notifikasi yang berbeda, maka saat itulah kita bisa mempertimbangkan untuk menggunakan trait agar implementasinya lebih fleksibel.


> id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Menurut saya, sudah tepat kita menggunakan DashMap dibandingkan Vec. Jika kita menggunakan Vec, maka kita harus melakukan iterasi setiap kali ingin mencari Subscriber berdasarkan url. Ini akan menjadi tidak efisien, terutama jika jumlah Subscriber bertambah banyak. Selain itu, jika kita hanya menggunakan Vec, kita harus memastikan sendiri bahwa url selalu unik, misalnya dengan melakukan pengecekan sebelum menambahkan data baru. Hal ini akan membuat proses menjadi lebih lambat dan lebih rentan terhadap bug jika ada kelalaian dalam pengecekan.

Dengan DashMap, kita bisa langsung menggunakan url sebagai key, sehingga setiap kali ingin menambahkan Subscriber, kita cukup melakukan operasi insert tanpa perlu iterasi. Selain itu, DashMap juga memungkinkan akses concurrent, yang sangat berguna jika kita ingin memastikan bahwa sistem bisa menangani banyak request dari berbagai thread secara aman dan efisien.

Selain itu, dalam implementasi kita, DashMap digunakan sebagai struktur data utama untuk menyimpan Subscriber berdasarkan kategori produk (product_type). Ini juga lebih fleksibel dibandingkan hanya menggunakan satu Vec, karena kita bisa dengan cepat mengakses daftar Subscriber berdasarkan kategori tertentu tanpa perlu menyaring hasil secara manual.


> When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

Dalam BambangShop, saya merasa penggunaan DashMap adalah pilihan yang lebih tepat dibandingkan menggantinya dengan pola desain Singleton. Hal ini karena DashMap sudah dirancang agar thread-safe tanpa memerlukan mekanisme locking manual.

Jika kita mengganti DashMap dengan Singleton yang menggunakan HashMap, maka kita perlu menambahkan mekanisme locking seperti Mutex atau RwLock. Masalahnya, jika banyak thread mencoba mengakses atau memperbarui data di HashMap secara bersamaan, mekanisme locking ini bisa menyebabkan bottleneck, yang mengurangi kinerja sistem. Selain itu, jika locking tidak dikelola dengan benar, ada potensi terjadinya deadlock, yang bisa membuat aplikasi menjadi tidak responsif.

Sebaliknya, DashMap sudah memiliki mekanisme internal yang memungkinkan pembacaan dan penulisan data secara concurrent tanpa harus memblokir seluruh map. Ini berarti kita bisa mengakses dan memperbarui data lebih cepat dibandingkan dengan HashMap yang dilindungi oleh Mutex. Oleh karena itu, untuk kebutuhan sistem BambangShop yang menggunakan banyak thread, saya rasa DashMap adalah pilihan yang lebih baik dibandingkan pola Singleton tradisional.

Namun, jika di masa depan ada kebutuhan untuk membatasi akses ke daftar Subscriber agar hanya bisa diakses oleh satu thread dalam kondisi tertentu, maka mungkin kita bisa mempertimbangkan untuk mengombinasikan DashMap dengan pola Singleton. Tetapi, untuk saat ini, DashMap sudah cukup untuk menangani kebutuhan aplikasi dengan cara yang lebih sederhana dan efisien.


#### Reflection Publisher-2

#### Reflection Publisher-3
