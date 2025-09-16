---
layout: page
title: "Principios SOLID"
---

| Principio | Objetivo | Beneficio |
|-----------|----------|-----------|
| **SRP** | Una responsabilidad por clase | Código más cohesivo y mantenible |
| **OCP** | Abierto para extensión, cerrado para modificación | Menos riesgo al agregar funcionalidades |
| **LSP** | Subclases intercambiables con superclases | Polimorfismo seguro |
| **ISP** | Interfaces específicas y pequeñas | Menos dependencias innecesarias |
| **DIP** | Depender de abstracciones | Código más flexible y testeable |

<a id="srp"></a>

# Single Responsibility Principle (SRP)

## Principio
> "Una clase debe tener una sola razón para cambiar"

## Propósito
Buscar que cada clase tenga una única responsabilidad bien definida, evitando:
- Clases que hacen demasiadas cosas
- Alto acoplamiento entre funcionalidades
- Dificultades para testing y mantenimiento

## Diagrama

```mermaid
classDiagram
    namespace ViolacionSRP {
        class UserManager {
            <<Viola SRP>>
            +validateUser(user)
            +saveUser(user)
            +sendWelcomeEmail(user)
            +logUserCreation(user)
            +generateReport(user)
        }
    }
    
    namespace AplicacionCorrecta {
        class UserController {
            +createUser(request)
            +updateUser(id, request)
        }
        
        class UserValidator {
            +validate(user)
        }
        
        class UserRepository {
            +save(user)
            +findById(id)
        }
        
        class EmailService {
            +sendWelcomeEmail(user)
        }
        
        class AuditService {
            +logUserCreation(user)
        }
    }
    
    UserController --> UserValidator
    UserController --> UserRepository
    UserController --> EmailService
    UserController --> AuditService
```

---

<a id="ocp"></a>

# Open/Closed Principle (OCP)

## Principio
> "Las clases deben estar abiertas para extensión, cerradas para modificación"

## Propósito
Lograr que el código sea extensible sin modificar lo existente, evitando:
- Romper funcionalidad probada al agregar features
- Riesgo en despliegues por cambios en código estable
- Dificultad para agregar nuevas funcionalidades

## Diagrama

```mermaid
classDiagram
    namespace ViolacionOCP {
        class DiscountCalculator {
            <<Viola OCP>>
            +calculateDiscount(order, type)
            -calculateStudentDiscount()
            -calculateSeniorDiscount()
            -calculateVipDiscount()
        }
    }
    
    namespace AplicacionCorrecta {
        class DiscountStrategy {
            <<interface>>
            +calculateDiscount(order)
            +isApplicable(customer)
        }
        
        class StudentDiscountStrategy {
            +calculateDiscount(order)
            +isApplicable(customer)
        }
        
        class SeniorDiscountStrategy {
            +calculateDiscount(order)
            +isApplicable(customer)
        }
        
        class VipDiscountStrategy {
            +calculateDiscount(order)
            +isApplicable(customer)
        }
        
        class DiscountService {
            -strategies: List~DiscountStrategy~
            +calculateBestDiscount(order, customer)
        }
        
    }
    
    DiscountStrategy <|.. StudentDiscountStrategy
    DiscountStrategy <|.. SeniorDiscountStrategy
    DiscountStrategy <|.. VipDiscountStrategy
    DiscountService --> DiscountStrategy
```

---

<a id="lsp"></a>

# Liskov Substitution Principle (LSP)

## Principio
> "Los objetos de una superclase deben ser reemplazables por objetos de sus subclases sin alterar el funcionamiento del programa"

## Propósito
Garantizar que el polimorfismo funcione correctamente, evitando:
- Subclases que rompen el contrato de la clase padre
- Comportamientos inesperados al usar herencia
- Violaciones de las expectativas de la interfaz

## Diagrama

