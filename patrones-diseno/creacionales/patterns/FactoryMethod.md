---
layout: page
title: "Factory Method"
---

## Problema
Crear objetos sin especificar su clase exacta, delegando la decisión de instanciación a las subclases.

## Propósito
Definir una interfaz para crear objetos, pero permitir que las subclases decidan qué clase instanciar. Encapsula la lógica de creación y reduce el acoplamiento entre el código cliente y las clases concretas.

## Casos de uso comunes
- Creadores de documentos (PDF, Word, Excel)
- Servicios de notificación (email, SMS, push)
- Parsers de diferentes formatos (JSON, XML, CSV)
- Loggers con diferentes destinos (archivo, consola, red)
- Conexiones de base de datos (MySQL, PostgreSQL, MongoDB)

## ¿Quién es quién en Factory Method?

| Actor | Lo que realmente es | Ejemplo | Analogía |
|-------|--------------------|---------|-----------|
| **Creator** | Clase abstracta con factory method y lógica común | `DocumentCreator` - define `createDocument()` | "Fábrica" (proceso general) |
| **ConcreteCreator** | Creadores que saben qué producto crear | `PDFCreator`, `WordCreator` | Fábrica de autos, fábrica de motos |
| **Product** | Interfaz que define qué pueden hacer | `Document` - define `open()`, `save()`, `close()` | "Vehículo" (qué puede hacer) |
| **ConcreteProduct** | Implementaciones reales de productos | `PDFDocument`, `WordDocument` | Auto, Moto (vehículos específicos) |

**Clave**: El cliente conoce el Creator pero NO el Product concreto

## Diagrama

```mermaid
classDiagram
    namespace FactoryMethod {
        class Creator {
            <<abstract>>
            +factoryMethod() Product
            +someOperation()
        }
        
        class ConcreteCreatorA {
            +factoryMethod() ProductA
        }
        
        class ConcreteCreatorB {
            +factoryMethod() ProductB
        }
        
        class Product {
            <<interface>>
            +doSomething()
        }
        
        class ProductA {
            +doSomething()
        }
        
        class ProductB {
            +doSomething()
        }
    }
    
    Creator <|-- ConcreteCreatorA
    Creator <|-- ConcreteCreatorB
    Product <|.. ProductA
    Product <|.. ProductB
    ConcreteCreatorA --> ProductA : creates
    ConcreteCreatorB --> ProductB : creates
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace DocumentExample {
        class Client {
            +main()
        }
        
        class DocumentCreator {
            <<abstract>>
            +createDocument() Document
            +processDocument()
        }
        
        class PDFCreator {
            +createDocument() PDFDocument
        }
        
        class WordCreator {
            +createDocument() WordDocument
        }
        
        class ExcelCreator {
            +createDocument() ExcelDocument
        }
        
        class Document {
            <<interface>>
            +open()
            +save()
            +close()
        }
        
        class PDFDocument {
            +open()
            +save()
            +close()
        }
        
        class WordDocument {
            +open()
            +save()
            +close()
        }
        
        class ExcelDocument {
            +open()
            +save()
            +close()
        }
    }
    
    Client --> DocumentCreator
    Client --> Document
    DocumentCreator <|-- PDFCreator
    DocumentCreator <|-- WordCreator
    DocumentCreator <|-- ExcelCreator
    Document <|.. PDFDocument
    Document <|.. WordDocument
    Document <|.. ExcelDocument
    PDFCreator --> PDFDocument : creates
    WordCreator --> WordDocument : creates
    ExcelCreator --> ExcelDocument : creates
```

## Flujo de creación

```mermaid
sequenceDiagram
    participant Client
    participant PDFCreator
    participant PDFDocument
    
    Note over Client: 1. Cliente instancia creator concreto
    Client->>PDFCreator: new PDFCreator()
    PDFCreator-->>Client: creator instance
    
    Note over Client: 2. Cliente solicita procesamiento
    Client->>PDFCreator: processDocument()
    PDFCreator->>PDFCreator: createDocument()
    PDFCreator->>PDFDocument: new PDFDocument()
    PDFDocument-->>PDFCreator: document instance
    
    Note over Client: 3. Creator usa el documento creado
    PDFCreator->>PDFDocument: open()
    PDFCreator->>PDFDocument: save()
    PDFCreator->>PDFDocument: close()
    PDFCreator-->>Client: processing complete
    
    Note over Client, PDFDocument: El cliente conoce PDFCreator pero no PDFDocument
```

## Limitación importante

En Factory Method **puro**, el cliente debe conocer qué creator concreto usar:

```javascript
// Cliente DEBE decidir qué creator usar
const creator = new PDFCreator();  // Cliente conoce PDFCreator
const doc = creator.processDocument(); // Pero NO conoce PDFDocument
doc.open(); // Usa polimorfismo
```

**El patrón desacopla**:
- ✅ Cliente de productos concretos
- ❌ Cliente de creators concretos

## Ventajas
- **Desacoplamiento**: Elimina dependencias directas con clases concretas
- **Extensibilidad**: Fácil agregar nuevos productos sin modificar código existente
- **Polimorfismo**: Permite crear objetos basados en parámetros o contexto
- **Single Responsibility**: Separa la lógica de creación de la lógica de negocio

## Desventajas
- **Complejidad**: Introduce más clases e interfaces
- **Jerarquías paralelas**: Cada producto requiere su creator correspondiente
- **Overhead**: Puede ser excesivo para casos simples

## Cuándo usar
- Necesitas crear objetos sin conocer su clase exacta
- Quieres delegar la decisión de creación a subclases
- Tienes múltiples implementaciones de una interfaz
- La lógica de creación es compleja o puede cambiar

## Cuándo NO usar
- Solo tienes una implementación
- La creación es simple y no cambiará
- No necesitas polimorfismo en la creación

## Variaciones del patrón

```mermaid
graph TB
    A[Factory Method Variations] --> B[Simple Factory]
    A --> C[Factory Method]
    A --> D[Abstract Factory]
    A --> E[Static Factory]
    
    B --> B1["Una clase con método estático<br/>No es patrón GoF"]
    C --> C1["Subclases deciden qué crear<br/>Patrón GoF clásico"]
    D --> D1["Familias de productos relacionados<br/>Múltiples factory methods"]
    E --> E1["Métodos estáticos de creación<br/>valueOf(), of(), getInstance()"]
```

## Simple Factory

```mermaid
classDiagram
    namespace SimpleFactoryComparison {
        class Client {
            +main()
        }
        
        class DocumentFactory {
            +createDocument(type) Document
        }
        
        class Document {
            <<interface>>
            +open()
            +save()
            +close()
        }
        
        class PDFDocument {
            +open()
            +save()
            +close()
        }
        
        class WordDocument {
            +open()
            +save()
            +close()
        }
    }
    
    Client --> DocumentFactory
    Client --> Document
    DocumentFactory --> PDFDocument : creates
    DocumentFactory --> WordDocument : creates
    Document <|.. PDFDocument
    Document <|.. WordDocument
```

**Simple Factory vs Factory Method**:

| Aspecto | Simple Factory | Factory Method |
|---------|----------------|----------------|
| **Estructura** | Una clase con switch/if | Jerarquía de clases |
| **Extensibilidad** | Modificar factory existente | Agregar nueva subclase |
| **Cliente conoce** | Solo la factory | Creator concreto |
| **Complejidad** | Menor | Mayor |
| **Flexibilidad** | Menor | Mayor |