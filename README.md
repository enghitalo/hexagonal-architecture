# hexagonal-architecture

## Tree

```shell
src/
├── core/                               # Core business logic layer
│   ├── ports/                              # Defines input and output ports (interfaces)
│   │   ├── input/                              # Input ports (interfaces for inbound adapters)
│   │   │   └── user/                               # User-related input interfaces
│   │   │       ├── IUserService.v                  # Interface for user service (e.g., methods like createUser, getUser)
│   │   │       └── IUserRepository.v               # Interface for user repository (e.g., methods like saveUser, getUserById)
│   │   └── output/                             # Output ports (interfaces for outbound adapters)
│   │       ├── user/                               # User repository output interfaces
│   │       │   └── IUserRepository.v               # Interface for user repository (methods like updateUser, deleteUser)
│   │       └── payment/                            # Payment-related output interfaces
│   │           └── IPaymentProcessor.v             # Interface for payment processing (e.g., methods like processPayment)
│   ├── use_cases/                          # Business rules and use case implementations
│   │   └── user/                                   # Use cases for user-related logic
│   │       ├── UserService.v                       # Class implementing IUserService interface (e.g., createUser, getUser)
│   │       └── UserValidator.v                     # Class implementing IUserValidator interface (e.g., validateUser)
│   │   └── payment/                                # Use cases for payment-related logic
│   │       ├── PaymentProcessor.v                  # Class implementing IPaymentProcessor interface (e.g., process payment logic)
│   └── dtos/                               # Data Transfer Objects (DTOs) for data transfer
│       └── user/                                   # User DTOs
│       │   └── UserDTO.v                           # User DTO (data structure to transfer user data)
│       └── payment/                                # Payment DTOs
│           └── PaymentDTO.v                        # Payment DTO (data structure to transfer payment data)
├── adapters/                           # Adapters connecting to external systems
│   ├── input/                              # Inbound adapters (interfaces for user interaction)
│   │   ├── rest/                                   # RESTful HTTP API for user interaction
│   │   │   └── UserController.v                    # Class implementing a REST controller (e.g., routes for user actions)
│   │   ├── cli/                                    # Command-line interface (if required)
│   │   ├── grpc/                                   # gRPC API (if required)
│   │   └── messaging/                              # Message consumers
│   │       └── kafka/                              # Kafka consumer for message-driven interactions
│   │           └── KafkaMessageConsumer.v          # Class implementing Kafka consumer (e.g., consuming messages)
│   └── output/                         # Outbound adapters (interfaces for interacting with external systems)
│       ├── database/                       # Database repositories and data persistence
│       │   └── UserRepository.v                    # Class implementing IUserRepository (e.g., interact with the database)
│       ├── external/                       # External service integrations (e.g., payment providers)
│       │   └── payment/                            # Integrations for external payment gateways like Stripe, Mercado Pago, Pagar.me
│       │       ├── StripePaymentAdapter.v          # Class implementing payment interface for Stripe
│       │       ├── MercadoPagoPaymentAdapter.v     # Class implementing payment interface for MercadoPago
│       │       └── PagarmePaymentAdapter.v         # Class implementing payment interface for Pagar.me
│       └── messaging/                              # Message producers for outbound messaging
│           └── kafka/                              # Kafka message producer for event-driven communication
│               └── KafkaMessageProducer.v          # Class implementing Kafka message producer (e.g., publishing messages)
├── domain/                             # Domain-specific entities and business rules
│   ├── models/                             # Domain models (entities and value objects)
│   │   └── User.v                                  # User domain model (e.g., encapsulates user data and methods)
│   │   └── Payment.v                               # Payment domain model (e.g., encapsulates payment data and methods)
│   └── exceptions/                                 # Custom exceptions for domain-specific business rule violations
│       └── UserNotFoundException.v         # Custom exception for when a user is not found
│       └── PaymentFailureException.v               # Custom exception for payment failure
├── config/                             # Configuration files for the system
│   ├── app/                                        # General application configurations (e.g., environment variables)
│   ├── database/                                   # Database connection and settings
│   ├── security/                                   # Security configurations (e.g., authentication and authorization)
│   ├── logging/                                    # Logging configurations and management
│   └── messaging/                                  # Messaging configurations
│       └── kafka/                                  # Kafka-specific messaging configuration
├── tests/                              # Tests for the system
│   ├── unit/                                       # Unit tests for core components
│   │   ├── core/                                   # Tests for core business logic
│   │   ├── adapters/                               # Tests for adapters
│   │   └── domain/                                 # Tests for domain models and exceptions
│   ├── integration/                                # Integration tests for verifying interactions between components
│   └── e2e/                                        # End-to-end tests for simulating real-world workflows
└── main.v                                          # Main entry point for the application
```
* Command line to generate all folders
```sh
mkdir -p src/{core/{ports/{input,output}/user,use_cases/user,dtos/user},adapters/{input/{rest,cli,grpc,messaging/kafka},output/{database,external/payment,messaging/kafka}},domain/{models,exceptions},config/{app,database,security,logging,messaging/kafka},tests/{unit/{core,adapters,domain},integration,e2e}}
```

