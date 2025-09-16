---
layout: page
title: "MVC, MVP, MVVM - Patrones de Presentación"
---

## ❓ Qué problema resuelven
- **Lógica mezclada**: UI, lógica de negocio y datos en el mismo lugar
- **Difícil testing**: Componentes acoplados difíciles de probar
- **Mantenibilidad**: Cambios en UI afectan lógica de negocio
- **Reutilización**: Lógica no reutilizable entre diferentes vistas

## 🔧 Cómo funcionan
Separan las responsabilidades de presentación, lógica y datos en componentes distintos con roles específicos.

## 📊 Comparación de Patrones

```mermaid
graph TB
    subgraph "MVC - Model View Controller"
        M1[Model] 
        V1[View]
        C1[Controller]
        
        V1 --> C1
        C1 --> M1
        M1 --> V1
    end
    
    subgraph "MVP - Model View Presenter"
        M2[Model]
        V2[View]
        P2[Presenter]
        
        V2 <--> P2
        P2 <--> M2
    end
    
    subgraph "MVVM - Model View ViewModel"
        M3[Model]
        V3[View]
        VM3[ViewModel]
        
        V3 <--> VM3
        VM3 <--> M3
    end
```

## 🎯 MVC - Model View Controller

### 📊 Diagrama de Arquitectura

```mermaid
classDiagram
    class Model {
        +data: any
        +getData(): any
        +setData(data): void
        +notifyObservers(): void
    }
    
    class View {
        +render(data): void
        +handleUserInput(): void
        +update(): void
    }
    
    class Controller {
        +handleRequest(request): void
        +updateModel(data): void
        +selectView(): View
    }
    
    View --> Controller : user input
    Controller --> Model : updates
    Model --> View : notifications
```

### 🏗️ Estructura Angular (MVC-like)

```
src/app/user-management/
├── models/
│   ├── user.model.ts              # Model
│   └── user-response.model.ts
├── controllers/
│   ├── user.controller.ts         # Controller
│   └── user-list.controller.ts
├── views/
│   ├── user-list.component.html   # View
│   ├── user-list.component.ts
│   └── user-form.component.html
└── services/
    ├── user.service.ts            # Model Services
    └── user-api.service.ts
```

### 🎨 Características MVC
- **Flujo unidireccional**: User → Controller → Model → View
- **Controller central**: Maneja toda la lógica de coordinación
- **Vista pasiva**: Solo renderiza, no contiene lógica
- **Modelo independiente**: No conoce vistas ni controladores

---

## 🎯 MVP - Model View Presenter

### 📊 Diagrama de Arquitectura

```mermaid
classDiagram
    class Model {
        +data: any
        +businessLogic(): any
        +dataAccess(): any
    }
    
    class View {
        <<interface>>
        +showData(data): void
        +showError(error): void
        +showLoading(): void
    }
    
    class Presenter {
        -view: View
        -model: Model
        +onViewReady(): void
        +onUserAction(action): void
        +loadData(): void
    }
    
    class ConcreteView {
        +showData(data): void
        +showError(error): void
        +showLoading(): void
    }
    
    Presenter --> Model
    Presenter --> View
    View <|.. ConcreteView
```

### 🏗️ Estructura Angular (MVP)

```
src/app/user-management/
├── models/
│   ├── user.model.ts
│   └── user.repository.ts         # Model
├── presenters/
│   ├── user-list.presenter.ts     # Presenter
│   └── user-form.presenter.ts
├── views/
│   ├── contracts/
│   │   ├── user-list.view.ts      # View Interface
│   │   └── user-form.view.ts
│   ├── user-list.component.ts     # Concrete View
│   ├── user-list.component.html
│   └── user-form.component.ts
└── services/
    └── user.service.ts
```

### 🎨 Características MVP
- **Vista pasiva**: No contiene lógica, solo interfaz
- **Presenter activo**: Maneja toda la lógica de presentación
- **Testeable**: Presenter se puede probar sin UI
- **Acoplamiento débil**: Vista e interfaz separadas

---

## 🎯 MVVM - Model View ViewModel

### 📊 Diagrama de Arquitectura

```mermaid
classDiagram
    class Model {
        +data: any
        +businessRules(): any
        +dataValidation(): any
    }
    
    class View {
        +template: HTML
        +dataBinding: Binding
        +eventHandlers: Events
    }
    
    class ViewModel {
        +properties: Observable
        +commands: Command[]
        +state: ViewState
        +bindToView(): void
    }
    
    class DataBinding {
        +twoWayBinding(): void
        +oneWayBinding(): void
        +eventBinding(): void
    }
    
    View --> ViewModel : data binding
    ViewModel --> Model : data access
    View --> DataBinding
    ViewModel --> DataBinding
```

### 🏗️ Estructura Angular (MVVM)

```
src/app/user-management/
├── models/
│   ├── user.model.ts              # Model
│   ├── user.service.ts
│   └── user.repository.ts
├── view-models/
│   ├── user-list.view-model.ts    # ViewModel
│   ├── user-form.view-model.ts
│   └── base.view-model.ts
├── views/
│   ├── user-list.component.ts     # View
│   ├── user-list.component.html
│   ├── user-form.component.ts
│   └── user-form.component.html
└── shared/
    ├── commands/
    │   └── relay-command.ts
    └── observables/
        └── observable-property.ts
```

