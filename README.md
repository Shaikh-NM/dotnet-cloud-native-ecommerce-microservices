# 🛒 Distributed Cloud-Native E-Commerce Microservices

An enterprise-grade, cloud-native distributed e-commerce system built on **.NET 8**, adhering strictly to **Clean Architecture**, **Domain-Driven Design (DDD)**, and **CQRS (Command Query Responsibility Segregation)** patterns with **MediatR**. 

The solution is architected for high scalability, containerized with Docker, orchestrated using **Kubernetes (AKS)** with an **Istio Service Mesh**, and features an **Angular 18** client storefront.

---

## 🏗️ Architecture Overview

```text
                          ┌───────────────────────────┐
                          │   Angular 18 Storefront   │
                          └─────────────┬─────────────┘
                                        │
                                        ▼
                          ┌───────────────────────────┐
                          │     Ocelot / YARP         │
                          │      API Gateway          │
                          └─────────────┬─────────────┘
                                        │
             ┌──────────────────────────┼──────────────────────────┐
             ▼                          ▼                          ▼
   ┌───────────────────┐      ┌───────────────────┐      ┌───────────────────┐
   │  Catalog Service  │      │  Basket Service   │      │ Ordering Service  │
   │ (.NET 8 / MongoDB)│      │  (.NET 8 / Redis) │      │(.NET 8/PostgreSQL)│
   └───────────────────┘      └───────────────────┘      └───────────────────┘
             │                          ▲                          │
             │                          │ (gRPC)                   │
             │                ┌─────────┴─────────┐                │
             │                │  Discount Service │                │
             │                │ (.NET 8/PostgreSQL)                │
             │                └───────────────────┘                │
             │                                                     │
             └──────────────► [ Event Bus / Saga ] ◄───────────────┘
                              (RabbitMQ / MassTransit)
```

---

## 🧩 Microservices Ecosystem

| Microservice | Technology Stack | Storage / Cache | Communication |
|---|---|---|---|
| **Catalog** | ASP.NET Core 8, MediatR, FluentValidation | MongoDB | REST, Specification Pattern |
| **Basket** | ASP.NET Core 8, StackExchange.Redis | Redis Distributed Cache | REST, gRPC Client |
| **Discount** | ASP.NET Core 8, Dapper / EF Core | PostgreSQL / SQLite | gRPC Server |
| **Ordering** | ASP.NET Core 8, CQRS, MediatR, AutoMapper | SQL Server / PostgreSQL | Async Event-Driven / Saga |
| **Identity** | Duende IdentityServer / Azure AD | SQL Server | OAuth2, OpenID Connect |

---

## 🛠️ Catalog Microservice Internals (Clean Architecture)

The Catalog service is decoupled into four explicit layers:

* **`Catalog.Core` (Domain Layer):** Holds core entities (`Product`, `ProductBrand`, `ProductType`), repository abstractions (`IProductRepository`, `IBrandRepository`, `ITypeRepository`), and query specification models (`CatalogSpecParams`, `Pagination<T>`). Completely dependency-free.
* **`Catalog.Application` (Use Cases Layer):** Houses DTOs, CQRS Queries/Commands, and MediatR handlers for business orchestration.
* **`Catalog.Infrastructure` (Data & External Layer):** Contains MongoDB driver integration, collection configurations, database context, dynamic filter builders (`ApplyDataFilters`), and seeders (`DatabaseSeeder`).
* **`Catalog.API` (Presentation Layer):** Controllers exposing REST endpoints, Swagger documentation, and dependency injection composition root.

---

## 🚀 Key Features & Enterprise Patterns

* **Clean Architecture & Domain-Driven Design (DDD)** for long-term maintainability.
* **CQRS Pattern with MediatR** to cleanly separate read operations from write mutations.
* **Specification & Dynamic Filtering** for scalable searching, multi-criteria filtering, and pagination over MongoDB.
* **Distributed Sagas** for transactional consistency across Basket, Ordering, and Payment workflows.
* **Centralized Observability** using **.NET Aspire** and the **ELK Stack (Elasticsearch, Logstash, Kibana)** for structured logging and distributed tracing.
* **Service Mesh with Istio** for traffic shifting, rate limiting, and zero-trust mTLS encryption.
* **Container Orchestration** on **Kubernetes (AKS)** with **Helm** charts for environment management.

---

## 💻 Getting Started

### Prerequisites

* [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
* [Docker Desktop](https://www.docker.com/products/docker-desktop/)
* [MongoDB Compass / Community Server](https://www.mongodb.com/try/download/community)

### Running the Catalog Service Locally

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Shaikh-NM/dotnet-cloud-native-ecommerce-microservices.git
   cd dotnet-cloud-native-ecommerce-microservices
   ```

2. **Start MongoDB via Docker:**
   ```bash
   docker run -d -p 27017:27017 --name local-mongo mongo:latest
   ```

3. **Run the Catalog API:**
   ```bash
   cd src/Services/Catalog/Catalog.API
   dotnet run
   ```

4. Open `https://localhost:7000/swagger` (or the configured port) to test the seeded endpoints.
