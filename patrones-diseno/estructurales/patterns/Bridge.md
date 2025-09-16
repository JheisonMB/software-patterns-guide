---
layout: page
title: "Bridge"
---

## Problema
Evitar el binding permanente entre una abstracción y su implementación, permitiendo que ambas puedan variar independientemente.

## Propósito
Separar una abstracción de su implementación para que ambas puedan evolucionar de forma independiente. Utiliza composición en lugar de herencia para conectar diferentes jerarquías de clases.

## Bridge vs Adapter - ¿Cuál es la diferencia?

| Aspecto | Bridge | Adapter |
|---------|--------|---------|
| **Propósito** | Diseñar para flexibilidad futura | Arreglar incompatibilidad existente |
| **Cuándo** | Durante diseño inicial | Después de que el código existe |
| **Problema** | "Quiero que evolucionen independientemente" | "Estas clases son incompatibles" |
| **Enfoque** | Proactivo (planificación) | Reactivo (solución) |
| **Estructura** | Dos jerarquías separadas conectadas | Una clase que traduce interfaces |

## Casos de uso comunes
- Sistemas multiplataforma (Windows, Mac, Linux)
- Drivers de base de datos (MySQL, PostgreSQL, Oracle)
- Sistemas de notificación con múltiples canales
- APIs con diferentes proveedores

## Diagrama

```mermaid
classDiagram
    namespace Bridge {
        class Abstraction {
            -implementation: Implementation
            +operation()
        }
        
        class RefinedAbstraction {
            +operation()
            +extendedOperation()
        }
        
        class Implementation {
            <<interface>>
            +operationImpl()
        }
        
        class ConcreteImplementationA {
            +operationImpl()
        }
        
        class ConcreteImplementationB {
            +operationImpl()
        }
    }
    
    Abstraction <|-- RefinedAbstraction
    Abstraction --> Implementation
    Implementation <|.. ConcreteImplementationA
    Implementation <|.. ConcreteImplementationB
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace NotificationExample {
        class Notification {
            -sender: MessageSender
            +send(message, recipient)
        }
        
        class UrgentNotification {
            +send(message, recipient)
        }
        
        class SimpleNotification {
            +send(message, recipient)
        }
        
        class MessageSender {
            <<interface>>
            +sendMessage(message, recipient)
        }
        
        class EmailSender {
            +sendMessage(message, recipient)
        }
        
        class SMSSender {
            +sendMessage(message, recipient)
        }
        
        class SlackSender {
            +sendMessage(message, recipient)
        }
    }
    
    Notification <|-- UrgentNotification
    Notification <|-- SimpleNotification
    Notification --> MessageSender
    MessageSender <|.. EmailSender
    MessageSender <|.. SMSSender
    MessageSender <|.. SlackSender
```

## Flujo de funcionamiento

```mermaid
sequenceDiagram
    participant Client
    participant UrgentNotification
    participant SMSSender
    
    Client->>UrgentNotification: send("Server down!", "+1234567890")
    Note over UrgentNotification: Modifica el mensaje (agrega 🚨 URGENT)
    UrgentNotification->>SMSSender: sendMessage("🚨 URGENT: Server down!", "+1234567890")
    Note over SMSSender: Implementa el envío real por SMS
    SMSSender-->>UrgentNotification: "sms-sent"
    UrgentNotification-->>Client: "sms-sent"
```

## Aclaración de terminología confusa

**Los nombres del patrón Bridge son engañosos:**

| Nombre en el patrón | Lo que realmente es | Ejemplo |
|---------------------|--------------------|---------|
| **"Abstraction"** | Clase de alto nivel que define QUÉ hacer | `Notification` - define cómo manejar notificaciones |
| **"Implementation"** | Interfaz que define CÓMO hacer | `MessageSender` - define cómo enviar mensajes |
| **"RefinedAbstraction"** | Variaciones de la clase de alto nivel | `UrgentNotification` - variación que agrega urgencia |
| **"ConcreteImplementation"** | Implementaciones reales del CÓMO | `SMSSender`, `EmailSender` - formas concretas de enviar |

## ¿Cómo separa abstracción de implementación?

**Básicamente es polimorfismo en el constructor, pero el valor está en la SEPARACIÓN:**

```javascript
// ABSTRACCIÓN: Define QUÉ hacer (lógica de negocio)
class UrgentNotification {
    constructor(sender) {
        this.sender = sender; // Bridge: no sabe CÓMO enviar
    }
    
    send(message, recipient) {
        // Lógica de negocio: agregar urgencia
        const urgentMessage = `🚨 URGENT: ${message}`;
        
        // Delega el CÓMO a la implementación
        return this.sender.sendMessage(urgentMessage, recipient);
    }
}

// IMPLEMENTACIÓN: Define CÓMO hacer (detalles técnicos)
class SMSSender {
    sendMessage(message, recipient) {
        // Detalles técnicos: cómo enviar SMS
        console.log(`Connecting to SMS gateway...`);
        console.log(`Sending SMS to ${recipient}: ${message}`);
        return "sms-sent";
    }
}
```

