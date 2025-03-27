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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

##### **1. Why do we use `RwLock<>` instead of `Mutex<>` for synchronizing the `Vec` of notifications?**  
In this tutorial, we use `RwLock<>` to allow multiple readers to access the `Vec<Notification>` at the same time while ensuring that only one writer can modify it. This is beneficial because:  
- **Efficiency**: Multiple threads can read the notifications simultaneously without blocking each other, improving performance.  
- **Thread Safety**: The `RwLock` ensures that only one thread can modify (write) the notifications at any given time, preventing data races.  

If we used `Mutex<>` instead, every access (both reading and writing) would require acquiring an exclusive lock, which means that even when multiple threads just need to read the data, they would have to wait for each other. This would cause unnecessary contention and reduce performance.  

##### **2. Why does Rust require `lazy_static` for static variables while Java allows direct mutation?**  
Rust enforces strict memory safety and ownership rules, which means that a mutable static variable must be explicitly synchronized to avoid data races. Unlike Java, where static variables can be mutated freely using static methods, Rust requires additional mechanisms (like `lazy_static!`) to safely initialize and manage mutable static variables. The reasons for this are:  
- **Thread Safety**: In Java, static variables are shared across threads, and developers must manually ensure synchronization. Rust prevents potential data races by making static variables immutable by default.  
- **Explicit Synchronization**: Rust enforces the use of `RwLock`, `Mutex`, or `Atomic` types to control concurrent access to static data.  
- **Lazy Initialization**: `lazy_static!` ensures that static variables are initialized safely at runtime, preventing issues like accessing an uninitialized variable.  

By using `lazy_static!`, we ensure that our `Vec<Notification>` and `DashMap` instances are safely initialized and can be used in a concurrent environment without violating Rust's safety guarantees.  

#### Reflection Subscriber-2

##### 1. Exploration Beyond the Tutorial Steps  
During the tutorial, I primarily followed the outlined steps to ensure correct implementation. However, I also explored additional files like `src/lib.rs` to better understand the overall project structure. From this exploration, I learned that `src/lib.rs` plays a crucial role in defining module visibility and dependencies. It acts as an entry point for organizing services, controllers, and repositories in a structured manner. Understanding this helped me grasp how different components interact within the application.

##### 2. Observer Pattern and Scalability  
After completing the tutorial and testing the notification system by spawning multiple Receiver instances, I realized how the **Observer pattern** simplifies the process of adding more subscribers. Since each Receiver instance acts as an observer, it can independently subscribe to notifications without modifying the main system. This flexibility allows for easy scalability—new instances can be added without disrupting existing ones.

However, spawning multiple **Main app instances** presents a different challenge. Unlike Receivers, which act as passive listeners, the Main app plays a central role in sending notifications. Running multiple Main app instances could lead to redundancy or conflicts in managing subscriptions. A proper solution would involve load balancing or a message queue system to handle multiple Main instances efficiently.

##### 3. Testing and Documentation Enhancements  
I attempted to create my own **unit tests** to validate functions like `subscribe_request`, `unsubscribe_request`, and `receive_notification`. Writing these tests was beneficial in catching potential issues early and ensuring each component worked as expected. Additionally, I enhanced the **Postman collection documentation** by adding descriptions for each API endpoint. This proved useful when debugging API requests and sharing the system with others in a team setting.