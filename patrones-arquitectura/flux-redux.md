---
layout: page
title: "Flux/Redux Pattern - Gestión de Estado"
---

## ❓ Qué problema resuelve
- **Estado compartido**: Múltiples componentes necesitan acceder al mismo estado
- **Flujo de datos complejo**: Datos fluyendo en múltiples direcciones
- **Debugging difícil**: Difícil rastrear cambios de estado
- **Efectos secundarios**: Mutaciones impredecibles del estado

## 🔧 Cómo funciona
Implementa un flujo unidireccional de datos donde las acciones desencadenan cambios de estado a través de reducers, y los componentes se suscriben a estos cambios.

## 📊 Diagrama de Arquitectura Flux

```mermaid
graph LR
    A[Action] --> B[Dispatcher]
    B --> C[Store]
    C --> D[View]
    D --> A
    
    subgraph "Flux Flow"
        E[User Interaction] --> F[Action Creator]
        F --> G[Action]
        G --> H[Dispatcher]
        H --> I[Store]
        I --> J[View Update]
    end
```

## 📊 Diagrama de Arquitectura Redux

```mermaid
graph TB
    subgraph "Redux Architecture"
        A[UI Component] --> B[Action Creator]
        B --> C[Action]
        C --> D[Reducer]
        D --> E[Store]
        E --> F[State]
        F --> A
        
        G[Middleware] --> D
        C --> G
    end
    
    subgraph "Redux DevTools"
        H[Time Travel]
        I[State Inspector]
        J[Action Logger]
    end
    
    E --> H
    E --> I
    E --> J
```

## 🎯 Flux Pattern

### 📊 Diagrama de Clases

```mermaid
classDiagram
    class Action {
        +type: string
        +payload: any
        +meta?: any
    }
    
    class Dispatcher {
        +dispatch(action): void
        +register(callback): string
        +unregister(id): void
    }
    
    class Store {
        +getState(): any
        +emitChange(): void
        +addChangeListener(callback): void
        +removeChangeListener(callback): void
    }
    
    class View {
        +render(): JSX.Element
        +componentDidMount(): void
        +onChange(): void
    }
    
    Action --> Dispatcher
    Dispatcher --> Store
    Store --> View
    View --> Action
```

### 🏗️ Estructura de Carpetas Flux

```
src/
├── actions/
│   ├── UserActions.js
│   ├── ProductActions.js
│   └── ActionTypes.js
├── dispatcher/
│   └── AppDispatcher.js
├── stores/
│   ├── UserStore.js
│   ├── ProductStore.js
│   └── BaseStore.js
├── components/
│   ├── UserList.jsx
│   ├── ProductList.jsx
│   └── App.jsx
└── utils/
    └── WebAPIUtils.js
```

---

## 🎯 Redux Pattern

### 📊 Diagrama de Clases

```mermaid
classDiagram
    class Action {
        +type: string
        +payload?: any
        +error?: boolean
        +meta?: any
    }
    
    class ActionCreator {
        +createUser(user): Action
        +updateUser(id, user): Action
        +deleteUser(id): Action
    }
    
    class Reducer {
        +function(state, action): newState
    }
    
    class Store {
        +getState(): any
        +dispatch(action): void
        +subscribe(listener): function
    }
    
    class Selector {
        +getUsers(state): User[]
        +getUserById(state, id): User
        +getLoadingState(state): boolean
    }
    
    ActionCreator --> Action
    Action --> Reducer
    Reducer --> Store
    Store --> Selector
```

### 🏗️ Estructura de Carpetas Redux

```
src/
├── store/
│   ├── index.js                   # Store configuration
│   └── rootReducer.js
├── features/
│   ├── users/
│   │   ├── userSlice.js          # Redux Toolkit slice
│   │   ├── userActions.js        # Async actions
│   │   ├── userSelectors.js      # Selectors
│   │   ├── userSagas.js          # Side effects
│   │   └── components/
│   │       ├── UserList.jsx
│   │       └── UserForm.jsx
│   └── products/
│       ├── productSlice.js
│       ├── productActions.js
│       └── components/
├── shared/
│   ├── middleware/
│   │   ├── apiMiddleware.js
│   │   └── loggerMiddleware.js
│   └── utils/
│       └── apiClient.js
└── hooks/
    ├── useAppDispatch.js
    ├── useAppSelector.js
    └── useAsyncAction.js
```

