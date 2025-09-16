---
layout: page
title: "Strategy"
---

## Problema
Cambiar algoritmos dinámicamente según el contexto, evitando condicionales complejas.

## Propósito
Encapsula diferentes algoritmos en clases separadas y permite intercambiarlos en tiempo de ejecución. El cliente elige qué algoritmo usar.

## Concepto clave
**Algoritmo intercambiable**: Como cambiar el método de pago en una tienda online - el proceso es el mismo pero el algoritmo de pago cambia (tarjeta, PayPal, transferencia).

## Casos de uso comunes
- Algoritmos de pricing (descuentos, promociones)
- Métodos de pago (tarjeta, PayPal, transferencia)
- Algoritmos de ordenamiento
- Estrategias de validación
- Algoritmos de compresión
- Estrategias de caching

## Diagrama

```mermaid
classDiagram
    namespace Strategy {
        class Context {
            -strategy: Strategy
            +setStrategy(strategy)
            +executeStrategy()
        }
        
        class Strategy {
            <<interface>>
            +execute(data) Result
        }
        
        class ConcreteStrategyA {
            +execute(data) Result
        }
        
        class ConcreteStrategyB {
            +execute(data) Result
        }
        
        class ConcreteStrategyC {
            +execute(data) Result
        }
    }
    
    Context --> Strategy
    Strategy <|.. ConcreteStrategyA
    Strategy <|.. ConcreteStrategyB
    Strategy <|.. ConcreteStrategyC
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace PricingExample {
        class PricingContext {
            -strategy: PricingStrategy
            +setStrategy(strategy)
            +calculatePrice(order) BigDecimal
        }
        
        class PricingStrategy {
            <<interface>>
            +calculatePrice(order) BigDecimal
        }
        
        class RegularPricingStrategy {
            +calculatePrice(order) BigDecimal
        }
        
        class VIPPricingStrategy {
            -discountPercentage: double
            +calculatePrice(order) BigDecimal
        }
        
        class SeasonalPricingStrategy {
            -seasonMultiplier: double
            +calculatePrice(order) BigDecimal
        }
        
        class BulkPricingStrategy {
            -bulkThreshold: int
            -bulkDiscount: double
            +calculatePrice(order) BigDecimal
        }
        
        class PricingFactory {
            +createStrategy(customerType, season, quantity) PricingStrategy
        }
    }
    
    PricingContext --> PricingStrategy
    PricingStrategy <|.. RegularPricingStrategy
    PricingStrategy <|.. VIPPricingStrategy
    PricingStrategy <|.. SeasonalPricingStrategy
    PricingStrategy <|.. BulkPricingStrategy
    PricingFactory --> PricingStrategy
```

## Selección de estrategia

```mermaid
flowchart TD
    A[Order Request] --> B{Customer Type?}
    B -->|VIP| C[VIP Strategy]
    B -->|Regular| D{Season?}
    B -->|Corporate| E{Quantity?}
    
    D -->|Holiday| F[Seasonal Strategy]
    D -->|Normal| G[Regular Strategy]
    
    E -->|> 100| H[Bulk Strategy]
    E -->|< 100| I[Corporate Strategy]
    
    C --> J[Calculate Price]
    F --> J
    G --> J
    H --> J
    I --> J
```

## Flujo de ejecución

```mermaid
sequenceDiagram
    participant Client
    participant Context
    participant Factory
    participant Strategy
    
    Client->>Factory: createStrategy(customerType, conditions)
    Factory->>Strategy: new ConcreteStrategy()
    Factory-->>Client: strategy
    
    Client->>Context: setStrategy(strategy)
    Client->>Context: executeStrategy(order)
    Context->>Strategy: execute(order)
    Strategy->>Strategy: apply algorithm
    Strategy-->>Context: result
    Context-->>Client: result
```



## Ventajas
- **Flexibilidad**: Algoritmos intercambiables en tiempo de ejecución
- **Extensibilidad**: Fácil agregar nuevas estrategias
- **Eliminación de condicionales**: No más if/switch complejos
- **Testabilidad**: Cada estrategia se puede probar independientemente

## Desventajas
- **Complejidad**: Introduce más clases
- **Conocimiento del cliente**: El cliente debe conocer las diferentes estrategias
- **Comunicación**: Todas las estrategias deben usar la misma interfaz
- **Overhead**: Puede ser excesivo para algoritmos simples

## Cuándo usar
- Tienes múltiples formas de realizar la misma tarea
- Quieres cambiar algoritmos dinámicamente
- Tienes muchas condicionales relacionadas con algoritmos
- Diferentes clientes necesitan diferentes variantes de un algoritmo

## Cuándo NO usar
- Solo tienes un algoritmo
- Los algoritmos nunca cambian
- Las diferencias entre algoritmos son mínimas
- La complejidad adicional no se justifica

## Diferencias con otros patrones
- **vs State**: Strategy se cambia externamente por el cliente, State cambia automáticamente según estado interno
- **vs Template Method**: Strategy cambia todo el algoritmo, Template Method solo algunos pasos del algoritmo