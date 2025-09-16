---
layout: page
title: "Command Query Responsibility Segregation (CQRS)"
---

## ❓ Qué problema resuelve
- **Modelos complejos**: Cuando el modelo de lectura y escritura tienen diferentes necesidades
- **Performance**: Optimización independiente de consultas y comandos
- **Escalabilidad**: Escalar lecturas y escrituras por separado
- **Complejidad de consultas**: Queries complejas que no encajan en el modelo de dominio

## 🔧 Cómo funciona
CQRS separa las operaciones de lectura (Queries) de las operaciones de escritura (Commands), permitiendo optimizar cada lado independientemente.

### Conceptos Clave:
- **Commands**: Operaciones que modifican el estado
- **Queries**: Operaciones que leen datos
- **Command Handlers**: Procesan comandos
- **Query Handlers**: Procesan consultas
- **Read Models**: Modelos optimizados para lectura
- **Write Models**: Modelos optimizados para escritura

## 📊 Diagrama de Arquitectura

```mermaid
graph TB
    subgraph "Client Applications"
        UI[User Interface]
        API[API Clients]
    end
    
    subgraph "CQRS Application"
        subgraph "Command Side (Write)"
            CMD[Commands]
            CH[Command Handlers]
            WM[Write Model]
            WDB[(Write Database)]
        end
        
        subgraph "Query Side (Read)"
            QRY[Queries]
            QH[Query Handlers]
            RM[Read Model]
            RDB[(Read Database)]
        end
        
        subgraph "Event Store"
            ES[Event Store]
            EP[Event Projections]
        end
    end
    
    UI --> CMD
    UI --> QRY
    API --> CMD
    API --> QRY
    
    CMD --> CH
    CH --> WM
    WM --> WDB
    WM --> ES
    
    QRY --> QH
    QH --> RM
    RM --> RDB
    
    ES --> EP
    EP --> RM
```

## 🏗️ Estructura de Paquetes CQRS

```
src/main/java/com/company/ecommerce/
├── command/                         # Command Side (Write)
│   ├── handler/
│   │   ├── CreateProductCommandHandler.java
│   │   ├── UpdateProductCommandHandler.java
│   │   ├── DeleteProductCommandHandler.java
│   │   └── CommandHandler.java
│   ├── model/                       # Write Models
│   │   ├── Product.java
│   │   ├── ProductRepository.java
│   │   └── ProductAggregate.java
│   ├── dto/
│   │   ├── CreateProductCommand.java
│   │   ├── UpdateProductCommand.java
│   │   └── DeleteProductCommand.java
│   └── validation/
│       ├── ProductCommandValidator.java
│       └── CommandValidator.java
├── query/                           # Query Side (Read)
│   ├── handler/
│   │   ├── GetProductQueryHandler.java
│   │   ├── GetProductsQueryHandler.java
│   │   ├── SearchProductsQueryHandler.java
│   │   └── QueryHandler.java
│   ├── model/                       # Read Models
│   │   ├── ProductView.java
│   │   ├── ProductListView.java
│   │   ├── ProductSearchView.java
│   │   └── ProductReadRepository.java
│   ├── dto/
│   │   ├── GetProductQuery.java
│   │   ├── GetProductsQuery.java
│   │   └── SearchProductsQuery.java
│   └── projection/                  # Event Projections
│       ├── ProductProjection.java
│       ├── ProductListProjection.java
│       └── ProjectionHandler.java
├── event/                           # Events & Event Store
│   ├── store/
│   │   ├── EventStore.java
│   │   ├── EventStoreRepository.java
│   │   └── StoredEvent.java
│   ├── publisher/
│   │   ├── EventPublisher.java
│   │   └── DomainEventPublisher.java
│   └── domain/
│       ├── ProductCreatedEvent.java
│       ├── ProductUpdatedEvent.java
│       ├── ProductDeletedEvent.java
│       └── DomainEvent.java
├── infrastructure/                  # Infrastructure
│   ├── persistence/
│   │   ├── command/                # Write Database
│   │   │   ├── ProductWriteRepository.java
│   │   │   └── JpaProductRepository.java
│   │   └── query/                  # Read Database
│   │       ├── ProductReadRepository.java
│   │       └── ProductViewRepository.java
│   ├── messaging/
│   │   ├── EventBus.java
│   │   ├── RabbitMQEventBus.java
│   │   └── EventHandler.java
│   └── config/
│       ├── CommandConfig.java
│       ├── QueryConfig.java
│       └── EventConfig.java
└── api/                            # API Layer
    ├── command/
    │   ├── ProductCommandController.java
    │   └── CommandController.java
    ├── query/
    │   ├── ProductQueryController.java
    │   └── QueryController.java
    └── dispatcher/
        ├── CQRSDispatcher.java
        └── MessageDispatcher.java
```

