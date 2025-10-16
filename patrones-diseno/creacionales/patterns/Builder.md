---
layout: page
title: "Builder"
---

## Problema
Construir objetos complejos paso a paso, evitando constructores con muchos parámetros y permitiendo diferentes representaciones del mismo objeto.

## Propósito
Separar la construcción de un objeto complejo de su representación, permitiendo que el mismo proceso de construcción pueda crear diferentes representaciones. Facilita la creación de objetos inmutables complejos.

## Casos de uso comunes
- Configuraciones complejas (base de datos, APIs, servidores)
- Objetos con muchos parámetros opcionales
- Consultas SQL dinámicas
- Objetos inmutables complejos
- Builders de UI (formularios, ventanas)
- Configuraciones de testing

## ¿Quién es quién en Builder?

| Actor | Lo que realmente es | Ejemplo | Analogía |
|-------|--------------------|---------|-----------|
| **Builder** | Acumula parámetros y valida antes de crear | `DatabaseConfigBuilder` - configura paso a paso | Arquitecto (diseña casa paso a paso) |
| **Product** | Objeto final complejo e inmutable | `DatabaseConfig` - configuración completa | Casa terminada (compleja, muchas opciones) |
| **Director** | Coordinador opcional que conoce pasos | Cliente que dice "configuración de producción" | Cliente ("casa familiar" vs "casa de lujo") |
| **Fluent Interface** | API encadenada para facilidad de uso | `.host().port().database().build()` | Conversación natural ("3 habitaciones, 2 baños") |

## Diagrama

```mermaid
classDiagram
    namespace BuilderPattern {
        class Client {
            +main()
        }
        
        class Builder {
            -partA: String
            -partB: String
            -partC: String
            +setPartA(value) Builder
            +setPartB(value) Builder
            +setPartC(value) Builder
            +build() Product
        }
        
        class Product {
            -partA: String
            -partB: String
            -partC: String
            +Product(builder)
        }
    }
    
    Client --> Builder
    Client --> Product
    Builder --> Product : builds
```

## Ejemplo práctico: Configuración de Base de Datos

```mermaid
classDiagram
    namespace DatabaseExample {
        class Client {
            +main()
        }
        
        class DatabaseConfigBuilder {
            -host: String
            -port: Integer
            -database: String
            -username: String
            -password: String
            -maxConnections: Integer
            -timeout: Integer
            -ssl: Boolean
            -retryAttempts: Integer
            -poolSize: Integer
            +host(host) DatabaseConfigBuilder
            +port(port) DatabaseConfigBuilder
            +database(name) DatabaseConfigBuilder
            +credentials(user, pass) DatabaseConfigBuilder
            +maxConnections(max) DatabaseConfigBuilder
            +timeout(seconds) DatabaseConfigBuilder
            +enableSSL() DatabaseConfigBuilder
            +retryAttempts(attempts) DatabaseConfigBuilder
            +poolSize(size) DatabaseConfigBuilder
            +build() DatabaseConfig
        }
        
        class DatabaseConfig {
            -host: String
            -port: Integer
            -database: String
            -username: String
            -password: String
            -maxConnections: Integer
            -timeout: Integer
            -ssl: Boolean
            -retryAttempts: Integer
            -poolSize: Integer
            +connect() Connection
            +isValid() Boolean
        }
    }
    
    Client --> DatabaseConfigBuilder
    Client --> DatabaseConfig
    DatabaseConfigBuilder --> DatabaseConfig : builds
```

## Flujo de construcción

```mermaid
sequenceDiagram
    participant Client
    participant DatabaseConfigBuilder
    participant DatabaseConfig
    
    Note over Client: 1. Cliente configura solo lo que necesita
    Client->>DatabaseConfigBuilder: new DatabaseConfigBuilder()
    Client->>DatabaseConfigBuilder: host("localhost")
    DatabaseConfigBuilder-->>Client: this
    Client->>DatabaseConfigBuilder: port(5432)
    DatabaseConfigBuilder-->>Client: this
    Client->>DatabaseConfigBuilder: database("myapp")
    DatabaseConfigBuilder-->>Client: this
    Client->>DatabaseConfigBuilder: credentials("user", "pass")
    DatabaseConfigBuilder-->>Client: this
    Client->>DatabaseConfigBuilder: maxConnections(20)
    DatabaseConfigBuilder-->>Client: this
    Client->>DatabaseConfigBuilder: enableSSL()
    DatabaseConfigBuilder-->>Client: this
    
    Note over Client: 2. Builder aplica defaults y valida
    Client->>DatabaseConfigBuilder: build()
    DatabaseConfigBuilder->>DatabaseConfigBuilder: applyDefaults()
    DatabaseConfigBuilder->>DatabaseConfigBuilder: validate()
    DatabaseConfigBuilder->>DatabaseConfig: new DatabaseConfig(this)
    DatabaseConfig-->>DatabaseConfigBuilder: config instance
    DatabaseConfigBuilder-->>Client: DatabaseConfig
    
    Note over Client: 3. Cliente usa la configuración
    Client->>DatabaseConfig: connect()
```

## Ventajas
- **Legibilidad**: Código más claro que constructores con muchos parámetros
- **Flexibilidad**: Permite diferentes representaciones del mismo objeto
- **Inmutabilidad**: Facilita la creación de objetos inmutables
- **Validación**: Centraliza validaciones antes de crear el objeto
- **Fluent Interface**: API más expresiva y fácil de usar

## Desventajas
- **Complejidad**: Introduce más código y clases
- **Memoria**: Crea objetos intermedios durante la construcción
- **Duplicación**: Puede duplicar campos entre builder y producto
- **Overhead**: Más costoso que constructores simples

## Cuándo usar
- Objetos con muchos parámetros (más de 4-5)
- Muchos parámetros opcionales
- Necesitas objetos inmutables complejos
- Quieres validar antes de crear el objeto
- Diferentes representaciones del mismo objeto

## Cuándo NO usar
- Objetos simples con pocos parámetros
- Todos los parámetros son obligatorios
- No necesitas inmutabilidad
- La complejidad adicional no se justifica

## Ejemplo de uso

```javascript
// Construcción fluent - solo configuras lo que necesitas
const dbConfig = new DatabaseConfigBuilder()
    .host("localhost")
    .port(5432)
    .database("myapp")
    .credentials("admin", "secret123")
    .maxConnections(20)
    .timeout(30)
    .enableSSL()
    .retryAttempts(3)
    .build();

const connection = dbConfig.connect();

// vs Constructor tradicional (pesadilla de parámetros)
const dbConfig2 = new DatabaseConfig(
    "localhost",    // host
    5432,           // port  
    "myapp",        // database
    "admin",        // username
    "secret123",    // password
    20,             // maxConnections
    30,             // timeout
    true,           // ssl
    3,              // retryAttempts
    10,             // poolSize
    null,           // ¿Qué es esto?
    false,          // ¿y esto?
    null            // ¿y esto otro?
);
```

## Diferencias con otros patrones

| Patrón | Propósito | Cuándo usar |
|--------|-----------|-------------|
| **Builder** | Construir objetos complejos paso a paso | Muchos parámetros opcionales |
| **Factory Method** | Crear objetos sin especificar clase exacta | Múltiples implementaciones |
| **Abstract Factory** | Crear familias de objetos relacionados | Productos que deben ser compatibles |
| **Prototype** | Crear objetos clonando existentes | Creación costosa, muchas variaciones |
