---
layout: page
title: "Domain Driven Design (DDD)"
---

## ❓ Qué problema resuelve
- **Complejidad del dominio**: Cuando el negocio es complejo y las reglas cambian frecuentemente
- **Comunicación**: Desalineación entre desarrolladores y expertos del dominio
- **Mantenibilidad**: Código que no refleja la realidad del negocio

## 🔧 Cómo funciona
DDD se enfoca en modelar el software basándose en el dominio del negocio, creando un lenguaje ubicuo entre desarrolladores y expertos del dominio.

### Conceptos Clave:
- **Entities**: Objetos con identidad única
- **Value Objects**: Objetos inmutables sin identidad
- **Aggregates**: Grupos de entidades relacionadas
- **Repositories**: Abstracción para persistencia
- **Domain Services**: Lógica que no pertenece a una entidad específica

## 📊 Diagrama de Arquitectura

```mermaid
graph TB
    subgraph "Bounded Context: E-commerce"
        subgraph "Domain Layer"
            E[Order Entity]
            VO[Money Value Object]
            A[Order Aggregate]
            DS[PricingService]
            DR[Domain Rules]
        end
        
        subgraph "Application Layer"
            AS[OrderApplicationService]
            CH[CreateOrderCommandHandler]
            QH[GetOrderQueryHandler]
        end
        
        subgraph "Infrastructure Layer"
            R[OrderRepository]
            DB[(Database)]
            MSG[Message Queue]
        end
        
        subgraph "Presentation Layer"
            API[REST Controllers]
            WEB[Web Interface]
        end
    end
    
    API --> AS
    WEB --> AS
    AS --> CH
    AS --> QH
    CH --> A
    QH --> R
    A --> E
    A --> VO
    A --> DS
    R --> DB
    AS --> MSG
```

## 🏗️ Estructura de Paquetes DDD

```
src/main/java/com/company/ecommerce/
├── domain/                          # Domain Layer
│   ├── model/
│   │   ├── order/                   # Order Aggregate
│   │   │   ├── Order.java          # Entity
│   │   │   ├── OrderId.java        # Value Object
│   │   │   ├── OrderItem.java      # Entity
│   │   │   ├── OrderStatus.java    # Enum
│   │   │   └── OrderRepository.java # Repository Interface
│   │   ├── customer/               # Customer Aggregate
│   │   │   ├── Customer.java
│   │   │   ├── CustomerId.java
│   │   │   └── CustomerRepository.java
│   │   └── shared/                 # Shared Value Objects
│   │       ├── Money.java
│   │       ├── Address.java
│   │       └── Email.java
│   ├── service/                    # Domain Services
│   │   ├── PricingService.java
│   │   ├── InventoryService.java
│   │   └── OrderValidationService.java
│   └── event/                      # Domain Events
│       ├── OrderCreatedEvent.java
│       ├── OrderShippedEvent.java
│       └── DomainEvent.java
├── application/                     # Application Layer
│   ├── service/
│   │   ├── OrderApplicationService.java
│   │   └── CustomerApplicationService.java
│   ├── command/                    # Commands (CQRS)
│   │   ├── CreateOrderCommand.java
│   │   ├── UpdateOrderCommand.java
│   │   └── CommandHandler.java
│   ├── query/                      # Queries (CQRS)
│   │   ├── GetOrderQuery.java
│   │   ├── OrderQueryHandler.java
│   │   └── OrderProjection.java
│   └── dto/                        # Data Transfer Objects
│       ├── CreateOrderRequest.java
│       ├── OrderResponse.java
│       └── CustomerResponse.java
├── infrastructure/                  # Infrastructure Layer
│   ├── persistence/
│   │   ├── jpa/
│   │   │   ├── OrderJpaRepository.java
│   │   │   ├── CustomerJpaRepository.java
│   │   │   └── JpaOrderRepository.java
│   │   └── entity/                 # JPA Entities
│   │       ├── OrderEntity.java
│   │       ├── CustomerEntity.java
│   │       └── OrderItemEntity.java
│   ├── messaging/
│   │   ├── EventPublisher.java
│   │   ├── OrderEventHandler.java
│   │   └── RabbitMQConfig.java
│   └── external/                   # External Services
│       ├── PaymentGateway.java
│       ├── EmailService.java
│       └── InventoryService.java
└── presentation/                    # Presentation Layer
    ├── rest/
    │   ├── OrderController.java
    │   ├── CustomerController.java
    │   └── GlobalExceptionHandler.java
    └── web/
        ├── OrderWebController.java
        └── CustomerWebController.java
```

## 📋 Diagrama de Clases - Order Aggregate

```mermaid
classDiagram
    class Order {
        -id: OrderId
        -customerId: CustomerId
        -items: List~OrderItem~
        -status: OrderStatus
        -totalAmount: Money
        +addItem(product, quantity)
        +removeItem(productId)
        +confirm()
        +ship()
        +cancel()
        +calculateTotal(): Money
    }
    
    class OrderId {
        -value: String
        +of(value): OrderId
        +getValue(): String
    }
    
    class OrderItem {
        -productId: ProductId
        -productName: String
        -quantity: int
        -unitPrice: Money
        +getSubtotal(): Money
    }
    
    class Money {
        -amount: BigDecimal
        -currency: Currency
        +add(other): Money
        +multiply(factor): Money
        +isZero(): boolean
    }
    
    class OrderStatus {
        <<enumeration>>
        PENDING
        CONFIRMED
        SHIPPED
        DELIVERED
        CANCELLED
    }
    
    class OrderRepository {
        <<interface>>
        +save(order): void
        +findById(id): Optional~Order~
        +findByCustomerId(customerId): List~Order~
    }
    
    Order --> OrderId
    Order --> OrderItem
    Order --> Money
    Order --> OrderStatus
    OrderItem --> Money
```

