---
layout: page
title: "Proxy"
---

## Problema
Controlar el acceso a otro objeto proporcionando un sustituto o placeholder que puede agregar funcionalidad adicional.

## Propósito
Proporcionar un sustituto o placeholder para otro objeto para controlar el acceso a él. Permite agregar funcionalidad como lazy loading, caching, logging, control de acceso o validación sin modificar el objeto original.

## Casos de uso comunes
- Lazy loading de recursos costosos
- Control de acceso y seguridad
- Caching de resultados
- Logging y auditoría
- Validación antes de operaciones
- Conexiones remotas (Remote Proxy)

## ¿Quién es quién en Proxy?

| Actor | Lo que realmente es | Ejemplo | Analogía |
|-------|--------------------|---------|-----------|
| **Subject** | Interfaz común para Proxy y RealSubject | `Image` - operaciones que ambos implementan | "Reproductor de video" (interfaz) |
| **RealSubject** | Objeto real que hace el trabajo pesado | `HighResolutionImage` - carga y muestra imagen real | Archivo de video real (pesado, en disco) |
| **Proxy** | Sustituto que controla acceso y agrega funcionalidad | `ImageProxy` - lazy loading, caching, control | Thumbnail/preview (ligero, controla carga) |
| **Client** | Usa el servicio sin saber si es Proxy o Real | `ImageViewer` - no sabe si imagen está cargada | Persona que quiere ver película |

## Diagrama

```mermaid
classDiagram
    namespace ProxyPattern {
        class Subject {
            <<interface>>
            +request()
        }
        
        class RealSubject {
            +request()
        }
        
        class Proxy {
            -realSubject: RealSubject
            +request()
            -checkAccess() boolean
            -logAccess()
        }
        
        class Client {
            +clientCode(Subject)
        }
    }
    
    Subject <|.. RealSubject
    Subject <|.. Proxy
    Proxy --> RealSubject
    Client --> Subject
```

## Tipos de Proxy

```mermaid
graph TB
    A[Proxy Pattern] --> B[Virtual Proxy]
    A --> C[Protection Proxy]
    A --> D[Remote Proxy]
    A --> E[Caching Proxy]
    A --> F[Smart Reference]
    
    B --> B1["Lazy loading<br/>Objetos costosos"]
    C --> C1["Control de acceso<br/>Autenticación"]
    D --> D1["Objetos remotos<br/>RPC, Web Services"]
    E --> E1["Cache de resultados<br/>Optimización"]
    F --> F1["Referencias inteligentes<br/>Conteo, logging"]
```

## Ejemplo práctico - Virtual Proxy

```mermaid
classDiagram
    namespace VirtualProxyExample {
        class Image {
            <<interface>>
            +display()
            +getInfo() String
        }
        
        class HighResolutionImage {
            -filename: String
            -imageData: byte[]
            +display()
            +getInfo() String
            -loadImageFromDisk()
        }
        
        class ImageProxy {
            -filename: String
            -realImage: HighResolutionImage
            +display()
            +getInfo() String
        }
        
        class ImageViewer {
            -images: List~Image~
            +viewImage(index)
        }
    }
    
    Image <|.. HighResolutionImage
    Image <|.. ImageProxy
    ImageProxy --> HighResolutionImage
    ImageViewer --> Image
```

## Flujo de Virtual Proxy

```mermaid
sequenceDiagram
    participant Client
    participant ImageProxy
    participant RealImage as HighResolutionImage
    
    Client->>ImageProxy: new ImageProxy("photo.jpg")
    Note over ImageProxy: Proxy creado, imagen NO cargada
    
    Client->>ImageProxy: getInfo()
    ImageProxy-->>Client: "ImageProxy: photo.jpg (not loaded)"
    
    Client->>ImageProxy: display()
    Note over ImageProxy: Primera llamada - lazy loading
    ImageProxy->>RealImage: new HighResolutionImage("photo.jpg")
    Note over RealImage: Carga costosa desde disco
    RealImage-->>ImageProxy: imagen cargada
    ImageProxy->>RealImage: display()
    RealImage-->>Client: imagen mostrada
    
    Client->>ImageProxy: display()
    Note over ImageProxy: Llamadas posteriores - sin carga
    ImageProxy->>RealImage: display()
    RealImage-->>Client: imagen mostrada
```

## Ejemplo práctico - Protection Proxy

```mermaid
classDiagram
    namespace ProtectionProxyExample {
        class BankAccount {
            <<interface>>
            +deposit(amount)
            +withdraw(amount)
            +getBalance() double
        }
        
        class RealBankAccount {
            -accountNumber: String
            -balance: double
            +deposit(amount)
            +withdraw(amount)
            +getBalance() double
        }
        
        class SecureBankAccountProxy {
            -realAccount: RealBankAccount
            -currentUser: String
            -accountOwner: String
            +deposit(amount)
            +withdraw(amount)
            +getBalance() double
            -hasAccess() boolean
            -logAccess(operation)
        }
    }
    
    BankAccount <|.. RealBankAccount
    BankAccount <|.. SecureBankAccountProxy
    SecureBankAccountProxy --> RealBankAccount
```

## Proxy vs Facade vs Adapter

```
Proxy:
• MISMA interfaz que el objeto real
• Controla ACCESO al objeto (cuándo, cómo)
• Relación 1:1 (un proxy por objeto)
• Ejemplo: ImageProxy controla cuándo cargar HighResolutionImage

Facade:
• NUEVA interfaz simplificada
• Coordina MÚTIPLES subsistemas
• Relación 1:N (una facade para muchos objetos)
• Ejemplo: HomeTheaterFacade coordina audio + video + luces

Adapter:
• CONVIERTE una interfaz en otra
• Hace compatibles interfaces incompatibles
• Relación 1:1 (adaptador por objeto incompatible)
• Ejemplo: XMLAdapter convierte XML a JSON
```

## ¿Por qué ambos implementan Subject?

```java
// Cliente solo conoce la interfaz
Subject image = getImage(); // Puede ser Proxy o RealSubject
image.display();            // Cliente no sabe cuál es

// Proxy controla el acceso
class ImageProxy implements Subject {
    public void display() {
        if (hasPermission()) {          // Control de acceso
            if (realImage == null) {    // Lazy loading
                realImage = new HighResolutionImage();
            }
            realImage.display();        // Delega al objeto real
        }
    }
}
```

## Ventajas
- **Control**: Controla acceso al objeto real
- **Optimización**: Lazy loading, caching, etc.
- **Transparencia**: Cliente no sabe que está usando un proxy
- **Funcionalidad adicional**: Logging, validación, seguridad sin modificar el objeto original

## Desventajas
- **Complejidad**: Introduce una capa adicional de abstracción
- **Latencia**: Puede introducir overhead mínimo
- **Mantenimiento**: Cambios en el subject pueden requerir cambios en el proxy
- **Memoria**: El proxy mantiene referencias adicionales

## Cuándo usar
- **Lazy loading**: Cuando la creación del objeto es costosa
- **Control de acceso**: Cuando necesitas verificar permisos
- **Caching**: Cuando quieres cachear resultados costosos
- **Logging**: Cuando necesitas registrar accesos
- **Validación**: Cuando necesitas validar antes de operaciones

## Cuándo NO usar
- El acceso directo al objeto es suficiente
- No necesitas funcionalidad adicional
- La complejidad adicional no se justifica
- El overhead del proxy es inaceptable