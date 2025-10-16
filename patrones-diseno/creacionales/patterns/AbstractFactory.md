---
layout: page
title: "Abstract Factory"
---

## Problema
Crear familias de objetos relacionados sin especificar sus clases concretas, garantizando que los productos de una familia sean compatibles entre sí.

## Propósito
Proporcionar una interfaz para crear familias de objetos relacionados o dependientes sin especificar sus clases concretas. Asegura que los productos creados por una factory sean compatibles entre sí.

## Casos de uso comunes
- UI components para diferentes sistemas operativos
- Drivers de base de datos con diferentes proveedores
- Temas visuales (dark/light mode)
- Formatos de exportación (PDF, Excel, Word)
- Protocolos de comunicación (HTTP, HTTPS, FTP)

## ¿Quién es quién en Abstract Factory?

| Actor | Lo que realmente es | Ejemplo | Analogía |
|-------|--------------------|---------|-----------|
| **AbstractFactory** | Interfaz de fábrica que define `createProductX()` | `UIFactory` - define qué componentes crear | "Fábrica de muebles" (interfaz) |
| **ConcreteFactory** | Fábricas que crean productos de UNA familia | `WindowsFactory`, `MacFactory` | Fábrica de muebles modernos vs clásicos |
| **AbstractProduct** | Interfaces de productos | `Button`, `Checkbox` - definen qué pueden hacer | "Silla", "Mesa" (tipos de muebles) |
| **ConcreteProduct** | Implementaciones concretas de productos | `WindowsButton`, `MacButton` | Silla moderna vs Silla clásica |
| **Client** | Usa productos, solo conoce interfaces abstractas | `Application` - no sabe si usa Windows o Mac | Decorador (quiere muebles que combinen) |

## Diagrama

```mermaid
classDiagram
    namespace AbstractFactoryPattern {
        class Client {
            +main()
        }
        
        class AbstractFactory {
            <<interface>>
            +createProductA() AbstractProductA
            +createProductB() AbstractProductB
        }
        
        class ConcreteFactory1 {
            +createProductA() ProductA1
            +createProductB() ProductB1
        }
        
        class ConcreteFactory2 {
            +createProductA() ProductA2
            +createProductB() ProductB2
        }
        
        class AbstractProductA {
            <<interface>>
            +operationA()
        }
        
        class AbstractProductB {
            <<interface>>
            +operationB()
        }
        
        class ProductA1 {
            +operationA()
        }
        
        class ProductA2 {
            +operationA()
        }
        
        class ProductB1 {
            +operationB()
        }
        
        class ProductB2 {
            +operationB()
        }
    }
    
    Client --> AbstractFactory
    Client --> AbstractProductA
    Client --> AbstractProductB
    AbstractFactory <|.. ConcreteFactory1
    AbstractFactory <|.. ConcreteFactory2
    AbstractProductA <|.. ProductA1
    AbstractProductA <|.. ProductA2
    AbstractProductB <|.. ProductB1
    AbstractProductB <|.. ProductB2
    ConcreteFactory1 --> ProductA1
    ConcreteFactory1 --> ProductB1
    ConcreteFactory2 --> ProductA2
    ConcreteFactory2 --> ProductB2
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace UIExample {
        class Application {
            +main()
        }
        
        class UIFactory {
            <<interface>>
            +createButton() Button
            +createCheckbox() Checkbox
            +createTextInput() TextInput
        }
        
        class WindowsFactory {
            +createButton() WindowsButton
            +createCheckbox() WindowsCheckbox
            +createTextInput() WindowsTextInput
        }
        
        class MacFactory {
            +createButton() MacButton
            +createCheckbox() MacCheckbox
            +createTextInput() MacTextInput
        }
        
        class Button {
            <<interface>>
            +render()
            +onClick()
        }
        
        class Checkbox {
            <<interface>>
            +render()
            +toggle()
        }
        
        class TextInput {
            <<interface>>
            +render()
            +setValue(text)
        }
        
        class WindowsButton {
            +render()
            +onClick()
        }
        
        class MacButton {
            +render()
            +onClick()
        }
        
        class WindowsCheckbox {
            +render()
            +toggle()
        }
        
        class MacCheckbox {
            +render()
            +toggle()
        }
        
        class WindowsTextInput {
            +render()
            +setValue(text)
        }
        
        class MacTextInput {
            +render()
            +setValue(text)
        }
    }
    
    Application --> UIFactory
    Application --> Button
    Application --> Checkbox
    Application --> TextInput
    UIFactory <|.. WindowsFactory
    UIFactory <|.. MacFactory
    Button <|.. WindowsButton
    Button <|.. MacButton
    Checkbox <|.. WindowsCheckbox
    Checkbox <|.. MacCheckbox
    TextInput <|.. WindowsTextInput
    TextInput <|.. MacTextInput
    WindowsFactory --> WindowsButton
    WindowsFactory --> WindowsCheckbox
    WindowsFactory --> WindowsTextInput
    MacFactory --> MacButton
    MacFactory --> MacCheckbox
    MacFactory --> MacTextInput
```

