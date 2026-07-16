# System Architecture

## Architecture Style

The system follows a **Modular Monolith Architecture with Event-Driven Components**, designed to evolve into Microservices if required.

## Core Principles

- Separation of concerns
- Domain-driven modules
- Event-driven communication
- Scalability
- High availability
- Fault tolerance

## High-Level Architecture

```text
SeisComP
    │
    ▼
Event Processor
    │
    ▼
Redis Queue
    │
    ├── Notification Worker
    ├── Analytics Worker
    └── Archive Worker
            │
            ▼
      Flask REST API
            │
            ▼
         MySQL
            │
            ▼
      Mobile Applications
```