---

## 🔄 Flujo de Datos Detallado

### Redux Flow
```mermaid
sequenceDiagram
    participant C as Component
    participant AC as Action Creator
    participant M as Middleware
    participant R as Reducer
    participant S as Store
    participant Sel as Selector
    
    C->>AC: User clicks button
    AC->>M: dispatch(action)
    M->>M: Process side effects
    M->>R: Forward action
    R->>R: Calculate new state
    R->>S: Return new state
    S->>S: Update state
    S->>Sel: State changed
    Sel->>C: Re-render with new data
```

### Async Actions Flow
```mermaid
sequenceDiagram
    participant C as Component
    participant T as Thunk/Saga
    participant API as API
    participant R as Reducer
    participant S as Store
    
    C->>T: dispatch(asyncAction)
    T->>R: dispatch(LOADING)
    T->>API: API call
    API-->>T: Response/Error
    alt Success
        T->>R: dispatch(SUCCESS, data)
    else Error
        T->>R: dispatch(ERROR, error)
    end
    R->>S: Update state
    S->>C: Re-render
```

---

## 🎨 Implementación por Framework

### React + Redux

```mermaid
classDiagram
    class UserComponent {
        +useSelector(selectUsers)
        +useDispatch()
        +handleCreateUser()
        +handleUpdateUser()
    }
    
    class userSlice {
        +name: 'users'
        +initialState: UserState
        +reducers: UserReducers
        +extraReducers: AsyncReducers
    }
    
    class userActions {
        +fetchUsers(): AsyncThunk
        +createUser(user): AsyncThunk
        +updateUser(id, user): AsyncThunk
    }
    
    class userSelectors {
        +selectAllUsers(state): User[]
        +selectUserById(state, id): User
        +selectUsersLoading(state): boolean
    }
    
    UserComponent --> userSlice
    UserComponent --> userActions
    UserComponent --> userSelectors
```

### Angular + NgRx

```mermaid
classDiagram
    class UserComponent {
        +users$: Observable~User[]~
        +loading$: Observable~boolean~
        +onCreateUser(user): void
    }
    
    class UserActions {
        +loadUsers(): Action
        +loadUsersSuccess(users): Action
        +loadUsersFailure(error): Action
    }
    
    class UserReducer {
        +function(state, action): UserState
    }
    
    class UserEffects {
        +loadUsers$: Observable~Action~
        +createUser$: Observable~Action~
    }
    
    class UserSelectors {
        +selectAllUsers: MemoizedSelector
        +selectUsersLoading: MemoizedSelector
    }
    
    UserComponent --> UserActions
    UserActions --> UserReducer
    UserActions --> UserEffects
    UserComponent --> UserSelectors
```

### Vue + Vuex

```mermaid
classDiagram
    class UserComponent {
        +computed: mapGetters
        +methods: mapActions
        +created(): void
    }
    
    class UserModule {
        +namespaced: true
        +state: UserState
        +getters: UserGetters
        +mutations: UserMutations
        +actions: UserActions
    }
    
    class UserService {
        +fetchUsers(): Promise~User[]~
        +createUser(user): Promise~User~
    }
    
    UserComponent --> UserModule
    UserModule --> UserService
```

---

## 🧪 Testing Strategies

### Redux Testing
```mermaid
graph TB
    A[Action Tests] --> B[Test Action Creators]
    C[Reducer Tests] --> D[Test State Changes]
    E[Selector Tests] --> F[Test Data Selection]
    G[Component Tests] --> H[Mock Store]
    I[Integration Tests] --> J[Real Store]
```

### Testing Pyramid
```mermaid
graph TB
    A[E2E Tests] --> B[User Workflows]
    C[Integration Tests] --> D[Connected Components]
    E[Unit Tests] --> F[Reducers]
    E --> G[Actions]
    E --> H[Selectors]
    E --> I[Components]
```

---

## 🔧 Middleware y Side Effects