<!-- IUserService.v -->

```v
// src/core/ports/input/user/IUserService.v
interface IUserService {
    fn create_user(user_data UserDTO) ?User
    fn get_user_by_id(id string) ?User
    fn update_user(id string, user_data UserDTO) ?User
    fn delete_user(id string) bool
    fn list_users() []User
}
```

<!-- IUserRepository.v -->

```v
// src/core/ports/input/user/IUserRepository.v
interface IUserRepository {
    fn save(user User) ?User
    fn find_by_id(id string) ?User
    fn find_all() []User
    fn update(id string, user User) ?User
    fn delete(id string) bool
}
```

<!-- IPaymentProcessor.v -->

```v
// src/core/ports/output/payment/IPaymentProcessor.v
interface IPaymentProcessor {
    fn process_payment(payment_data PaymentDTO) ?Payment
    fn refund_payment(payment_id string) bool
    fn get_payment_status(payment_id string) ?PaymentStatus
    fn verify_payment(payment_id string) bool
}
```

<!-- Payment.v -->

```v
// src/domain/models/Payment.v
module domain

pub struct Payment {
    id string
    amount f64
    currency string
    status PaymentStatus
    created_at time.Time
    updated_at time.Time
}

pub enum PaymentStatus {
    pending
    completed
    failed
    refunded
}
```

<!-- PaymentProcessor.v -->

```v
// src/core/use_cases/payment/PaymentProcessor.v
module payment

import core.dtos.payment { PaymentDTO }
import core.ports.output.payment { IPaymentProcessor }
import domain { Payment, PaymentStatus }
import time

pub struct PaymentProcessor {
    payment_provider IPaymentProcessor
}

pub fn (pp PaymentProcessor) process_payment(payment_data PaymentDTO) ?Payment {
    if payment_data.amount <= 0 {
        return error('Invalid payment amount')
    }

    payment := Payment{
        id: uuid_v4()
        amount: payment_data.amount
        currency: payment_data.currency
        status: .pending
        created_at: time.now()
        updated_at: time.now()
    }

    processed_payment := pp.payment_provider.process_payment(payment_data) or {
        payment.status = .failed
        return error('Payment processing failed')
    }

    payment.status = .completed
    payment.updated_at = time.now()
    return payment
}

pub fn (pp PaymentProcessor) refund_payment(payment_id string) bool {
    return pp.payment_provider.refund_payment(payment_id)
}

pub fn (pp PaymentProcessor) get_payment_status(payment_id string) ?PaymentStatus {
    return pp.payment_provider.get_payment_status(payment_id)
}

pub fn (pp PaymentProcessor) verify_payment(payment_id string) bool {
    return pp.payment_provider.verify_payment(payment_id)
}
```

<!-- PaymentDTO.v -->

```v
// src/core/dtos/payment/PaymentDTO.v
module payment

pub struct PaymentDTO {
    amount f64
    currency string
    payment_method string
    description string
}
```

<!-- PaymentFailureException.v -->

```v
// src/domain/exceptions/PaymentFailureException.v
struct PaymentFailureException {
    Error
    message string
    payment_id string
    error_code int
}

fn (e PaymentFailureException) msg() string {
    return 'Payment failure: ${e.message} (Payment ID: ${e.payment_id}, Code: ${e.error_code})'
}
```

<!-- User.v -->

```v
// src/domain/models/User.v
module domain

pub struct User {
    id string
    name string
    email string
    created_at time.Time
}
```

<!-- UserDTO.v -->

```v
// src/core/dtos/user/UserDTO.v
module user

pub struct UserDTO {
    name string
    email string
}
```

<!-- UserService.v -->

