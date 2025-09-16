---
layout: default
title: "Patrones de Comportamiento"
---

# ⚡ Patrones de Comportamiento

Los patrones de comportamiento se centran en la comunicación entre objetos y la asignación de responsabilidades, definiendo cómo los objetos interactúan y distribuyen el trabajo.

## 📊 Resumen de Patrones

| Patrón | Propósito | Caso de Uso Principal |
|--------|----------|----------------------|
| **Observer** | Notificar cambios a múltiples objetos | Eventos, notificaciones |
| **Strategy** | Intercambiar algoritmos dinámicamente | Pricing, payment processing |
| **Command** | Encapsular operaciones como objetos | Undo/Redo, queues |
| **State** | Cambiar comportamiento según estado | State machines |
| **Template Method** | Esqueleto de algoritmo | Frameworks, pipelines |
| **Chain of Responsibility** | Cadena de handlers | Middleware, validaciones |
| **Mediator** | Centralizar comunicación | UI components, workflows |
| **Memento** | Capturar y restaurar estado | Snapshots, checkpoints |
| **Iterator** | Recorrido secuencial | Navegación de colecciones |
| **Visitor** | Operaciones sobre jerarquías | AST processing |
| **Interpreter** | Interpretar expresiones | Parsers, reglas de negocio |

---

## 📝 Patrones Disponibles

### [Observer](./patterns/Observer)
**Problema**: Notificar automáticamente a múltiples objetos cuando cambia el estado de otro objeto.

**Solución**: Define una dependencia uno-a-muchos entre objetos para que los dependientes sean notificados automáticamente.

**Casos de uso**: Eventos de dominio, notificaciones, UI reactiva, pub/sub

---

### [Strategy](./patterns/Strategy)
**Problema**: Necesitas intercambiar algoritmos dinámicamente según el contexto.

**Solución**: Encapsula algoritmos en clases separadas y los hace intercambiables.

**Casos de uso**: Pricing, shipping, payment processing, algoritmos de ordenamiento

---

### [Command](./patterns/Command)
**Problema**: Encapsular operaciones como objetos para poder almacenarlas, ejecutarlas más tarde o deshacerlas.

**Solución**: Convierte requests en objetos que contienen toda la información necesaria.

**Casos de uso**: Undo/Redo, queues, logging de operaciones, macros

---

### [State](./patterns/State)
**Problema**: Un objeto debe cambiar su comportamiento cuando su estado interno cambia.

**Solución**: Encapsula estados en clases separadas y delega el comportamiento al estado actual.

**Casos de uso**: State machines, workflows, estados de conexión

---

### [Template Method](./patterns/TemplateMethod)
**Problema**: Definir el esqueleto de un algoritmo pero permitir que subclases redefinan ciertos pasos.

**Solución**: Define la estructura del algoritmo en una clase base con métodos abstractos para los pasos variables.

**Casos de uso**: Frameworks, pipelines de procesamiento, algoritmos con variaciones

---

### [Chain of Responsibility](./patterns/ChainOfResponsibility)
**Problema**: Pasar requests a través de una cadena de handlers hasta que uno lo procese.

**Solución**: Encadena objetos receptores y pasa el request a lo largo de la cadena.

**Casos de uso**: Middleware, validaciones, autorización, filtros

---

### [Mediator](./patterns/Mediator)
**Problema**: Reducir las dependencias caóticas entre objetos que se comunican directamente.

**Solución**: Centraliza la comunicación compleja entre objetos en un mediador.

**Casos de uso**: UI components, chat systems, workflows, coordinación de servicios

---

### [Memento](./patterns/Memento)
**Problema**: Capturar y restaurar el estado interno de un objeto sin violar su encapsulación.

**Solución**: Externaliza el estado en un objeto memento que puede restaurarse posteriormente.

**Casos de uso**: Undo/Redo, snapshots, checkpoints, versionado de estado

---

### [Iterator](./patterns/Iterator)
**Problema**: Acceder secuencialmente a elementos de una colección sin exponer su representación interna.

**Solución**: Proporciona una interfaz uniforme para recorrer diferentes tipos de colecciones.

**Casos de uso**: Recorrido de estructuras, paginación, navegación de datos

---

### [Visitor](./patterns/Visitor)
**Problema**: Agregar nuevas operaciones a una jerarquía de clases sin modificar las clases existentes.

**Solución**: Separa algoritmos de los objetos sobre los que operan usando double dispatch.

**Casos de uso**: AST processing, reporting, transformaciones, compiladores

---

### [Interpreter](./patterns/Interpreter)
**Problema**: Interpretar y evaluar expresiones en un lenguaje o gramática específica.

**Solución**: Define una representación para la gramática y un intérprete que evalúa expresiones.

**Casos de uso**: Parsers, calculadoras, reglas de negocio, DSLs

---

## 🎯 Guía de Selección

| Patrón | Cuándo usar | Evitar cuando |
|--------|-------------|---------------|
| **Observer** | Notificaciones 1-a-muchos | Comunicación directa suficiente |
| **Strategy** | Múltiples algoritmos | Un solo algoritmo |
| **Command** | Operaciones como objetos | Operaciones simples |
| **State** | Comportamiento según estado | Estados simples |
| **Template Method** | Algoritmos con pasos variables | Algoritmos completamente diferentes |
| **Chain of Responsibility** | Múltiples handlers posibles | Un solo handler |
| **Mediator** | Comunicación compleja entre objetos | Comunicación simple |
| **Memento** | Necesitas undo/redo | Estados simples |
| **Iterator** | Recorrido de colecciones | Acceso directo suficiente |
| **Visitor** | Operaciones sobre jerarquías | Jerarquías estables |
| **Interpreter** | Lenguajes o expresiones complejas | Lógica simple |

---

## 📚 Navegación

[← Volver a Patrones de Diseño](../) | [← Volver al inicio]({{ '/' | relative_url }})