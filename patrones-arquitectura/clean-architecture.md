---
layout: page
title: "Clean Architecture - Frontend (Angular)"
---

## ❓ Qué problema resuelve
- **Acoplamiento con frameworks**: Lógica de negocio mezclada con UI
- **Difícil testing**: Componentes complejos difíciles de probar
- **Mantenibilidad**: Cambios en UI afectan lógica de negocio
- **Reutilización**: Lógica no reutilizable entre diferentes interfaces

## 🔧 Cómo funciona
Separa la aplicación en capas concéntricas donde las dependencias apuntan hacia adentro, manteniendo la lógica de negocio independiente de frameworks y UI.

## 📊 Diagrama de Arquitectura

```mermaid
graph TB
    subgraph "Clean Architecture - Frontend"
        subgraph "External Layer"
            UI[Components/Views]
            API[HTTP Services]
            STORAGE[Local Storage]
        end
        
        subgraph "Interface Adapters"
            PRESENTER[Presenters]
            CONTROLLER[Controllers]
            GATEWAY[API Gateways]
        end
        
        subgraph "Use Cases Layer"
            UC1[Login Use Case]
            UC2[Get Users Use Case]
            UC3[Create Order Use Case]
        end
        
        subgraph "Entities Layer"
            USER[User Entity]
            ORDER[Order Entity]
            PRODUCT[Product Entity]
        end
    end
    
    UI --> PRESENTER
    PRESENTER --> UC1
    PRESENTER --> UC2
    PRESENTER --> UC3
    
    UC1 --> USER
    UC2 --> USER
    UC3 --> ORDER
    UC3 --> PRODUCT
    
    UC1 --> GATEWAY
    UC2 --> GATEWAY
    UC3 --> GATEWAY
    
    GATEWAY --> API
    GATEWAY --> STORAGE
```

## 🏗️ Estructura de Carpetas Angular

```
src/app/
├── core/                           # Entities & Use Cases
│   ├── entities/
│   │   ├── user.entity.ts
│   │   ├── order.entity.ts
│   │   └── product.entity.ts
│   ├── use-cases/
│   │   ├── auth/
│   │   │   ├── login.use-case.ts
│   │   │   └── logout.use-case.ts
│   │   ├── user/
│   │   │   ├── get-users.use-case.ts
│   │   │   └── create-user.use-case.ts
│   │   └── order/
│   │       ├── create-order.use-case.ts
│   │       └── get-orders.use-case.ts
│   └── repositories/               # Abstract repositories
│       ├── user.repository.ts
│       ├── order.repository.ts
│       └── auth.repository.ts
├── data/                          # Interface Adapters - Data
│   ├── repositories/              # Repository implementations
│   │   ├── user-api.repository.ts
│   │   ├── order-api.repository.ts
│   │   └── auth-local.repository.ts
│   ├── services/                  # HTTP Services
│   │   ├── user-api.service.ts
│   │   ├── order-api.service.ts
│   │   └── auth-api.service.ts
│   └── models/                    # DTOs/API Models
│       ├── user-api.model.ts
│       ├── order-api.model.ts
│       └── auth-api.model.ts
├── presentation/                  # Interface Adapters - UI
│   ├── pages/
│   │   ├── login/
│   │   │   ├── login.component.ts
│   │   │   ├── login.presenter.ts
│   │   │   └── login.component.html
│   │   ├── users/
│   │   │   ├── user-list.component.ts
│   │   │   ├── user-list.presenter.ts
│   │   │   ├── user-create.component.ts
│   │   │   └── user-create.presenter.ts
│   │   └── orders/
│   │       ├── order-list.component.ts
│   │       ├── order-list.presenter.ts
│   │       └── order-create.component.ts
│   ├── shared/
│   │   ├── components/
│   │   ├── pipes/
│   │   └── directives/
│   └── view-models/               # View Models
│       ├── user.view-model.ts
│       ├── order.view-model.ts
│       └── auth.view-model.ts
└── shared/                        # Cross-cutting concerns
    ├── utils/
    ├── constants/
    └── types/
```

## 📋 Implementación por Capas

### 🔵 Entities Layer
```mermaid
classDiagram
    class User {
        +id: string
        +email: string
        +name: string
        +isActive: boolean
        +validate(): ValidationResult
        +isValidEmail(): boolean
    }
    
    class Order {
        +id: string
        +userId: string
        +items: OrderItem[]
        +total: number
        +status: OrderStatus
        +calculateTotal(): number
        +canBeCancelled(): boolean
    }
    
    class Product {
        +id: string
        +name: string
        +price: number
        +stock: number
        +isAvailable(): boolean
    }
```

### 🟢 Use Cases Layer
```mermaid
classDiagram
    class LoginUseCase {
        -authRepository: AuthRepository
        +execute(email, password): Promise~AuthResult~
    }
    
    class GetUsersUseCase {
        -userRepository: UserRepository
        +execute(filters): Promise~User[]~
    }
    
    class CreateOrderUseCase {
        -orderRepository: OrderRepository
        -userRepository: UserRepository
        +execute(orderData): Promise~Order~
    }
    
    class AuthRepository {
        <<interface>>
        +login(credentials): Promise~AuthResult~
        +logout(): Promise~void~
        +getCurrentUser(): Promise~User~
    }
    
    class UserRepository {
        <<interface>>
        +getAll(filters): Promise~User[]~
        +getById(id): Promise~User~
        +create(user): Promise~User~
    }
    
    LoginUseCase --> AuthRepository
    GetUsersUseCase --> UserRepository
    CreateOrderUseCase --> UserRepository
```