```v
// src/core/use_cases/user/UserService.v
module user

import core.dtos.user
import core.ports.input.user { IUserService }
import core.ports.output.user { IUserRepository }

pub struct UserService {
    repo IUserRepository
}

pub fn (service UserService) create_user(user_data UserDTO) ?User {
    // Validate user data
    validate_user(user_data)?
    // Convert DTO to domain entity
    user := User{
        id: uuid_v4()
        name: user_data.name
        email: user_data.email
    }
    return service.repo.save(user)
}

pub fn (service UserService) get_user_by_id(id string) ?User {
    return service.repo.find_by_id(id)
}

pub fn (service UserService) update_user(id string, user_data UserDTO) ?User {
    existing_user := service.repo.find_by_id(id)?
    updated_user := User{
        id: existing_user.id
        name: user_data.name or { existing_user.name }
        email: user_data.email or { existing_user.email }
    }
    return service.repo.update(id, updated_user)
}

pub fn (service UserService) delete_user(id string) bool {
    return service.repo.delete(id)
}

pub fn (service UserService) list_users() []User {
    return service.repo.find_all()
}

fn validate_user(user_data UserDTO) ? {
    if user_data.name.len == 0 {
        return error('Name is required')
    }
    if user_data.email.len == 0 || !user_data.email.contains('@') {
        return error('Valid email is required')
    }
}
```

<!-- UserController.v -->

```v
// src/adapters/input/rest/UserController.v
module rest

import core.use_cases.user { UserService }
import core.dtos.user { UserDTO }
import net.http

pub struct UserController {
    service UserService
}

['POST']
pub fn (ctrl UserController) create_user(req http.Request) http.Response {
    user_data := json.decode(UserDTO, req.data) or {
        return http.bad_request('Invalid user data')
    }
    new_user := ctrl.service.create_user(user_data) or {
        return http.server_error('Error creating user')
    }
    return http.ok(json.encode(new_user))
}

['GET']
pub fn (ctrl UserController) get_user(req http.Request) http.Response {
    id := req.url.query_params['id'] or { return http.bad_request('Missing user ID') }
    user := ctrl.service.get_user_by_id(id) or {
        return http.not_found('User not found')
    }
    return http.ok(json.encode(user))
}

```

<!-- main.v -->

```v
// src/main.v
module main

import core.use_cases.user { UserService }
import core.use_cases.payment { PaymentProcessor }
import adapters.input.rest { UserController }
import adapters.output.database { PostgresUserRepository }
import adapters.output.external.payment { StripePaymentAdapter }

fn main() {
    // Initialize repositories and external services
    user_repo := PostgresUserRepository{}
    payment_provider := StripePaymentAdapter{}

    // Initialize core services with dependencies
    user_service := UserService{
        repo: user_repo
    }
    payment_processor := PaymentProcessor{
        payment_provider: payment_provider
    }

    // Initialize controllers
    user_controller := UserController{
        service: user_service
    }

    // Start HTTP server
    server := http.Server{
        port: 8080
        handler: create_router(user_controller)
    }
    server.listen() or { panic(err) }
}

fn create_router(user_controller UserController) &http.Router {
    mut router := http.new_router()

    router.post('/users', user_controller.create_user)
    router.get('/users/:id', user_controller.get_user)

    return router
}
```

## Injection

The application uses dependency injection through interfaces to maintain loose coupling between components. Here are examples of how dependencies are injected:

```v
// UserService with repository injection
user_repo := PostgresUserRepository{}
user_service := UserService{
    repo: user_repo
}

// PaymentProcessor with provider injection
stripe_provider := StripePaymentAdapter{}
payment_processor := PaymentProcessor{
    payment_provider: stripe_provider
}

// UserController with service injection
controller := UserController{
    service: user_service
}
```

This allows for:

- Easy component replacement
- Testability through mocking
- Separation of concerns
- Runtime dependency configuration

## Types and Structures

The application uses several key types and structures:

### Domain Models

- `User`: Represents user entity with ID, name, and email
- `Payment`: Represents payment transactions with amount, status, and timestamps

### Data Transfer Objects (DTOs)

- `UserDTO`: Simplified user data for API interactions
- `PaymentDTO`: Payment information for processing transactions

### Interfaces

- `IUserService`: Core business logic for user operations
- `IUserRepository`: Data persistence operations for users
- `IPaymentProcessor`: Payment processing operations

### Status Types

- `PaymentStatus`: Enum for payment states (pending, completed, failed, refunded)

### Exception Types

- `PaymentFailureException`: Custom error for payment processing failures
