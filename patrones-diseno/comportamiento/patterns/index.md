---
layout: default
title: "Patrones de Comportamiento"
---

# Patrones de Comportamiento

Los patrones de comportamiento se centran en la comunicación entre objetos y la asignación de responsabilidades entre ellos.

## Patrones Disponibles

### [Observer](Observer)
Define una dependencia uno-a-muchos entre objetos para que cuando un objeto cambie de estado, todos sus dependientes sean notificados.

### [Strategy](Strategy)
Encapsula algoritmos intercambiables y los hace intercambiables en tiempo de ejecución.

### [Command](Command)
Encapsula una solicitud como un objeto, permitiendo parametrizar clientes con diferentes solicitudes.

### [State](State)
Permite que un objeto altere su comportamiento cuando su estado interno cambia.

### [Template Method](TemplateMethod)
Define el esqueleto de un algoritmo en una clase base, permitiendo que las subclases redefinan ciertos pasos.

### [Chain of Responsibility](ChainOfResponsibility)
Pasa solicitudes a lo largo de una cadena de manejadores hasta que uno de ellos la procese.

### [Mediator](Mediator)
Define cómo un conjunto de objetos interactúan entre sí, promoviendo el acoplamiento débil.

### [Memento](Memento)
Captura y externaliza el estado interno de un objeto para poder restaurarlo posteriormente.

### [Iterator](Iterator)
Proporciona una forma de acceder secuencialmente a los elementos de una colección sin exponer su representación interna.

### [Visitor](Visitor)
Define una nueva operación sin cambiar las clases de los elementos sobre los que opera.

### [Interpreter](Interpreter)
Define una representación para la gramática de un lenguaje y un intérprete que usa esa representación.

---

[← Volver a Patrones de Comportamiento](../) | [← Volver a Patrones de Diseño](../../) | [← Volver al inicio]({{ '/' | relative_url }})