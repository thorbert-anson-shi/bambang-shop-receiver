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
    | variable | type | description |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT | string | Port number that will be listened by this receiver instance. |
    | APP_INSTANCE_ROOT_URL | string | URL address where this receiver instance can be accessed. |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed. |
    | APP_INSTANCE_NAME | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:

    - Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    - Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    - Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)

- [✅] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
- **STAGE 1: Implement models and repositories**
  - [✅] Commit: `Create Notification model struct.`
  - [✅] Commit: `Create SubscriberRequest model struct.`
  - [✅] Commit: `Create Notification database and Notification repository struct skeleton.`
  - [✅] Commit: `Implement add function in Notification repository.`
  - [✅] Commit: `Implement list_all_as_string function in Notification repository.`
  - [✅] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
- **STAGE 3: Implement services and controllers**
  - [✅] Commit: `Create Notification service struct skeleton.`
  - [✅] Commit: `Implement subscribe function in Notification service.`
  - [✅] Commit: `Implement subscribe function in Notification controller.`
  - [✅] Commit: `Implement unsubscribe function in Notification service.`
  - [✅] Commit: `Implement unsubscribe function in Notification controller.`
  - [✅] Commit: `Implement receive_notification function in Notification service.`
  - [✅] Commit: `Implement receive function in Notification controller.`
  - [✅] Commit: `Implement list_messages function in Notification service.`
  - [✅] Commit: `Implement list function in Notification controller.`
  - [✅] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections

This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. A `Mutex<>` only allows one accessor to the data, be it read or write access. This is bad because we need multiple accessors to be able to read the notification list. If we were to use a mutex, then the process of reading notifications would take the same amount of time whether it was singlethreaded or multithreaded. In contrast, `RwLock<>` provides multiple read locks, and only one write lock, which is exactly what we need.

2. Rust is stricter when it comes to mutating state during runtime. It requires explicit synchronization tools such as the aforementioned `Mutex<>` and `RwLock<>` to be used in order to ensure proper state synchronization during runtime. If a static variable could simply be changed using a static function, this would allow for the existence of race conditions and synchronization issues, which Rust prevents from compile time.

#### Reflection Subscriber-2

1. While trying to understand where the requests to the `/receive` endpoint are coming from, I did check the contents of `lib.rs` to check if they are defined within it. After further examination, it seems to have been defined earlier in the service. However, now I know that `lib.rs` is used to define the app configuration, such as which ports it's running on, and also to declare shared convenience types and methods such as `Result<T>` and `compose_error_response()`.

   Other than `lib.rs`, the only "other" file is `main.rs`, which is as expected, where the program is first run, and top level server configurations are loaded.

2. The Observer pattern here allows publishers to publish to one uniform interface (the `Subscriber` struct from `bambangshop/src/model/subscriber.rs`). As long as the subscribers make subscription requests following the predefined interface, by providing a URL and name in this case, the subscriber can listen to the publisher quite seamlessly.

   This particular Observer doesn't seem to be optimized for having multiple instances of the main app running. However, if the application were to rely on a singular database, which in and of itself could be sharded further, then horizontal scaling on the main app would be of no issue. As long as they are relying on the same database, then having multiple instances of the main app running would be of no issue.

3. Due to a lack of time, I have not yet added my own tests. I feel like the tests provided in the Postman collection seems to be enough to test the happy paths of the API. In the future, perhaps adding more tests and documentation to the Postman collection could be useful. I believe I could use the Postman documentation tools to communicate between colleagues on the particular structure and required parameters of an API endpoint.
