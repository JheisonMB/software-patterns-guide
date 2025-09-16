---
layout: page
title: "Singleton"
---

## Problema
Garantizar que una clase tenga solo una instancia y proporcionar un punto de acceso global a ella.

## Propósito
Controlar la instanciación de una clase para asegurar que solo exista un objeto de esa clase en todo el sistema, proporcionando acceso global a esa instancia única.

## Casos de uso comunes
- Loggers del sistema
- Configuraciones de aplicación
- Conexiones a base de datos
- Caches globales
- Pools de recursos
- Managers de estado global

## Diagrama

```mermaid
classDiagram
    class Singleton {
            -static instance: Singleton
            -Singleton()
            +static getInstance() Singleton
            +doSomething()
        }
        
        class Client1 {
            +useService()
        }
        
        class Client2 {
            +useService()
    }
    
    Client1 --> Singleton : getInstance()
    Client2 --> Singleton : getInstance()
    
    note for Singleton "Solo una instancia <br> existe en el sistema"
```

## Flujo de creación

```mermaid
sequenceDiagram
    participant Client1
    participant Client2
    participant Singleton
    
    Client1->>Singleton: getInstance()
    alt Primera llamada
        Singleton->>Singleton: crear instancia
        Singleton-->>Client1: retorna nueva instancia
    end
    
    Client2->>Singleton: getInstance()
    alt Llamadas posteriores
        Singleton-->>Client2: retorna instancia existente
    end
    
    Note over Client1,Client2: Ambos clientes usan la misma instancia
```

## Ventajas
- **Instancia única**: Garantiza una sola instancia en el sistema
- **Acceso global**: Punto de acceso controlado desde cualquier parte
- **Lazy loading**: Puede crear la instancia solo cuando se necesita
- **Control de recursos**: Útil para recursos costosos o limitados

## Desventajas
- **Testing**: Dificulta las pruebas unitarias (hard to mock)
- **Acoplamiento**: Crea dependencias globales ocultas
- **Concurrencia**: Requiere consideraciones especiales para thread-safety
- **Principio de responsabilidad única**: Viola SRP (controla instanciación + lógica de negocio)

## Cuándo usar
- Necesitas exactamente una instancia de una clase
- Requieres acceso global a esa instancia
- La instancia debe ser creada de forma lazy
- Controlas recursos compartidos (conexiones, caches)

## Cuándo NO usar
- Puedes usar Dependency Injection
- Necesitas múltiples instancias en el futuro
- Dificulta el testing
- Crea acoplamiento innecesario
- El framework ya maneja singletons (Spring Boot)

## Alternativas modernas
- **Dependency Injection**: Spring Boot maneja singletons automáticamente
- **Static classes**: Para funcionalidades sin estado
- **Factory patterns**: Para control más flexible de instancias