### 🟡 Interface Adapters Layer
```mermaid
classDiagram
    class UserListPresenter {
        -getUsersUseCase: GetUsersUseCase
        +users$: Observable~UserViewModel[]~
        +loading$: Observable~boolean~
        +loadUsers(filters): void
        +refreshUsers(): void
    }
    
    class UserApiRepository {
        -userApiService: UserApiService
        +getAll(filters): Promise~User[]~
        +getById(id): Promise~User~
        +create(user): Promise~User~
    }
    
    class UserViewModel {
        +id: string
        +displayName: string
        +email: string
        +statusLabel: string
        +canEdit: boolean
    }
    
    UserListPresenter --> GetUsersUseCase
    UserApiRepository --> UserApiService
```

### 🔴 External Layer (Components)
```mermaid
classDiagram
    class UserListComponent {
        -presenter: UserListPresenter
        +users$: Observable~UserViewModel[]~
        +loading$: Observable~boolean~
        +ngOnInit(): void
        +onRefresh(): void
        +onFilterChange(filters): void
    }
    
    class UserApiService {
        -http: HttpClient
        +getUsers(filters): Observable~UserApiModel[]~
        +getUserById(id): Observable~UserApiModel~
        +createUser(user): Observable~UserApiModel~
    }
    
    UserListComponent --> UserListPresenter
    UserApiService --> HttpClient
```

## 🔄 Flujo de Datos

```mermaid
sequenceDiagram
    participant C as Component
    participant P as Presenter
    participant UC as Use Case
    participant R as Repository
    participant API as API Service
    
    C->>P: loadUsers()
    P->>UC: execute(filters)
    UC->>R: getAll(filters)
    R->>API: getUsers(filters)
    API-->>R: UserApiModel[]
    R-->>UC: User[]
    UC-->>P: User[]
    P-->>C: UserViewModel[]
    C->>C: updateView()
```

## 🎯 Beneficios de Clean Architecture

### ✅ Ventajas
- **Testabilidad**: Cada capa se puede probar independientemente
- **Independencia de Framework**: Lógica no depende de Angular
- **Flexibilidad**: Fácil cambiar implementaciones
- **Mantenibilidad**: Separación clara de responsabilidades
- **Reutilización**: Use cases reutilizables

### 📊 Comparación con Arquitectura Tradicional

```mermaid
graph TB
    subgraph "❌ Arquitectura Tradicional"
        A[Component] --> B[Service]
        B --> C[HTTP Client]
        A --> D[Business Logic]
        A --> E[Validation]
        A --> F[State Management]
    end
    
    subgraph "✅ Clean Architecture"
        G[Component] --> H[Presenter]
        H --> I[Use Case]
        I --> J[Repository Interface]
        J --> K[Repository Implementation]
        K --> L[API Service]
    end
```

## 🧪 Testing Strategy

### Unit Tests por Capa
```mermaid
graph LR
    subgraph "Testing Layers"
        A[Entity Tests] --> B[Use Case Tests]
        B --> C[Presenter Tests]
        C --> D[Component Tests]
        
        E[Repository Tests] --> F[API Service Tests]
    end
    
    subgraph "Test Types"
        G[Unit Tests]
        H[Integration Tests]
        I[E2E Tests]
    end
```

## 🔧 Implementación Práctica

### Dependency Injection Setup
```typescript
// app.module.ts
providers: [
  // Use Cases
  { provide: LoginUseCase, useClass: LoginUseCase },
  { provide: GetUsersUseCase, useClass: GetUsersUseCase },
  
  // Repositories
  { provide: 'AuthRepository', useClass: AuthApiRepository },
  { provide: 'UserRepository', useClass: UserApiRepository },
  
  // Presenters
  UserListPresenter,
  LoginPresenter
]
```

### Feature Module Structure
```typescript
// user.module.ts
@NgModule({
  declarations: [
    UserListComponent,
    UserCreateComponent
  ],
  providers: [
    GetUsersUseCase,
    CreateUserUseCase,
    UserListPresenter,
    UserCreatePresenter,
    { provide: 'UserRepository', useClass: UserApiRepository }
  ]
})
export class UserModule { }
```

## 🎨 Patrones Complementarios

### MVVM (Model-View-ViewModel)
```mermaid
classDiagram
    class View {
        +template: HTML
        +bindings: Data Binding
    }
    
    class ViewModel {
        +properties: Observable
        +commands: Methods
        +state: ViewState
    }
    
    class Model {
        +entities: Domain Objects
        +useCases: Business Logic
    }
    
    View --> ViewModel
    ViewModel --> Model
```

### Repository Pattern
```mermaid
classDiagram
    class Repository {
        <<interface>>
        +getAll(): Promise~T[]~
        +getById(id): Promise~T~
        +create(entity): Promise~T~
        +update(entity): Promise~T~
        +delete(id): Promise~void~
    }
    
    class ApiRepository {
        +getAll(): Promise~T[]~
        +getById(id): Promise~T~
        +create(entity): Promise~T~
    }
    
    class LocalRepository {
        +getAll(): Promise~T[]~
        +getById(id): Promise~T~
        +create(entity): Promise~T~
    }
    
    Repository <|.. ApiRepository
    Repository <|.. LocalRepository
```

## 📱 Adaptación para Otros Frameworks

### React
```
src/
├── core/              # Same as Angular
├── data/              # Same as Angular
├── presentation/
│   ├── hooks/         # Custom hooks (Presenters)
│   ├── components/    # React Components
│   └── pages/         # Page Components
└── shared/
```

### Vue.js
```
src/
├── core/              # Same as Angular
├── data/              # Same as Angular
├── presentation/
│   ├── composables/   # Vue Composables (Presenters)
│   ├── components/    # Vue Components
│   └── views/         # Page Views
└── shared/
```