### 🎨 Características MVVM
- **Data Binding**: Sincronización automática View ↔ ViewModel
- **Commands**: Acciones encapsuladas en objetos
- **Observables**: Propiedades que notifican cambios
- **Declarativo**: Vista describe qué mostrar, no cómo

---

## 🔄 Comparación Detallada

### Flujo de Datos

```mermaid
sequenceDiagram
    participant U as User
    participant V as View
    participant P as Presenter/Controller/ViewModel
    participant M as Model
    
    Note over U,M: MVC Flow
    U->>V: User Action
    V->>P: Forward Action
    P->>M: Update Data
    M->>V: Notify Change
    V->>U: Update UI
    
    Note over U,M: MVP Flow
    U->>V: User Action
    V->>P: Delegate Action
    P->>M: Business Logic
    M->>P: Return Data
    P->>V: Update View
    
    Note over U,M: MVVM Flow
    U->>V: User Action
    V->>P: Data Binding
    P->>M: Business Logic
    M->>P: Return Data
    P->>V: Auto Update (Binding)
```

### Tabla Comparativa

| Aspecto | MVC | MVP | MVVM |
|---------|-----|-----|------|
| **Vista** | Pasiva/Activa | Pasiva | Activa (Data Binding) |
| **Lógica de Presentación** | Controller | Presenter | ViewModel |
| **Testabilidad** | Media | Alta | Alta |
| **Data Binding** | Manual | Manual | Automático |
| **Acoplamiento** | Medio | Bajo | Bajo |
| **Complejidad** | Baja | Media | Alta |

---

## 🎨 Implementación en Frameworks

### Angular (MVVM-like)

```mermaid
classDiagram
    class UserComponent {
        +users$: Observable~User[]~
        +loading$: Observable~boolean~
        +onCreateUser(): void
        +onDeleteUser(id): void
    }
    
    class UserViewModel {
        +users$: BehaviorSubject~User[]~
        +loading$: BehaviorSubject~boolean~
        +createUser(user): void
        +deleteUser(id): void
    }
    
    class UserService {
        +getUsers(): Observable~User[]~
        +createUser(user): Observable~User~
        +deleteUser(id): Observable~void~
    }
    
    UserComponent --> UserViewModel
    UserViewModel --> UserService
```

### React (MVP-like con Hooks)

```mermaid
classDiagram
    class UserListComponent {
        +render(): JSX.Element
        +useUserPresenter(): UserPresenter
    }
    
    class UserPresenter {
        +users: User[]
        +loading: boolean
        +loadUsers(): void
        +createUser(user): void
    }
    
    class UserService {
        +getUsers(): Promise~User[]~
        +createUser(user): Promise~User~
    }
    
    UserListComponent --> UserPresenter
    UserPresenter --> UserService
```

### Vue.js (MVVM-like)

```mermaid
classDiagram
    class UserComponent {
        +data(): ComponentData
        +computed: ComputedProperties
        +methods: ComponentMethods
    }
    
    class UserStore {
        +state: UserState
        +getters: UserGetters
        +mutations: UserMutations
        +actions: UserActions
    }
    
    class UserService {
        +fetchUsers(): Promise~User[]~
        +saveUser(user): Promise~User~
    }
    
    UserComponent --> UserStore
    UserStore --> UserService
```

---

## 🧪 Testing Strategies

### MVC Testing
```mermaid
graph TB
    A[Controller Tests] --> B[Mock Model]
    A --> C[Mock View]
    D[Model Tests] --> E[Unit Tests]
    F[View Tests] --> G[UI Tests]
```

### MVP Testing
```mermaid
graph TB
    A[Presenter Tests] --> B[Mock View Interface]
    A --> C[Mock Model]
    D[View Tests] --> E[Mock Presenter]
    F[Model Tests] --> G[Unit Tests]
```

### MVVM Testing
```mermaid
graph TB
    A[ViewModel Tests] --> B[Mock Model]
    C[View Tests] --> D[Mock ViewModel]
    E[Model Tests] --> F[Unit Tests]
    G[Binding Tests] --> H[Integration Tests]
```

---

## 🎯 Cuándo Usar Cada Patrón

### MVC
- ✅ **Aplicaciones web tradicionales**
- ✅ **Flujo de navegación simple**
- ✅ **Equipos pequeños**
- ❌ **Interfaces complejas**
- ❌ **Mucha interactividad**

### MVP
- ✅ **Testing intensivo**
- ✅ **Lógica de presentación compleja**
- ✅ **Múltiples vistas para mismo modelo**
- ❌ **Data binding automático requerido**
- ❌ **Desarrollo rápido**

### MVVM
- ✅ **Data binding automático**
- ✅ **Interfaces reactivas**
- ✅ **Aplicaciones complejas**
- ✅ **Frameworks que lo soportan**
- ❌ **Aplicaciones simples**
- ❌ **Performance crítica**

---

## 🔧 Mejores Prácticas

### General
- **Separación clara** de responsabilidades
- **Interfaces bien definidas** entre capas
- **Testing** de cada capa por separado
- **Inyección de dependencias** para desacoplamiento

### Específicas por Patrón
- **MVC**: Controller delgado, Model rico
- **MVP**: Vista tonta, Presenter inteligente
- **MVVM**: ViewModel sin referencias a Vista