### Middleware Chain
```mermaid
graph LR
    A[Action] --> B[Logger Middleware]
    B --> C[Thunk Middleware]
    C --> D[API Middleware]
    D --> E[Reducer]
    E --> F[Store]
```

### Side Effects Patterns
```mermaid
classDiagram
    class ReduxThunk {
        +function(dispatch, getState): any
    }
    
    class ReduxSaga {
        +function*(): Generator
        +takeEvery(pattern, saga): void
        +call(fn, ...args): Effect
        +put(action): Effect
    }
    
    class RTKQuery {
        +createApi(config): ApiSlice
        +fetchBaseQuery(config): BaseQuery
    }
    
    ReduxThunk --> Store
    ReduxSaga --> Store
    RTKQuery --> Store
```

---

## 🎯 Patrones Avanzados

### Normalized State
```mermaid
graph TB
    subgraph "Normalized State Structure"
        A[entities] --> B[users]
        A --> C[posts]
        A --> D[comments]
        
        B --> E[byId: {1: user1, 2: user2}]
        B --> F[allIds: [1, 2]]
        
        C --> G[byId: {1: post1, 2: post2}]
        C --> H[allIds: [1, 2]]
    end
```

### Feature-Based Architecture
```mermaid
graph TB
    subgraph "Feature Slices"
        A[authSlice] --> B[login, logout, register]
        C[usersSlice] --> D[CRUD operations]
        E[postsSlice] --> F[CRUD + comments]
    end
    
    subgraph "Shared"
        G[apiSlice] --> H[RTK Query endpoints]
        I[uiSlice] --> J[loading, errors, modals]
    end
```

---

## 🎨 Mejores Prácticas

### Estado Inmutable
```mermaid
graph TB
    A[Current State] --> B[Action]
    B --> C[Reducer]
    C --> D[New State]
    
    E[❌ Mutation] --> F[state.users.push(newUser)]
    G[✅ Immutable] --> H[...state, users: [...state.users, newUser]]
```

### Action Design
```mermaid
classDiagram
    class GoodAction {
        +type: "users/userAdded"
        +payload: User
        +meta: {timestamp: Date}
    }
    
    class BadAction {
        +type: "ADD_USER"
        +user: User
        +otherData: any
        +randomProperty: any
    }
    
    note for GoodAction "FSA compliant\nPredictable structure\nNamespaced type"
    note for BadAction "Non-standard structure\nUnpredictable properties\nGeneric type"
```

---

## 🔄 Migración y Evolución

### From Flux to Redux
```mermaid
graph LR
    A[Multiple Stores] --> B[Single Store]
    C[Event Emitters] --> D[Subscriptions]
    E[Imperative] --> F[Functional]
    G[Dispatcher] --> H[Middleware]
```

### Modern Redux (RTK)
```mermaid
graph TB
    A[Redux Toolkit] --> B[createSlice]
    A --> C[createAsyncThunk]
    A --> D[RTK Query]
    A --> E[Immer Integration]
    
    F[Benefits] --> G[Less Boilerplate]
    F --> H[Better DevEx]
    F --> I[Built-in Best Practices]
```

---

## 🎯 Cuándo Usar Redux/Flux

### ✅ Usar cuando:
- **Estado compartido** entre muchos componentes
- **Lógica de estado compleja**
- **Debugging** y **time-travel** necesarios
- **Aplicaciones grandes** con múltiples desarrolladores
- **Efectos secundarios** complejos

### ❌ No usar cuando:
- **Aplicaciones pequeñas** con estado local
- **Prototipado rápido**
- **Estado principalmente en servidor**
- **Componentes independientes**
- **Overhead** no justificado

---

## 📊 Comparación con Otras Soluciones

| Aspecto | Redux | Context API | Zustand | Recoil |
|---------|-------|-------------|---------|--------|
| **Curva de aprendizaje** | Alta | Baja | Baja | Media |
| **Boilerplate** | Alto | Bajo | Muy bajo | Bajo |
| **DevTools** | Excelente | Básico | Bueno | Bueno |
| **Performance** | Buena | Variable | Buena | Excelente |
| **Ecosistema** | Muy grande | Nativo | Pequeño | Creciendo |