```mermaid
classDiagram
    namespace ViolacionLSP {
        class Bird_Wrong {
            <<Viola LSP>>
            +fly()
        }
        
        class Penguin_Wrong {
            +fly() throws Exception
        }
        
    }
    
    namespace AplicacionCorrecta {
        class Bird {
            <<abstract>>
            +move()
            +eat()
        }
        
        class FlyingBird {
            <<abstract>>
            +fly()
            +move()
        }
        
        class FlightlessBird {
            <<abstract>>
            +walk()
            +move()
        }
        
        class Eagle {
            +fly()
            +move()
        }
        
        class Penguin {
            +walk()
            +swim()
            +move()
        }
    }
    
    Bird_Wrong <|-- Penguin_Wrong
    Bird <|-- FlyingBird
    Bird <|-- FlightlessBird
    FlyingBird <|-- Eagle
    FlightlessBird <|-- Penguin
```

---

<a id="isp"></a>

# Interface Segregation Principle (ISP)

## Principio
> "Los clientes no deben depender de interfaces que no usan"

## Propósito
Crear interfaces específicas y cohesivas, evitando:
- Interfaces "gordas" con demasiados métodos
- Forzar clases a implementar métodos innecesarios
- Alto acoplamiento por interfaces demasiado amplias

## Diagrama

```mermaid
classDiagram
    namespace ViolacionISP {
        class Worker_Fat {
            <<Viola ISP>>
            +work()
            +eat()
            +sleep()
            +attendMeeting()
            +writeCode()
            +designUI()
        }
        
        class Robot_Forced {
            +work()
            +eat() throws Exception
            +sleep() throws Exception
            +attendMeeting() throws Exception
            +writeCode()
            +designUI() throws Exception
        }
        
    }
    
    namespace AplicacionCorrecta {
        class Workable {
            <<interface>>
            +work()
        }
        
        class Eatable {
            <<interface>>
            +eat()
        }
        
        class Sleepable {
            <<interface>>
            +sleep()
        }
        
        class Programmable {
            <<interface>>
            +writeCode()
        }
        
        class Developer {
            +work()
            +eat()
            +sleep()
            +writeCode()
        }
        
        class Robot {
            +work()
            +writeCode()
        }
    }
    
    Worker_Fat <|.. Robot_Forced
    Workable <|.. Developer
    Eatable <|.. Developer
    Sleepable <|.. Developer
    Programmable <|.. Developer
    Workable <|.. Robot
    Programmable <|.. Robot
```

---

<a id="dip"></a>

# Dependency Inversion Principle (DIP)

## Principio
> "Depende de abstracciones, no de concreciones"

## Propósito
Invertir las dependencias hacia abstracciones, evitando:
- Acoplamiento fuerte a implementaciones concretas
- Dificultades para testing (mocks/stubs)
- Poca flexibilidad para cambiar implementaciones

## Diagrama

```mermaid
classDiagram
    namespace ViolacionDIP {
        class OrderService_Wrong {
            <<Viola DIP>>
            -repository: MySQLOrderRepository
            -emailService: SMTPEmailService
            +createOrder(order)
        }
        
        class MySQLOrderRepository_Concrete {
            +save(order)
        }
        
        class SMTPEmailService_Concrete {
            +sendConfirmation(email)
        }
        
    }
    
    namespace AplicacionCorrecta {
        class OrderService {
            -repository: OrderRepository
            -emailService: EmailService
            +createOrder(order)
        }
        
        class OrderRepository {
            <<interface>>
            +save(order)
            +findById(id)
        }
        
        class EmailService {
            <<interface>>
            +sendConfirmation(email)
        }
        
        class MySQLOrderRepository {
            +save(order)
            +findById(id)
        }
        
        class MongoOrderRepository {
            +save(order)
            +findById(id)
        }
        
        class SMTPEmailService {
            +sendConfirmation(email)
        }
        
        class SendGridEmailService {
            +sendConfirmation(email)
        }
    }
    
    OrderService_Wrong --> MySQLOrderRepository_Concrete
    OrderService_Wrong --> SMTPEmailService_Concrete
    OrderService --> OrderRepository
    OrderService --> EmailService
    OrderRepository <|.. MySQLOrderRepository
    OrderRepository <|.. MongoOrderRepository
    EmailService <|.. SMTPEmailService
    EmailService <|.. SendGridEmailService
```

