---
layout: page
title: "Mediator"
---

## Problema
Evitar que objetos se comuniquen directamente creando un lío de dependencias difícil de mantener.

## Propósito
Centraliza la comunicación entre objetos en un mediador. Los objetos no se conocen entre sí, solo conocen al mediador.

## Concepto clave
**Torre de control**: Como en un aeropuerto - los aviones no se comunican entre sí, todos hablan con la torre de control que coordina todo el tráfico.

## Casos de uso comunes
- Interfaces gráficas complejas (formularios, diálogos)
- Sistemas de chat y mensajería
- Controladores de tráfico aéreo
- Workflows y procesos de negocio
- Sistemas de notificaciones
- Coordinación entre microservicios

## Diagrama

```mermaid
classDiagram
    namespace Mediator {
        class Mediator {
            <<interface>>
            +notify(sender, event)
        }
        
        class ConcreteMediator {
            -componentA: ComponentA
            -componentB: ComponentB
            +notify(sender, event)
        }
        
        class BaseComponent {
            -mediator: Mediator
            +setMediator(mediator)
        }
        
        class ComponentA {
            +doA()
        }
        
        class ComponentB {
            +doB()
        }
    }
    
    Mediator <|.. ConcreteMediator
    BaseComponent <|-- ComponentA
    BaseComponent <|-- ComponentB
    BaseComponent --> Mediator
    ConcreteMediator --> ComponentA
    ConcreteMediator --> ComponentB
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace DialogExample {
        class DialogMediator {
            <<interface>>
            +notify(sender, event)
        }
        
        class AuthenticationDialog {
            -title: String
            -loginButton: Button
            -registerButton: Button
            -usernameField: TextBox
            -passwordField: TextBox
            -rememberCheckbox: Checkbox
            +notify(sender, event)
        }
        
        class Component {
            <<abstract>>
            -mediator: DialogMediator
            +setMediator(mediator)
            +click()
            +keyPress()
        }
        
        class Button {
            -text: String
            +click()
        }
        
        class TextBox {
            -text: String
            +keyPress()
            +getText() String
            +setText(text)
        }
        
        class Checkbox {
            -checked: boolean
            +click()
            +isChecked() boolean
        }
    }
    
    DialogMediator <|.. AuthenticationDialog
    Component <|-- Button
    Component <|-- TextBox
    Component <|-- Checkbox
    Component --> DialogMediator
    AuthenticationDialog --> Button
    AuthenticationDialog --> TextBox
    AuthenticationDialog --> Checkbox
```

## Comunicación sin mediator

```mermaid
graph TB
    A[Button] <--> B[TextBox1]
    A <--> C[TextBox2]
    A <--> D[Checkbox]
    B <--> C
    B <--> D
    C <--> D
    B <--> E[Label]
    C <--> E
    D <--> E
    
    F["Comunicación caótica<br/>Alto acoplamiento<br/>Difícil mantenimiento"]
```

## Comunicación con mediator

```mermaid
graph TB
    A[Button] --> M[Mediator]
    B[TextBox1] --> M
    C[TextBox2] --> M
    D[Checkbox] --> M
    E[Label] --> M
    
    M --> A
    M --> B
    M --> C
    M --> D
    M --> E
    
    F["Comunicación centralizada<br/>Bajo acoplamiento<br/>Fácil mantenimiento"]
```

## Flujo de comunicación

```mermaid
sequenceDiagram
    participant User
    participant Button
    participant Mediator
    participant TextBox
    participant Checkbox
    
    User->>Button: click()
    Button->>Mediator: notify(this, "click")
    
    alt Login button clicked
        Mediator->>TextBox: getText()
        TextBox-->>Mediator: username
        Mediator->>TextBox: getText()
        TextBox-->>Mediator: password
        Mediator->>Checkbox: isChecked()
        Checkbox-->>Mediator: remember
        Mediator->>Mediator: authenticate(username, password, remember)
    else Register button clicked
        Mediator->>TextBox: setText("")
        Mediator->>TextBox: setText("")
        Mediator->>Checkbox: setChecked(false)
    end
```

## Tipos de mediadores

```mermaid
graph TB
    A[Mediator Types] --> B[Simple Mediator]
    A --> C[Event-Driven Mediator]
    A --> D[State-Based Mediator]
    A --> E[Command Mediator]
    
    B --> B1["Lógica directa<br/>Métodos específicos"]
    C --> C1["Basado en eventos<br/>Pub/Sub interno"]
    D --> D1["Mantiene estado<br/>Decisiones basadas en estado"]
    E --> E1["Usa Command pattern<br/>Operaciones como objetos"]
```

## Ventajas
- **Bajo acoplamiento**: Los componentes no se conocen directamente
- **Reutilización**: Los componentes pueden reutilizarse en diferentes contextos
- **Centralización**: La lógica de interacción está centralizada
- **Flexibilidad**: Fácil cambiar las reglas de interacción

## Desventajas
- **God Object**: El mediator puede volverse muy complejo
- **Punto único de falla**: Toda la comunicación depende del mediator
- **Complejidad**: Puede ser excesivo para interacciones simples
- **Performance**: Introduce overhead en la comunicación

## Cuándo usar
- Tienes un conjunto de objetos que se comunican de forma compleja
- Las dependencias entre objetos son difíciles de entender
- Quieres reutilizar componentes en diferentes contextos
- Necesitas centralizar la lógica de coordinación

## Cuándo NO usar
- Las interacciones son simples y directas
- Solo tienes pocos objetos que interactúan
- El mediator se volvería demasiado complejo
- La comunicación directa es más eficiente

## Diferencias con otros patrones
- **vs Observer**: Mediator coordina, Observer notifica
- **vs Facade**: Mediator para comunicación, Facade para simplificación
- **vs Command**: Mediator coordina objetos, Command encapsula operaciones