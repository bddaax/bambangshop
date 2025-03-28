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

> In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Dalam BambangShop, saya merasa bahwa memisahkan Service dan Repository dari Model adalah keputusan yang tepat karena beberapa alasan utama.

Pertama, pemisahan ini mengikuti prinsip Separation of Concerns (SoC), di mana setiap bagian dari kode memiliki tanggung jawab yang jelas. Repository bertanggung jawab untuk menangani akses data, sedangkan Service menangani logika bisnis. Jika kita menggabungkan semuanya dalam Model, kode akan menjadi terlalu kompleks dan sulit untuk dikelola.

Kedua, pemisahan ini meningkatkan fleksibilitas. Dengan memiliki Repository yang khusus menangani penyimpanan data, kita dapat dengan mudah mengganti implementasi penyimpanan tanpa perlu mengubah bagian lain dari kode. Misalnya, jika saat ini kita menggunakan DashMap tetapi ingin beralih ke database relasional seperti PostgreSQL di masa depan, kita hanya perlu memodifikasi Repository tanpa harus mengubah logika bisnis yang ada di Service.

Ketiga, pemisahan ini memudahkan pengujian. Dengan adanya lapisan Service yang terpisah dari Repository, kita bisa menulis unit test untuk logika bisnis di Service tanpa harus benar-benar terhubung dengan penyimpanan data. Ini membantu meningkatkan kualitas kode dan mengurangi kemungkinan bug.


> What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Jika kita hanya menggunakan Model tanpa Service dan Repository, kode akan menjadi jauh lebih kompleks dan sulit dikelola. Berikut beberapa masalah yang kemungkinan akan terjadi:

Pertama, kesulitan dalam memisahkan logika bisnis dan akses data.
Saat ini, Repository menangani penyimpanan Subscriber, sedangkan Service menangani logika bisnis seperti validasi atau manipulasi data sebelum disimpan. Jika kita menghilangkan kedua lapisan ini, maka semua operasi akan terjadi di dalam Model, menyebabkan kode Model menjadi terlalu besar dan sulit dipahami.

Kedua, kode menjadi sulit diuji. Jika semua logika bisnis dan akses data ada di dalam Model, maka pengujian akan menjadi lebih sulit karena kita tidak dapat dengan mudah mengisolasi bagian tertentu untuk diuji secara unit. Misalnya, jika kita ingin menguji apakah Subscriber berhasil ditambahkan, kita harus menjalankan seluruh kode termasuk penyimpanan data, yang mungkin tidak selalu diinginkan dalam pengujian unit.

Ketiga, meningkatkan ketergantungan antar Model
Dalam BambangShop, ada beberapa Model utama seperti Program, Subscriber, dan Notification. Jika kita hanya mengandalkan Model tanpa adanya Service, maka setiap Model akan langsung berinteraksi satu sama lain. Misalnya, jika Notification ingin mengakses daftar Subscriber, ia harus langsung berinteraksi dengan Subscriber tanpa melalui Service. Ini bisa menyebabkan keterkaitan yang terlalu erat (tight coupling), sehingga sulit untuk melakukan perubahan di satu bagian tanpa mempengaruhi bagian lain.


> Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Ya, saya telah melakukan sedikit eksplorasi tentang Postman, dan saya merasa alat ini sangat membantu dalam pengujian API yang sedang saya kerjakan di BambangShop.

Dalam BambangShop, saya menggunakan Postman untuk menguji endpoint subscribe dan unsubscribe yang telah saya buat di dalam Notification Controller. Dengan Postman, saya bisa dengan mudah mengirim permintaan HTTP POST ke endpoint subscribe/<product_type> dengan data Subscriber, dan memeriksa apakah Subscriber berhasil ditambahkan. Begitu juga dengan endpoint unsubscribe/<product_type>?<url>, saya bisa langsung mengecek apakah Subscriber berhasil dihapus dari sistem.

Dengan fitur Collection, saya bisa menyimpan semua permintaan API yang terkait dengan BambangShop dalam satu tempat, sehingga tidak perlu mengetik ulang setiap kali ingin melakukan pengujian. Selain itu, Environment Variables memungkinkan saya untuk menyimpan nilai seperti base_url agar lebih mudah beradaptasi jika ada perubahan server.


#### Reflection Publisher-3

> Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

Dalam implementasi notification mechanism di BambangShop, kita menggunakan Push Model, di mana publisher (NotificationService) secara langsung mengirimkan notifikasi ke subscriber begitu terjadi perubahan status pada produk.

