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
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
>In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Menurut saya, dalam kasus BambangShop saat ini, tidak perlu menggunakan trait (interface version dalam Rust) karena hanya ada satu jenis subscriber. Trait biasanya digunakan saat kita memiliki beberapa jenis subscriber dengan perilaku berbeda, sehingga perlu disatukan dalam satu pola yang sama. Namun, jika ke depannya kita ingin mendukung berbagai jenis subscriber dengan logika yang berbeda, maka penggunaan trait akan lebih masuk akal.

>id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Menurut saya, penggunaan DashMap lebih tepat dibandingkan Vec, karena kita membutuhkan cara yang efisien buat menyimpan dan mencari subscriber berdasarkan id atau url yang unik. Kalau menggunakan Vec, pencarian atau penghapusan data harus dilakukan dengan iterasi satu per satu (O(n)), yang bisa jadi bakal lambat kalau jumlah subscribernya banyak. Sementara itu, DashMap sendiri bisa/memungkinkan pencarian, penambahan, dan penghapusan dalam O(1) waktu konstan, sehingga jauh lebih cepat dan lebih efisien. Selain itu, DashMap juga thread-safe, yang mana ini sangat penting dalam aplikasi web agar tidak terjadi konflik saat banyak pengguna melakukan subscribe atau unsubscribe secara bersamaan.

>When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

kalau diliat lagi, dalam `repository/subscriber.rs` sudah diimplementasikan `lazy_static!` yang membuat satu static instance dari DashMap yang mana merupakan cara pengimplementasian singleton pattern sehingga dalam App ini, DashMap dan Singleton pattern diimplementasikan secara bersamaan. Namun, hanya menggunakan Singleton saja tidak cukup untuk menangani akses data secara bersamaan (concurrent access) di aplikasi web. DashMap lebih unggul karena sudah mendukung mekanisme thread-safe dengan locking yang lebih efisien dibandingkan jika kita harus membuatnya sendiri menggunakan Mutex atau RwLock. Jadi, menurut saya tetap menggunakan DashMap adalah pilihan yang lebih mudah, praktis, dan optimal.

#### Reflection Publisher-2
>In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?
 
perlakuan pemisahan antara Repository dan Service ini sebenarnya lebih berkaitan kuat dengan salah satu SOLID Principle yaitu Single Responsibility Principle (SRP), dimana terdapat pemisahan yang jelas untuk perilaku yang berbeda. Model hanya berfungsi dalam representasi data saja sedangkan Repository menangani interaksi dengan menyimpan dan modifikasi data. Service berfokus kepada logika bisnis tanpa mengubah data yang ada. Dengan pendekatan ini, kode menjadi lebih modular dan lebih mudah di-maintain.

>What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Jika kita hanya mengandalkan Model tanpa Service dan Repository, maka kompleksitas kode akan meningkat drastis. Model bakal bekerja sangat keras menangani logika bisnis, pengambilan data, dan juga pengiriman notifikasi dalam satu tempat. Yang seperti ini justru juga melanggar prinsip SRP yang sempat saya mention di soal sebelumnya. Jika terdapat _update_ ke salah satu method, maka akan sangat banyak yang harus di-fix karena banyak code yang saling terkait. Istilah ini sering juga disebut dengna _shotgun surgery_. 

>Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Sebelumnya, saya sudah pernah sedikit mencoba dan mengeksplorasi Postman saat mata kuliah pemrograman berbasis platform (pbp). Menurut saya, Postman sangat membantu dalam pengujian sistem notifikasi BambangShop. Dengan Postman, saya bisa dengan mudah mengirim permintaan HTTP ke aplikasi publisher dan subscriber tanpa perlu membuat interface frontend terlebih dahulu. Selain itu, adanya fitur visualisasi request dan response sangat membantu dalam menganalisis dan mengidentifikasi kesalahan pada implementasi API dengan lebih cepat.

#### Reflection Publisher-3
>Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?
 
Pada project ini, observer pattern yang digunakan adalah Push Model yang diimplementasikan via notification dimana publisher mengirimkan permintaan HTTP Post notifikasi ke semua subscribers setiap kali ada modifikasi terhadap sebuah produk. Hal ini dapat terlihat dari terdapatnya kode `NotificationService::notify()` di `src/service/product.rs`.

intinya, Publisher (aplikasi utama) menyimpan daftar subscriber beserta URL mereka, dan publisher bertanggung jawab untuk memberi tahu setiap subscriber dengan mengirimkan data notifikasi melalui metode update() milik subscriber. Subscriber sendiri tidak perlu melakukan pengecekan secara berkala, mereka hanya menunggu hingga mendapatkan notifikasi melalui endpoint /receive. Dari cara kerja yang sudah saya sebutkan tadi, saya bisa kembali menyimpulkan bahwa pendekatan yang digunakan adalah Push model, di mana inisiatif komunikasi berasal dari sisi publisher, dan data langsung dikirimkan ke subscriber tanpa perlu diminta terlebih dahulu.

>What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Jika menggunakan pull, terdapat beberapa keuntungan seperti berkurangnya redundansi transfer data, lebih banyak kontrol pada observer sehingga aplikasi lebih ringan, dan yang utamanya itu kita tidak harus mengirimkan notifikasi kepada setiap Subscriber tiap kali ada perubahan pada product, melainkan subscribernya itu bebas kapan saja request perubahan kepada product terkait. Sementara sisi kekurangan yang bakal terjadi, Subscriber harus terus-menerus mengirim permintaan (polling) ke aplikasi utama, yang bisa menyebabkan lalu lintas jaringan yang tidak perlu jika tidak ada pembaruan. Kemudian ada juga potensi notifikasi tidak langsung diterima subscriber, karena mereka baru mendapatkan pembaruan saat polling berikutnya, sehingga ada latensi dalam penyampaian informasi.

>Explain what will happen to the program if we decide to not use multi-threading in the notification process.

kalau kita memilih atau memutuskan untuk tidak menggunakan multi-threading dalam program, maka kinerjanya akan menjadi lambat karena notifikasi diproses secara berurutan, literally satu per satu. Jika jumlah subscriber banyak, sistem akan berjalan lebih lambat karena setiap proses harus menunggu proses sebelumnya selesai. Namun, dengan multi-threading, notifikasi dapat dikirim secara paralel, sehingga tidak mengganggu aplikasi utama dan dapat meningkatkan kinerja sistem secara keseluruhan. contoh simplenya, contoh simplenya, Jika setiap notifikasi membutuhkan waktu 750ms untuk dikirim, maka memberi tahu 100 subscriber akan memakan waktu 75 detik sebelum pengguna mendapatkan respons dari sistem. Thread utama akan terblokir, sehingga aplikasi tidak bisa menangani permintaan lain selama proses notifikasi berlangsung.