## La separación en acción

**Puedes cambiar la ABSTRACCIÓN (qué hacer) sin tocar la IMPLEMENTACIÓN (cómo hacer):**

```javascript
// Nueva abstracción - no toca las implementaciones
class CriticalNotification {
    constructor(sender) {
        this.sender = sender;
    }
    
    send(message, recipient) {
        const criticalMessage = `⚠️ CRITICAL: ${message} - IMMEDIATE ACTION REQUIRED`;
        return this.sender.sendMessage(criticalMessage, recipient);
    }
}

// Funciona con TODAS las implementaciones existentes
const criticalEmail = new CriticalNotification(new EmailSender());
const criticalSMS = new CriticalNotification(new SMSSender());
```

**Puedes cambiar la IMPLEMENTACIÓN (cómo hacer) sin tocar la ABSTRACCIÓN (qué hacer):**

```javascript
// Nueva implementación - no toca las abstracciones
class WhatsAppSender {
    sendMessage(message, recipient) {
        console.log(`Connecting to WhatsApp API...`);
        console.log(`Sending WhatsApp to ${recipient}: ${message}`);
        return "whatsapp-sent";
    }
}

// Funciona con TODAS las abstracciones existentes
const urgentWhatsApp = new UrgentNotification(new WhatsAppSender());
const criticalWhatsApp = new CriticalNotification(new WhatsAppSender());
```

## El valor real: Evolución independiente

**Sin Bridge (acoplado):**
```javascript
class UrgentEmailNotification {
    send(message, recipient) {
        const urgentMessage = `🚨 URGENT: ${message}`;
        // Acoplado: sabe cómo enviar email
        console.log(`Email to ${recipient}: ${urgentMessage}`);
    }
}

// Si cambias cómo enviar email, debes tocar TODAS las clases
// Si agregas nuevo tipo, debes crear clases para TODOS los métodos
```

**Con Bridge (desacoplado):**
```javascript
class UrgentNotification {
    constructor(sender) {
        this.sender = sender; // Desacoplado: no sabe cómo enviar
    }
    
    send(message, recipient) {
        const urgentMessage = `🚨 URGENT: ${message}`;
        return this.sender.sendMessage(urgentMessage, recipient);
    }
}

// Cambios en implementación NO afectan abstracción
// Cambios en abstracción NO afectan implementación
```

## Ventajas
- **Desacoplamiento**: Abstracción e implementación evolucionan independientemente
- **Extensibilidad**: Fácil agregar nuevas abstracciones o implementaciones
- **Flexibilidad**: Combinaciones dinámicas en tiempo de ejecución
- **Reutilización**: Las implementaciones pueden ser compartidas

## Desventajas
- **Complejidad**: Introduce más clases e interfaces
- **Indirección**: Puede afectar ligeramente el rendimiento
- **Diseño inicial**: Requiere planificación previa de las jerarquías

## Cuándo usar
- Quieres evitar binding permanente entre abstracción e implementación
- Tanto abstracción como implementación deben ser extensibles
- Necesitas compartir implementaciones entre múltiples abstracciones
- Cambios en implementación no deben afectar clientes

## Cuándo NO usar
- Solo tienes una abstracción y una implementación
- La relación entre abstracción e implementación es simple y estable
- La complejidad adicional no se justifica

## Las dos jerarquías independientes

```mermaid
graph TB
    subgraph "Jerarquía 1: Tipos de Notificación (QUÉ hacer)"
        N[Notification]
        N --> UN[UrgentNotification]
        N --> SN[SimpleNotification]
        N --> CN[CriticalNotification]
    end
    
    subgraph "Jerarquía 2: Métodos de Envío (CÓMO hacer)"
        MS[MessageSender Interface]
        MS --> ES[EmailSender]
        MS --> SS[SMSSender]
        MS --> WS[WhatsAppSender]
        MS --> SLS[SlackSender]
    end
    
    N -.->|"Bridge: this.sender"| MS
    
    style N fill:#e1f5fe
    style MS fill:#f3e5f5
```

**El Bridge conecta ambas jerarquías:**
- **Jerarquía 1** puede evolucionar independientemente (agregar nuevos tipos)
- **Jerarquía 2** puede evolucionar independientemente (agregar nuevos métodos)
- **Cualquier combinación** funciona automáticamente

**Matriz de combinaciones:**

| Abstracción \ Implementación | EmailSender | SMSSender | WhatsAppSender | SlackSender |
|------------------------------|-------------|-----------|----------------|-------------|
| **UrgentNotification** | ✅ | ✅ | ✅ | ✅ |
| **SimpleNotification** | ✅ | ✅ | ✅ | ✅ |
| **CriticalNotification** | ✅ | ✅ | ✅ | ✅ |

**12 combinaciones con solo 7 clases**

## Diferencias con otros patrones
- **vs Adapter**: Bridge planifica flexibilidad, Adapter arregla incompatibilidad
- **vs Strategy**: Bridge separa jerarquías, Strategy intercambia algoritmos