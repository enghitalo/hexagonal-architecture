# hexagonal-architecture

## Tree

```shell
src/
├── core/                  # Core business logic layer
│   ├── ports/             # Defines input and output ports (interfaces)
│   │   ├── input/         # Input ports (interfaces for inbound adapters)
│   │   │   └── user/      # User-related input interfaces
│   │   │       ├── IUserService.v    # Interface for user service (e.g., methods like createUser, getUser)
│   │   │       └── IUserRepository.v # Interface for user repository (e.g., methods like saveUser, getUserById)
│   │   └── output/        # Output ports (interfaces for outbound adapters)
│   │       ├── user/      # User repository output interfaces
│   │       │   └── IUserRepository.v # Interface for user repository (methods like updateUser, deleteUser)
│   │       └── payment/   # Payment-related output interfaces
│   │           └── IPaymentProcessor.v # Interface for payment processing (e.g., methods like processPayment)
│   ├── use_cases/         # Business rules and use case implementations
│   │   └── user/          # Use cases for user-related logic
│   │       ├── UserService.v        # Class implementing IUserService interface (e.g., createUser, getUser)
│   │       └── UserValidator.v      # Class implementing IUserValidator interface (e.g., validateUser)
│   │   └── payment/       # Use cases for payment-related logic
│   │       ├── PaymentProcessor.v   # Class implementing IPaymentProcessor interface (e.g., process payment logic)
│   └── dtos/              # Data Transfer Objects (DTOs) for data transfer
│       └── user/          # User DTOs
│       │   └── UserDTO.v  # User DTO (data structure to transfer user data)
│       └── payment/       # Payment DTOs
│           └── PaymentDTO.v # Payment DTO (data structure to transfer payment data)
├── adapters/              # Adapters connecting to external systems
│   ├── input/             # Inbound adapters (interfaces for user interaction)
│   │   ├── rest/          # RESTful HTTP API for user interaction
│   │   │   └── UserController.v     # Class implementing a REST controller (e.g., routes for user actions)
│   │   ├── cli/           # Command-line interface (if required)
│   │   ├── grpc/          # gRPC API (if required)
│   │   └── messaging/     # Message consumers
│   │       └── kafka/     # Kafka consumer for message-driven interactions
│   │           └── KafkaMessageConsumer.v  # Class implementing Kafka consumer (e.g., consuming messages)
│   └── output/            # Outbound adapters (interfaces for interacting with external systems)
│       ├── database/      # Database repositories and data persistence
│       │   └── UserRepository.v  # Class implementing IUserRepository (e.g., interact with the database)
│       ├── external/      # External service integrations (e.g., payment providers)
│       │   └── payment/   # Integrations for external payment gateways like Stripe, Mercado Pago, Pagar.me
│       │       ├── StripePaymentAdapter.v # Class implementing payment interface for Stripe
│       │       ├── MercadoPagoPaymentAdapter.v # Class implementing payment interface for MercadoPago
│       │       └── PagarmePaymentAdapter.v # Class implementing payment interface for Pagar.me
│       └── messaging/     # Message producers for outbound messaging
│           └── kafka/     # Kafka message producer for event-driven communication
│               └── KafkaMessageProducer.v # Class implementing Kafka message producer (e.g., publishing messages)
├── domain/                # Domain-specific entities and business rules
│   ├── models/            # Domain models (entities and value objects)
│   │   └── User.v         # User domain model (e.g., encapsulates user data and methods)
│   │   └── Payment.v      # Payment domain model (e.g., encapsulates payment data and methods)
│   └── exceptions/        # Custom exceptions for domain-specific business rule violations
│       └── UserNotFoundException.v # Custom exception for when a user is not found
│       └── PaymentFailureException.v # Custom exception for payment failure
├── config/                # Configuration files for the system
│   ├── app/               # General application configurations (e.g., environment variables)
│   ├── database/          # Database connection and settings
│   ├── security/          # Security configurations (e.g., authentication and authorization)
│   ├── logging/           # Logging configurations and management
│   └── messaging/         # Messaging configurations
│       └── kafka/         # Kafka-specific messaging configuration
├── tests/                 # Tests for the system
│   ├── unit/              # Unit tests for core components
│       ├── core/          # Tests for core business logic
│       ├── adapters/      # Tests for adapters
│       └── domain/        # Tests for domain models and exceptions
│   ├── integration/       # Integration tests for verifying interactions between components
│   └── e2e/               # End-to-end tests for simulating real-world workflows
└── main.v                 # Main entry point for the application

```
