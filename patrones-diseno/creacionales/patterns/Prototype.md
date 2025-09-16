---
layout: page
title: "Prototype"
---

## Problema
Crear objetos clonando instancias existentes cuando la creación directa es costosa o compleja.

## Propósito
Especificar los tipos de objetos a crear usando una instancia prototípica, y crear nuevos objetos copiando este prototipo. Evita el costo de creación desde cero cuando la clonación es más eficiente.

## Casos de uso comunes
- Configuraciones de juegos (personajes, niveles)
- Templates de documentos o emails
- Objetos con inicialización costosa
- Caches de objetos complejos
- Sistemas con muchas variaciones de objetos similares

## Diagrama

```mermaid
classDiagram
    namespace PrototypePattern {
        class Prototype {
            <<interface>>
            +clone() Prototype
        }
        
        class ConcretePrototype1 {
            -field1: String
            -field2: int
            +clone() ConcretePrototype1
            +setField1(value)
            +setField2(value)
        }
        
        class ConcretePrototype2 {
            -fieldA: String
            -fieldB: List~String~
            +clone() ConcretePrototype2
            +setFieldA(value)
            +setFieldB(value)
        }
        
        class PrototypeRegistry {
            -prototypes: Map~String, Prototype~
            +register(key, prototype)
            +getPrototype(key) Prototype
        }
        
        class Client {
            +makePrototype(key) Prototype
        }
    }
    
    Prototype <|.. ConcretePrototype1
    Prototype <|.. ConcretePrototype2
    PrototypeRegistry --> Prototype
    Client --> PrototypeRegistry
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace GameExample {
        class GameCharacter {
            <<interface>>
            +clone() GameCharacter
            +customize(name, level)
        }
        
        class Warrior {
            -skills: List~String~
            -equipment: Equipment
            -stats: Stats
            +clone() Warrior
            +customize(name, level)
        }
        
        class Mage {
            -spells: List~String~
            -manaPool: int
            -stats: Stats
            +clone() Mage
            +customize(name, level)
        }
        
        class CharacterRegistry {
            -prototypes: Map~String, GameCharacter~
            +registerPrototype(type, character)
            +createCharacter(type, name, level) GameCharacter
        }
        
        class GameSession {
            -registry: CharacterRegistry
            +createPlayerCharacter(type, name) GameCharacter
        }
    }
    
    GameCharacter <|.. Warrior
    GameCharacter <|.. Mage
    CharacterRegistry --> GameCharacter
    GameSession --> CharacterRegistry
```

## Tipos de clonación

**Shallow Clone**: Copia el objeto pero comparte referencias internas (arrays, objetos)
**Deep Clone**: Copia el objeto y todos sus objetos internos independientemente

```mermaid
graph LR
    subgraph "Shallow Clone"
        O1[Original] --> A1["name: 'Warrior'"]
        O1 --> B1["skills: ['Attack']"]
        C1[Clone] --> A2["name: 'Warrior'"]
        C1 --> B1
        style B1 fill:#ffcccc
    end
    
    subgraph "Deep Clone"
        O2[Original] --> A3["name: 'Warrior'"]
        O2 --> B2["skills: ['Attack']"]
        C2[Clone] --> A4["name: 'Warrior'"]
        C2 --> B3["skills: ['Attack']"]
        style B2 fill:#ccffcc
        style B3 fill:#ccffcc
    end
```

## Flujo de clonación

```mermaid
sequenceDiagram
    participant Client
    participant Registry
    participant Prototype
    participant Clone
    
    Client->>Registry: createCharacter("warrior", "Aragorn")
    Registry->>Registry: getPrototype("warrior")
    Registry->>Prototype: clone()
    Prototype->>Clone: deepCopy(this)
    Clone-->>Registry: new character
    Registry->>Clone: customize("Aragorn", 1)
    Registry-->>Client: GameCharacter
```

## Ventajas
- **Performance**: Evita el costo de creación de objetos complejos
- **Flexibilidad**: Permite crear objetos en tiempo de ejecución
- **Configuración**: Útil para objetos con muchas configuraciones predefinidas
- **Independencia**: Reduce dependencias de clases concretas

## Desventajas
- **Complejidad**: Implementar deep cloning puede ser complejo
- **Referencias circulares**: Problemas con objetos que se referencian mutuamente
- **Mantenimiento**: Cambios en la clase requieren actualizar el método clone
- **Inmutabilidad**: Los prototipos deben manejar correctamente la mutabilidad

## Cuándo usar
- La creación de objetos es costosa (consultas DB, cálculos complejos)
- Necesitas muchas variaciones de un objeto base
- Quieres evitar subclases de Factory
- Los objetos tienen configuraciones complejas que se reutilizan

## Cuándo NO usar
- La creación de objetos es simple y rápida
- Los objetos no comparten configuraciones comunes
- Deep cloning es demasiado complejo de implementar
- Prefieres inmutabilidad sobre clonación

## Diferencias con otros patrones
- **vs Factory Method**: Prototype clona existentes, Factory crea desde cero
- **vs Builder**: Prototype clona configurados, Builder construye paso a paso