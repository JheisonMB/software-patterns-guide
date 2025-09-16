---
layout: page
title: "Flyweight"
---

## Problema
Minimizar el uso de memoria cuando se necesita crear gran cantidad de objetos similares que comparten características comunes.

## Propósito
Compartir objetos para ahorrar memoria. En lugar de crear 1000 objetos idénticos, crea 1 objeto y compártelo 1000 veces.

## Concepto clave
**Compartir lo común, separar lo único**: Los datos que no cambian (intrínsecos) se guardan en el flyweight compartido. Los datos que sí cambian (extrínsecos) se pasan como parámetros.

## Casos de uso comunes
- **Juegos**: 1000 balas del mismo tipo, solo cambia posición
- **Texto**: Miles de letras 'A', solo cambia posición y color
- **Árboles**: Bosque con 500 robles, solo cambia posición y tamaño
- **Iconos**: Mismos iconos en diferentes lugares de la UI
- **Partículas**: Efectos de fuego, humo, lluvia

## Diagrama

```mermaid
classDiagram
    namespace FlyweightPattern {
        class Flyweight {
            <<interface>>
            +operation(extrinsicState)
        }
        
        class ConcreteFlyweight {
            -intrinsicState
            +operation(extrinsicState)
        }
        
        class FlyweightFactory {
            -flyweights: Map
            +getFlyweight(key) Flyweight
        }
        
        class Context {
            -extrinsicState
            -flyweight: Flyweight
            +operation()
        }
    }
    
    Flyweight <|.. ConcreteFlyweight
    FlyweightFactory --> Flyweight
    Context --> Flyweight
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace TreeExample {
        class TreeFlyweight {
            <<interface>>
            +render(x, y, size)
        }
        
        class TreeType {
            -name: String
            -sprite: Image
            +render(x, y, size)
        }
        
        class TreeFactory {
            -flyweights: Map
            -getTreeType(name) TreeFlyweight
            +createTree(x, y, size, typeName) Tree
        }
        
        class Tree {
            -x: int
            -y: int
            -size: int
            -flyweight: TreeFlyweight
            +render()
        }
    }
    
    TreeFlyweight <|.. TreeType
    TreeFactory --> TreeFlyweight
    TreeFactory --> Tree
    Tree --> TreeFlyweight
```

## Flujo de operación

```mermaid
sequenceDiagram
    participant Client
    participant Factory as TreeFactory
    participant Flyweight as TreeType
    participant Tree1 as Tree #1
    participant Tree2 as Tree #2
    
    Client->>Factory: createTree(10, 20, 5, "Roble")
    Factory->>Factory: getTreeType("Roble")
    Note over Factory: Crea nuevo TreeType("Roble")
    Factory->>Flyweight: new TreeType("Roble", sprite)
    Factory->>Tree1: new Tree(10, 20, 5, flyweight)
    Factory-->>Client: Tree #1
    
    Client->>Factory: createTree(15, 25, 6, "Roble")
    Factory->>Factory: getTreeType("Roble")
    Note over Factory: Reutiliza TreeType existente
    Factory->>Tree2: new Tree(15, 25, 6, flyweight)
    Factory-->>Client: Tree #2
    
    Client->>Tree1: render()
    Tree1->>Flyweight: render(10, 20, 5)
    Note over Flyweight: Usa datos intrínsecos + extrínsecos
    Flyweight-->>Client: árbol renderizado
    
    Client->>Tree2: render()
    Tree2->>Flyweight: render(15, 25, 6)
    Note over Flyweight: MISMO flyweight, datos diferentes
    Flyweight-->>Client: árbol renderizado
```

## Ahorro de memoria

```mermaid
graph LR
    subgraph "Sin Flyweight"
        A1["Tree #1<br/>name='Roble'<br/>sprite=roble.png<br/>x=10, y=20, size=5"]
        A2["Tree #2<br/>name='Roble'<br/>sprite=roble.png<br/>x=15, y=25, size=6"]
        A3["Tree #3<br/>name='Roble'<br/>sprite=roble.png<br/>x=20, y=30, size=4"]
    end
    
    subgraph "Con Flyweight"
        F["TreeType<br/>name='Roble'<br/>sprite=roble.png<br/>(COMPARTIDO)"]
        
        T1["Tree #1<br/>x=10, y=20, size=5"]
        T2["Tree #2<br/>x=15, y=25, size=6"]
        T3["Tree #3<br/>x=20, y=30, size=4"]
        
        T1 --> F
        T2 --> F
        T3 --> F
    end
```

## Ventajas
- **Memoria**: Reduce significativamente el uso de memoria
- **Performance**: Menos objetos = menos presión en el Garbage Collector
- **Escalabilidad**: Permite manejar gran cantidad de objetos
- **Compartición**: Reutiliza objetos inmutables eficientemente

## Desventajas
- **Complejidad**: Separar estado intrínseco del extrínseco puede ser complejo
- **Cálculos**: Puede introducir overhead de cálculos para el estado extrínseco
- **Referencias circulares**: Problemas con objetos que se referencian mutuamente
- **Inmutabilidad**: Los flyweights deben ser inmutables

## Cuándo usar
- Necesitas crear gran cantidad de objetos similares
- El costo de almacenamiento es alto debido a la cantidad de objetos
- El estado extrínseco puede ser calculado o pasado como parámetro
- Los objetos pueden ser agrupados por características compartidas

## Cuándo NO usar
- Tienes pocos objetos
- Los objetos no comparten características significativas
- El estado extrínseco es difícil de separar o calcular
- La aplicación no tiene restricciones de memoria