Pada implementasi ini, ketika sebuah produk dibuat (create), dihapus (delete), atau diterbitkan (publish), NotificationService secara otomatis mencari daftar subscriber dan mengirimkan notifikasi HTTP POST ke URL yang telah didaftarkan oleh masing-masing subscriber.

Kode di NotificationService memperlihatkan bagaimana notifikasi dikirim menggunakan `thread::spawn(move || subscriber_clone.update(payload_clone));`. Ini menunjukkan bahwa begitu ada perubahan dalam sistem, publisher segera memberitahu subscriber tanpa menunggu permintaan dari mereka.


> What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Jika kita menggunakan Pull Model, maka alih-alih publisher mengirimkan notifikasi secara langsung, subscriber harus secara aktif mengambil informasi terbaru dari publisher dengan mengirim permintaan ke server secara berkala (misalnya menggunakan polling atau mekanisme lainnya).

Keuntungan Pull Model:
1) Mengurangi beban pada publisher : 
Dalam Push Model, setiap perubahan memicu notifikasi ke semua subscriber, yang bisa membebani server jika jumlah subscriber sangat banyak. Dalam Pull Model, subscriber hanya menarik data saat dibutuhkan, mengurangi lonjakan beban pada publisher.

2) Menghindari notifikasi yang tidak perlu : 
Jika subscriber tidak terlalu sering membutuhkan pembaruan, mereka bisa mengatur sendiri kapan harus mengambil informasi terbaru. Ini lebih efisien dibandingkan mengirim notifikasi setiap ada perubahan kecil.

3) Meningkatkan fleksibilitas pada sisi subscriber : 
Subscriber dapat mengatur kapan mereka ingin melakukan permintaan ke publisher, sehingga mereka bisa menghindari pemrosesan data di waktu yang tidak diinginkan.

Kerugian Pull Model:
1) Menambah latensi dalam mendapatkan informasi terbaru : 
Jika subscriber hanya menarik data secara berkala, ada kemungkinan mereka tidak langsung mendapatkan pembaruan saat terjadi perubahan. Ini bisa menjadi masalah dalam kasus notifikasi real-time seperti di BambangShop.

2) Membutuhkan mekanisme tambahan di sisi subscriber : 
Dengan Pull Model, subscriber harus memiliki mekanisme untuk secara berkala melakukan polling ke server. Ini bisa menjadi lebih kompleks dan memerlukan sumber daya lebih banyak di sisi klien.

3) Kurang efisien jika banyak subscriber : 
Jika banyak subscriber terus-menerus meminta data ke server, ini bisa menciptakan beban yang lebih besar daripada jika publisher hanya mengirim notifikasi saat ada perubahan.


Dalam BambangShop, Push Model lebih sesuai karena kita menginginkan notifikasi segera dikirim begitu ada perubahan pada produk. Jika kita menggunakan Pull Model, notifikasi bisa tertunda dan membebani subscriber karena mereka harus terus melakukan polling ke server untuk mendapatkan pembaruan terbaru.


> Explain what will happen to the program if we decide to not use multi-threading in the notification process.

Saat ini, proses notifikasi diimplementasikan dengan multi-threading, di mana setiap subscriber menerima notifikasi dalam sebuah thread terpisah menggunakan `thread::spawn(move || subscriber_clone.update(payload_clone));`.

Jika kita tidak menggunakan multi-threading, beberapa hal yang akan terjadi adalah:

Pertama, notifikasi menjadi lambat dan terblokir. Tanpa multi-threading, pengiriman notifikasi akan dilakukan secara berurutan (synchronous). Jika ada banyak subscriber, server harus mengirimkan notifikasi satu per satu. Ini berarti jika ada satu subscriber yang lambat atau tidak responsif, proses pengiriman ke subscriber berikutnya akan tertunda.
Menurunkan performa sistem

Jika server harus menangani banyak transaksi lain (misalnya pembuatan dan penghapusan produk), tanpa multi-threading, setiap pemrosesan notifikasi akan menghalangi eksekusi operasi lainnya. Ini bisa menyebabkan waktu respons API menjadi lebih lama.

Kedua, tidak bisa menangani jumlah subscriber yang besar. Jika sistem memiliki ratusan atau ribuan subscriber, tanpa multi-threading server akan kesulitan mengirimkan notifikasi dengan cepat. Ini bisa menyebabkan penumpukan antrian proses yang memperlambat keseluruhan sistem.