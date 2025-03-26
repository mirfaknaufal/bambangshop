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

1. In the Observer pattern, an interface (or trait in Rust) is typically used to define a contract for all subscribers, allowing flexibility for different implementations. In *BambangShop*, the *Subscriber* struct only holds data (`url` and `name`) without any behavior, making it more of a data model rather than an entity requiring polymorphism. Since the *SubscriberRepository* directly manages subscribers without any variation in behavior, a trait is unnecessary unless different types of subscribers with distinct notification mechanisms (e.g., HTTP, WebSocket, database logging) are required in the future. If all subscribers behave the same way, the current struct-based approach is sufficient; however, if flexibility is needed, implementing a `Subscriber` trait with different subscriber types (e.g., `HttpSubscriber`, `WebSocketSubscriber`) would be beneficial.

2. Using `DashMap` is necessary in this case because both `id` in `Program` and `url` in `Subscriber` are intended to be unique. A `Vec` would require iterating through all elements to check for uniqueness before inserting a new entry, which is inefficient, especially as the data grows.

3. Using the Singleton pattern alone is not sufficient for ensuring thread safety in Rust, as it only guarantees a single instance but does not handle concurrent access. Since multiple threads may read and write to `SUBSCRIBERS`, using a standard `HashMap` within a Singleton would require manual locking with `Mutex` or `RwLock`, which can introduce performance bottlenecks. In contrast, DashMap is specifically designed for efficient multi-threaded access, providing fine-grained locking and minimizing contention. From a design pattern perspective, DashMap functions as an enhanced Singleton that integrates thread safety, making it both a Singleton and a thread-safe shared resource, ensuring optimal performance and safety in concurrent environments.

#### Reflection Publisher-2

1. In traditional MVC, the Model handles both business logic and data storage, but separating Service and Repository improves scalability, maintainability, and testability. The Repository abstracts database operations, while the Service manages business logic, ensuring a cleaner, reusable, and modular design. This separation enhances flexibility, allows easier testing, and supports scaling, making modern architectures more efficient.

2. If we only use the Model without separating Service and Repository, interactions between Program, Subscriber, and Notification would become tightly coupled, increasing complexity and reducing maintainability. Each model would handle data storage, business logic, and cross-model communication, leading to duplicate logic, harder testing, and reduced modularity. 

3. Postman is a powerful API testing tool that simplifies development by allowing users to send GET, POST, PUT, DELETE, and other HTTP requests without writing client-side code.  Postman’s Collections feature is extremely useful for organizing and managing API requests efficiently. Instead of manually entering request details each time, a Collection groups related API endpoints, making it easier to test different functionalities of an application.

#### Reflection Publisher-3

1. In this tutorial case, we use the Push model of the Observer Pattern, where the publisher actively sends data to subscribers when an event occurs. SubscriberRepository stores subscriber details, and when a relevant event happens, the system pushes notifications to the subscriber’s URL instead of waiting for them to request updates. Unlike the Pull model, where subscribers periodically poll the publisher for new data, this approach ensures real-time updates and reduces unnecessary API calls, making it more efficient for our use case.

2. If we used the Pull model instead of Push, subscribers would fetch updates only when needed, reducing unnecessary notifications and allowing them to recover missed updates if their system is down. It would also improve scalability by preventing the publisher from sending updates to all subscribers, reducing server load. However, this approach introduces higher latency since updates wouldn’t be real-time, increases API requests as subscribers must repeatedly poll the publisher, and adds complexity by requiring subscribers to implement periodic update checks. In this tutorial case, Pull would make notifications less efficient and delay updates, making Push the better choice for immediate notifications.

3. If we don’t use multi-threading in the notification process, notifications will be sent sequentially, causing delays if a subscriber is slow or unresponsive. This blocking behavior can make the system sluggish and unresponsive, especially as the number of subscribers grows. Without parallel processing, the system won’t scale well, leading to longer notification times and potential timeout issues if a request takes too long. Additionally, it results in inefficient CPU usage, as multiple cores won’t be utilized, making the system less efficient in handling high volumes of notifications.