## Familias de productos

```mermaid
graph TB
    subgraph "Familia 1 (ConcreteFactory1)"
        PA1[ProductA1]
        PB1[ProductB1]
    end
    
    subgraph "Familia 2 (ConcreteFactory2)"
        PA2[ProductA2]
        PB2[ProductB2]
    end
    
    CF1[ConcreteFactory1] --> PA1
    CF1 --> PB1
    
    CF2[ConcreteFactory2] --> PA2
    CF2 --> PB2
    
    style PA1 fill:#e1f5fe
    style PB1 fill:#e1f5fe
    style PA2 fill:#f3e5f5
    style PB2 fill:#f3e5f5
```

## Flujo de creación

```mermaid
sequenceDiagram
    participant Client
    participant WindowsFactory
    participant WindowsButton
    participant WindowsCheckbox
    
    Note over Client: 1. Cliente instancia factory concreta
    Client->>WindowsFactory: new WindowsFactory()
    WindowsFactory-->>Client: factory instance
    
    Note over Client: 2. Cliente crea productos usando la factory
    Client->>WindowsFactory: createButton()
    WindowsFactory->>WindowsButton: new WindowsButton()
    WindowsButton-->>WindowsFactory: button instance
    WindowsFactory-->>Client: WindowsButton
    
    Client->>WindowsFactory: createCheckbox()
    WindowsFactory->>WindowsCheckbox: new WindowsCheckbox()
    WindowsCheckbox-->>WindowsFactory: checkbox instance
    WindowsFactory-->>Client: WindowsCheckbox
    
    Note over Client: 3. Cliente usa productos polimórficamente
    Client->>WindowsButton: render()
    Client->>WindowsCheckbox: render()
    
    Note over Client, WindowsCheckbox: Todos los productos son de la misma familia (Windows)
```

## Ventajas
- **Consistencia**: Garantiza que los productos de una familia sean compatibles
- **Flexibilidad**: Fácil intercambio entre familias de productos
- **Aislamiento**: El código cliente no depende de clases concretas
- **Extensibilidad**: Fácil agregar nuevas familias de productos

## Desventajas
- **Complejidad**: Introduce muchas interfaces y clases
- **Extensibilidad**: Agregar nuevos productos requiere modificar todas las factories
- **Rigidez**: Difícil cambiar la interfaz de productos existentes

## Cuándo usar
- El sistema debe ser independiente de cómo se crean sus productos
- Necesitas garantizar que productos de una familia se usen juntos
- Quieres proporcionar una biblioteca de productos ocultando sus implementaciones
- Tienes múltiples familias de productos relacionados

## Cuándo NO usar
- Solo tienes una familia de productos
- Los productos no están relacionados entre sí
- La complejidad adicional no se justifica
- No necesitas garantizar compatibilidad entre productos

## Extensiones prácticas comunes

En implementaciones reales, es común agregar un **Factory Provider** (Simple Factory o Factory Method) para decidir qué factory concreta usar:

```javascript
// Patrón puro (cliente decide)
const factory = new WindowsFactory();

// Extensión práctica (provider decide)
const factory = FactoryProvider.getFactory(platform);
```

Esto **no es parte del patrón Abstract Factory**, sino un patrón complementario que resuelve el problema de selección dinámica.
