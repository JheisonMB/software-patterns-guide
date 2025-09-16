---
layout: default
title: "Patrones Creacionales"
---

# 🏗️ Patrones Creacionales

Los patrones creacionales se encargan de la creación de objetos, proporcionando mecanismos que aumentan la flexibilidad y reutilización del código.

## 📊 Resumen de Patrones

| Patrón | Propósito | Caso de Uso Principal |
|--------|----------|----------------------|
| **Singleton** | Una única instancia | Configuración global, Logger |
| **Factory Method** | Crear objetos sin especificar clase | Crear objetos según parámetros |
| **Abstract Factory** | Crear familias de objetos | Temas UI, Drivers de BD |
| **Builder** | Construir objetos complejos | Objetos con muchos parámetros |
| **Prototype** | Clonar objetos existentes | Copiar objetos costosos |

---

## 📝 Patrones Disponibles

### [Singleton](./patterns/Singleton)
**Problema**: Garantizar una única instancia de una clase en toda la aplicación.

**Solución**: Controla la creación de instancias y proporciona acceso global.

**Casos de uso**: Loggers, configuraciones, conexiones DB, cache

---

### [Factory Method](./patterns/FactoryMethod)
**Problema**: Crear objetos sin especificar su clase exacta hasta tiempo de ejecución.

**Solución**: Delega la creación de objetos a subclases o métodos especializados.

**Casos de uso**: Procesadores de pago, servicios de notificación, parsers

---

### [Abstract Factory](./patterns/AbstractFactory)
**Problema**: Crear familias de objetos relacionados que deben ser compatibles entre sí.

**Solución**: Proporciona interfaces para crear familias completas de productos.

**Casos de uso**: UI components, drivers de base de datos, temas visuales

---

### [Builder](./patterns/Builder)
**Problema**: Construir objetos complejos con muchos parámetros opcionales.

**Solución**: Construye objetos paso a paso usando una interfaz fluida.

**Casos de uso**: Emails, configuraciones, objetos inmutables, consultas SQL

---

### [Prototype](./patterns/Prototype)
**Problema**: Crear objetos clonando instancias existentes cuando la creación es costosa.

**Solución**: Clona objetos existentes en lugar de crear desde cero.

**Casos de uso**: Configuraciones de juegos, templates de documentos, objetos complejos

---

## 🎯 Guía de Selección

| Patrón | Cuándo usar | Evitar cuando |
|--------|-------------|---------------|
| **Singleton** | Recursos únicos (DB, Logger) | Testing, DI disponible |
| **Factory Method** | Múltiples implementaciones | Pocas variaciones |
| **Abstract Factory** | Familias de productos | Productos independientes |
| **Builder** | Objetos complejos/inmutables | Objetos simples |
| **Prototype** | Clonación costosa | Objetos simples |

---

## 📚 Navegación

[← Volver a Patrones de Diseño](../) | [← Volver al inicio]({{ '/' | relative_url }})
