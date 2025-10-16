---
layout: page
title: "Iterator"
---

## Problema
Recorrer una colección de diferentes maneras sin saber cómo está implementada internamente.

## Propósito
Permite navegar por cualquier colección (array, lista, árbol) usando la misma interfaz. Separa el "cómo recorrer" del "qué recorrer".

## Concepto clave
**Interfaz uniforme**: `hasNext()` y `next()` funcionan igual para arrays, listas, árboles o cualquier estructura. Como el `for-each` de Java que funciona con cualquier colección.

## Casos de uso comunes
- Recorrido de estructuras de datos complejas
- Paginación de resultados
- Streaming de datos
- Navegación en menús o árboles
- Procesamiento de listas grandes
- Múltiples formas de recorrer la misma estructura

## ¿Quién es quién en Iterator?

| Actor | Lo que realmente es | Ejemplo | Analogía |
|-------|--------------------|---------|-----------|
| **Iterator** | Interfaz que define `hasNext()` y `next()` | `BookIterator` - define cómo recorrer | "Forma de recorrer" (interfaz) |
| **ConcreteIterator** | Sabe CÓMO recorrer una estructura | `ForwardIterator`, `GenreIterator` | Bibliotecario específico (por género, autor) |
| **Aggregate** | Interfaz de colección con `createIterator()` | `BookCollection` - define qué iteradores crear | "Biblioteca" (interfaz) |
| **ConcreteAggregate** | Colección que crea sus iteradores | `Library` - crea iteradores para sí misma | Biblioteca real (tiene libros) |

## Diagrama

```mermaid
classDiagram
    namespace IteratorPattern {
        class Iterator {
            <<interface>>
            +hasNext() boolean
            +next() Object
            +remove()
        }
        
        class ConcreteIterator {
            -collection: ConcreteAggregate
            -currentPosition: int
            +hasNext() boolean
            +next() Object
            +remove()
        }
        
        class Aggregate {
            <<interface>>
            +createIterator() Iterator
        }
        
        class ConcreteAggregate {
            -items: List
            +createIterator() Iterator
            +getItem(index) Object
            +getCount() int
        }
    }
    
    Iterator <|.. ConcreteIterator
    Aggregate <|.. ConcreteAggregate
    ConcreteIterator --> ConcreteAggregate
    ConcreteAggregate --> ConcreteIterator : creates
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace BookCollectionExample {
        class BookIterator {
            <<interface>>
            +hasNext() boolean
            +next() Book
        }
        
        class BookCollection {
            <<interface>>
            +createIterator() BookIterator
            +createReverseIterator() BookIterator
            +createGenreIterator(genre) BookIterator
        }
        
        class Library {
            -books: List~Book~
            +createIterator() BookIterator
            +createReverseIterator() BookIterator
            +createGenreIterator(genre) BookIterator
            +addBook(book)
            +removeBook(book)
        }
        
        class ForwardIterator {
            -library: Library
            -position: int
            +hasNext() boolean
            +next() Book
        }
        
        class ReverseIterator {
            -library: Library
            -position: int
            +hasNext() boolean
            +next() Book
        }
        
        class GenreIterator {
            -library: Library
            -genre: String
            -position: int
            +hasNext() boolean
            +next() Book
        }
        
        class Book {
            -title: String
            -author: String
            -genre: String
        }
    }
    
    BookIterator <|.. ForwardIterator
    BookIterator <|.. ReverseIterator
    BookIterator <|.. GenreIterator
    BookCollection <|.. Library
    ForwardIterator --> Library
    ReverseIterator --> Library
    GenreIterator --> Library
    Library --> Book
```

## Tipos de iteradores

```mermaid
graph TB
    A[Iterator Types] --> B[Forward Iterator]
    A --> C[Reverse Iterator]
    A --> D[Bidirectional Iterator]
    A --> E[Random Access Iterator]
    A --> F[Filtered Iterator]
    
    B --> B1["Recorre hacia adelante<br/>Más común y simple"]
    C --> C1["Recorre hacia atrás<br/>Útil para deshacer operaciones"]
    D --> D1["Recorre en ambas direcciones<br/>Navegación flexible"]
    E --> E1["Acceso directo por índice<br/>Saltos eficientes"]
    F --> F1["Filtra elementos durante recorrido<br/>Criterios específicos"]
```

## Flujo de iteración

```mermaid
sequenceDiagram
    participant Client
    participant Collection
    participant Iterator
    
    Client->>Collection: createIterator()
    Collection->>Iterator: new ConcreteIterator(this)
    Iterator-->>Collection: iterator instance
    Collection-->>Client: iterator
    
    loop While has more elements
        Client->>Iterator: hasNext()
        Iterator-->>Client: true/false
        
        alt Has next element
            Client->>Iterator: next()
            Iterator->>Collection: getItem(currentPosition)
            Collection-->>Iterator: item
            Iterator->>Iterator: increment position
            Iterator-->>Client: item
            Client->>Client: process(item)
        end
    end
```

## Ventajas
- **Encapsulación**: Oculta la representación interna de la colección
- **Múltiples recorridos**: Diferentes formas de recorrer la misma estructura
- **Interfaz uniforme**: Misma interfaz para diferentes tipos de colecciones
- **Separación de responsabilidades**: Recorrido separado de la estructura

## Desventajas
- **Complejidad**: Introduce clases adicionales
- **Overhead**: Puede ser más lento que acceso directo
- **Estado**: Mantener estado del iterador puede ser complejo
- **Concurrencia**: Problemas si la colección cambia durante iteración

## Cuándo usar
- Necesitas diferentes formas de recorrer la misma colección
- Quieres ocultar la complejidad interna de la estructura
- Necesitas una interfaz uniforme para diferentes tipos de colecciones
- Quieres poder pausar y reanudar el recorrido

## Cuándo NO usar
- Solo necesitas una forma simple de recorrido
- La colección es muy simple (array básico)
- El acceso directo por índice es suficiente
- La complejidad adicional no aporta valor