## 📋 Diagrama de Clases - Command Side

```mermaid
classDiagram
    class CreateProductCommand {
        +name: String
        +price: BigDecimal
        +category: String
        +description: String
    }
    
    class CreateProductCommandHandler {
        -repository: ProductRepository
        -eventPublisher: EventPublisher
        +handle(command): ProductId
    }
    
    class Product {
        -id: ProductId
        -name: String
        -price: BigDecimal
        -category: String
        +updatePrice(newPrice)
        +updateName(newName)
        +isActive(): boolean
    }
    
    class ProductRepository {
        <<interface>>
        +save(product): void
        +findById(id): Optional~Product~
        +delete(id): void
    }
    
    class EventPublisher {
        +publish(event): void
        +publishAll(events): void
    }
    
    CreateProductCommandHandler --> CreateProductCommand
    CreateProductCommandHandler --> Product
    CreateProductCommandHandler --> ProductRepository
    CreateProductCommandHandler --> EventPublisher
```

## 📋 Diagrama de Clases - Query Side

```mermaid
classDiagram
    class GetProductsQuery {
        +category: String
        +page: int
        +size: int
        +sortBy: String
    }
    
    class GetProductsQueryHandler {
        -readRepository: ProductReadRepository
        +handle(query): ProductListView
    }
    
    class ProductView {
        +id: String
        +name: String
        +price: BigDecimal
        +category: String
        +categoryName: String
        +stockQuantity: int
        +averageRating: double
        +reviewCount: int
        +isAvailable: boolean
    }
    
    class ProductReadRepository {
        <<interface>>
        +findByCategory(category, pageable): List~ProductView~
        +searchByName(name, pageable): List~ProductView~
        +findTopRated(limit): List~ProductView~
        +findByPriceRange(min, max): List~ProductView~
    }
    
    class ProductListView {
        +products: List~ProductView~
        +totalCount: long
        +page: int
        +size: int
    }
    
    GetProductsQueryHandler --> GetProductsQuery
    GetProductsQueryHandler --> ProductReadRepository
    GetProductsQueryHandler --> ProductListView
    ProductListView --> ProductView
```

## 🔄 Event Flow Diagram

```mermaid
sequenceDiagram
    participant Client
    participant CommandAPI
    participant CommandHandler
    participant WriteDB
    participant EventStore
    participant EventBus
    participant Projection
    participant ReadDB
    participant QueryAPI
    
    Client->>CommandAPI: CreateProductCommand
    CommandAPI->>CommandHandler: handle(command)
    CommandHandler->>WriteDB: save(product)
    CommandHandler->>EventStore: store(event)
    CommandHandler->>EventBus: publish(ProductCreatedEvent)
    EventBus->>Projection: handle(event)
    Projection->>ReadDB: update(productView)
    
    Note over Client: Later...
    Client->>QueryAPI: GetProductsQuery
    QueryAPI->>ReadDB: findByCategory()
    ReadDB-->>QueryAPI: ProductView[]
    QueryAPI-->>Client: ProductListView
```

## 🎯 Patrones de Implementación

