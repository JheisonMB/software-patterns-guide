---
layout: default
title: "Patrones Estructurales"
---

# 🔧 Patrones Estructurales

Los patrones estructurales se ocupan de cómo se componen las clases y objetos para formar estructuras más grandes y flexibles.

## 📊 Resumen de Patrones

| Patrón | Propósito | Caso de Uso Principal |
|--------|----------|----------------------|
| **Adapter** | Hacer compatibles interfaces | Integración con APIs legacy |
| **Bridge** | Separar abstracción de implementación | Drivers multiplataforma |
| **Composite** | Tratar objetos y grupos uniformemente | Árboles de archivos |
| **Decorator** | Añadir funcionalidad dinámicamente | Middleware, validadores |
| **Facade** | Simplificar interfaces complejas | APIs unificadas |
| **Flyweight** | Minimizar uso de memoria | Editores de texto |
| **Proxy** | Controlar acceso a objetos | Lazy loading, cache |

---

## 📝 Patrones Disponibles

### [Adapter](./patterns/Adapter)
**Problema**: Hacer que dos interfaces incompatibles trabajen juntas.

**Solución**: Crea una clase intermedia que traduce las llamadas entre interfaces.

**Casos de uso**: Integración con APIs legacy, bibliotecas de terceros, sistemas externos

---

### [Bridge](./patterns/Bridge)
**Problema**: Separar una abstracción de su implementación para que ambas puedan variar independientemente.

**Solución**: Usa composición en lugar de herencia para conectar abstracción e implementación.

**Casos de uso**: Drivers de base de datos, sistemas multiplataforma, interfaces gráficas

---

### [Composite](./patterns/Composite)
**Problema**: Tratar objetos individuales y composiciones de objetos de manera uniforme.

**Solución**: Organiza objetos en estructuras de árbol donde hojas y nodos implementan la misma interfaz.

**Casos de uso**: Árboles de archivos, menús anidados, estructuras organizacionales

---

### [Decorator](./patterns/Decorator)
**Problema**: Añadir funcionalidad a objetos dinámicamente sin alterar su estructura.

**Solución**: Envuelve objetos en decoradores que añaden comportamiento adicional.

**Casos de uso**: Middleware, validadores, loggers, filtros de procesamiento

---

### [Facade](./patterns/Facade)
**Problema**: Simplificar el acceso a un subsistema complejo.

**Solución**: Proporciona una interfaz unificada y simple para un conjunto de interfaces complejas.

**Casos de uso**: APIs unificadas, servicios de alto nivel, wrappers de bibliotecas

---

### [Flyweight](./patterns/Flyweight)
**Problema**: Minimizar el uso de memoria cuando se necesitan muchos objetos similares.

**Solución**: Comparte eficientemente datos comunes entre múltiples objetos similares.

**Casos de uso**: Editores de texto, juegos con partículas, sistemas de renderizado

---

### [Proxy](./patterns/Proxy)
**Problema**: Controlar el acceso a otro objeto proporcionando un sustituto.

**Solución**: Crea un objeto proxy que controla el acceso al objeto real.

**Casos de uso**: Lazy loading, cache, seguridad, logging de accesos

---

## 🎯 Guía de Selección

| Patrón | Cuándo usar | Evitar cuando |
|--------|-------------|---------------|
| **Adapter** | Integrar sistemas incompatibles | Interfaces ya compatibles |
| **Bridge** | Abstracción e implementación independientes | Abstracción simple |
| **Composite** | Estructuras jerárquicas | Estructuras planas |
| **Decorator** | Agregar funcionalidad dinámicamente | Funcionalidad estática |
| **Facade** | Simplificar subsistemas complejos | Sistemas ya simples |
| **Flyweight** | Muchos objetos similares | Pocos objetos |
| **Proxy** | Control de acceso/lazy loading | Acceso directo suficiente |

---

## 📚 Navegación

[← Volver a Patrones de Diseño](../) | [← Volver al inicio]({{ '/' | relative_url }})