## 🎯 Bounded Contexts

```mermaid
graph TB
    subgraph "E-commerce Domain"
        subgraph "Sales Context"
            SC1[Order Management]
            SC2[Customer Management]
            SC3[Product Catalog]
        end
        
        subgraph "Inventory Context"
            IC1[Stock Management]
            IC2[Warehouse Operations]
            IC3[Supplier Relations]
        end
        
        subgraph "Shipping Context"
            SH1[Delivery Management]
            SH2[Tracking System]
            SH3[Carrier Integration]
        end
        
        subgraph "Payment Context"
            PC1[Payment Processing]
            PC2[Billing Management]
            PC3[Financial Reporting]
        end
    end
    
    SC1 -.->|Anti-Corruption Layer| IC1
    SC1 -.->|Published Events| SH1
    SC1 -.->|Shared Kernel| PC1
```

## 🔄 Aggregate Design Patterns

### Event Sourcing Integration
```mermaid
classDiagram
    class OrderAggregate {
        -uncommittedEvents: List~DomainEvent~
        +addItem(product, quantity)
        +markChangesAsCommitted()
        +getUncommittedEvents(): List~DomainEvent~
    }
    
    class DomainEvent {
        <<abstract>>
        -eventId: String
        -occurredOn: LocalDateTime
        -aggregateId: String
    }
    
    class OrderCreatedEvent {
        -orderId: String
        -customerId: String
        -totalAmount: Money
    }
    
    class OrderItemAddedEvent {
        -orderId: String
        -productId: String
        -quantity: int
    }
    
    OrderAggregate --> DomainEvent
    DomainEvent <|-- OrderCreatedEvent
    DomainEvent <|-- OrderItemAddedEvent
```

### Repository Pattern
```mermaid
classDiagram
    class OrderRepository {
        <<interface>>
        +save(order): void
        +findById(id): Optional~Order~
        +findByCustomerId(customerId): List~Order~
    }
    
    class JpaOrderRepository {
        -orderJpaRepository: OrderJpaRepository
        -orderMapper: OrderMapper
        +save(order): void
        +findById(id): Optional~Order~
        +findByCustomerId(customerId): List~Order~
    }
    
    class OrderJpaRepository {
        <<interface>>
        +save(entity): OrderEntity
        +findById(id): Optional~OrderEntity~
        +findByCustomerId(customerId): List~OrderEntity~
    }
    
    class OrderMapper {
        +toDomain(entity): Order
        +toEntity(order): OrderEntity
    }
    
    OrderRepository <|.. JpaOrderRepository
    JpaOrderRepository --> OrderJpaRepository
    JpaOrderRepository --> OrderMapper
```

## 🎨 Domain Services vs Application Services

```mermaid
graph TB
    subgraph "Domain Layer"
        DS[Domain Services]
        DS1[PricingService]
        DS2[OrderValidationService]
        DS3[InventoryAllocationService]
    end
    
    subgraph "Application Layer"
        AS[Application Services]
        AS1[OrderApplicationService]
        AS2[CustomerApplicationService]
        AS3[ReportingApplicationService]
    end
    
    subgraph "Infrastructure Layer"
        IS[Infrastructure Services]
        IS1[EmailService]
        IS2[PaymentGateway]
        IS3[AuditLogger]
    end
    
    AS1 --> DS1
    AS1 --> DS2
    AS1 --> DS3
    AS1 --> IS1
    AS1 --> IS2
    AS1 --> IS3
```

## 🏛️ Hexagonal Architecture Integration

```mermaid
graph TB
    subgraph "Hexagon - Application Core"
        subgraph "Domain"
            DOM[Domain Models]
            SER[Domain Services]
        end
        
        subgraph "Application"
            UC[Use Cases]
            PORT[Ports]
        end
    end
    
    subgraph "Primary Adapters"
        REST[REST API]
        WEB[Web UI]
        CLI[CLI]
    end
    
    subgraph "Secondary Adapters"
        DB[Database]
        MSG[Message Queue]
        EXT[External APIs]
    end
    
    REST --> UC
    WEB --> UC
    CLI --> UC
    
    UC --> PORT
    PORT --> DB
    PORT --> MSG
    PORT --> EXT
    
    UC --> DOM
    UC --> SER
```

## 📊 Métricas y Monitoreo DDD

### Aggregate Health
```mermaid
graph LR
    A[Aggregate Metrics] --> B[Size Monitoring]
    A --> C[Event Frequency]
    A --> D[Consistency Checks]
    
    B --> E[Too Large Warning]
    C --> F[High Activity Alert]
    D --> G[Invariant Violations]
```

### Bounded Context Mapping
```mermaid
graph TB
    subgraph "Context Map"
        A[Sales Context] -->|Customer/Supplier| B[Inventory Context]
        A -->|Published Language| C[Shipping Context]
        A -->|Shared Kernel| D[Payment Context]
        
        B -->|Anti-Corruption Layer| E[Legacy System]
        C -->|Open Host Service| F[External Carriers]
    end
```