### Command Pattern Integration
```mermaid
classDiagram
    class Command {
        <<interface>>
        +execute(): void
    }
    
    class CommandHandler~T~ {
        <<interface>>
        +handle(command: T): Result
    }
    
    class CommandBus {
        -handlers: Map~Class, CommandHandler~
        +dispatch(command): Result
        +register(commandClass, handler): void
    }
    
    class CreateProductCommand {
        +execute(): void
    }
    
    Command <|.. CreateProductCommand
    CommandBus --> CommandHandler
    CommandHandler <|.. CreateProductCommandHandler
```

### Query Pattern Integration
```mermaid
classDiagram
    class Query {
        <<interface>>
    }
    
    class QueryHandler~T, R~ {
        <<interface>>
        +handle(query: T): R
    }
    
    class QueryBus {
        -handlers: Map~Class, QueryHandler~
        +dispatch(query): Result
        +register(queryClass, handler): void
    }
    
    class GetProductsQuery {
        +category: String
        +page: int
    }
    
    Query <|.. GetProductsQuery
    QueryBus --> QueryHandler
    QueryHandler <|.. GetProductsQueryHandler
```

## 📊 Read Model Optimization Strategies

### Denormalized Views
```mermaid
graph TB
    subgraph "Normalized Write Model"
        P[Product]
        C[Category]
        R[Review]
        I[Inventory]
    end
    
    subgraph "Denormalized Read Models"
        PV[ProductView]
        PLV[ProductListView]
        PSV[ProductSearchView]
        PCV[ProductCatalogView]
    end
    
    P --> PV
    C --> PV
    R --> PV
    I --> PV
    
    PV --> PLV
    PV --> PSV
    PV --> PCV
```

### Materialized Views
```mermaid
classDiagram
    class ProductSummaryView {
        +productId: String
        +name: String
        +averageRating: double
        +totalReviews: int
        +totalSales: int
        +lastUpdated: LocalDateTime
    }
    
    class ProductCategoryView {
        +categoryId: String
        +categoryName: String
        +productCount: int
        +averagePrice: BigDecimal
        +topProducts: List~String~
    }
    
    class ProductSearchView {
        +productId: String
        +searchableText: String
        +tags: List~String~
        +boost: double
        +isActive: boolean
    }
```

## 🔧 Event Sourcing Integration

### Event Store Pattern
```mermaid
classDiagram
    class EventStore {
        +saveEvents(streamId, events): void
        +getEvents(streamId): List~Event~
        +getEventsAfter(streamId, version): List~Event~
    }
    
    class StoredEvent {
        +eventId: String
        +streamId: String
        +eventType: String
        +eventData: String
        +eventVersion: int
        +timestamp: LocalDateTime
    }
    
    class EventStream {
        +streamId: String
        +version: int
        +events: List~Event~
        +appendEvent(event): void
    }
    
    EventStore --> StoredEvent
    EventStore --> EventStream
```

### Projection Rebuilding
```mermaid
graph TB
    A[Event Store] --> B[Projection Rebuilder]
    B --> C[Clear Read Models]
    C --> D[Replay All Events]
    D --> E[Rebuild Projections]
    E --> F[Update Read Models]
    F --> G[Validate Consistency]
```

## 🎨 Advanced CQRS Patterns

### Saga Pattern Integration
```mermaid
classDiagram
    class OrderSaga {
        +handle(OrderCreatedEvent): void
        +handle(PaymentProcessedEvent): void
        +handle(InventoryReservedEvent): void
        +handle(ShippingScheduledEvent): void
    }
    
    class SagaManager {
        +startSaga(sagaType, event): void
        +continueSaga(sagaId, event): void
        +completeSaga(sagaId): void
    }
    
    OrderSaga --> SagaManager
```

### Snapshot Pattern
```mermaid
classDiagram
    class AggregateSnapshot {
        +aggregateId: String
        +version: int
        +data: String
        +timestamp: LocalDateTime
    }
    
    class SnapshotStore {
        +saveSnapshot(snapshot): void
        +getSnapshot(aggregateId): Optional~AggregateSnapshot~
        +getLatestSnapshot(aggregateId): Optional~AggregateSnapshot~
    }
    
    SnapshotStore --> AggregateSnapshot
```