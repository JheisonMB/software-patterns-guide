---
layout: page
title: "Interpreter"
---

## Problema
Permitir que usuarios configuren reglas o fórmulas complejas sin programar.

## Propósito
Convierte expresiones de texto (como "age >= 18 AND income > 30000") en objetos que pueden evaluarse dinámicamente. Crea un "mini-lenguaje" para que usuarios configuren reglas de negocio.

## Concepto clave
**Árbol de expresión**: Cada parte de la expresión (números, operadores, variables) se convierte en un objeto que sabe cómo evaluarse a sí mismo.

## Casos de uso comunes
- Calculadoras con expresiones matemáticas
- Motores de reglas de negocio
- Lenguajes de consulta simples
- Parsers de configuración
- Validadores de expresiones regulares
- Intérpretes de comandos

## ¿Quién es quién en Interpreter?

| Actor | Lo que realmente es | Ejemplo | Analogía |
|-------|--------------------|---------|-----------|
| **AbstractExpression** | Interfaz que define `interpret(context)` | `Expression` - define cómo evaluarse | "Elemento de fórmula" (interfaz) |
| **TerminalExpression** | Elementos básicos (hojas del árbol) | `NumberExpression`, `VariableExpression` | Números en pantalla (3, 5, x) |
| **NonTerminalExpression** | Operadores (nodos del árbol) | `AddExpression`, `MultiplyExpression` | Botones de operación (+, -, *, /) |
| **Context** | Almacena variables y valores | `Context` con variables x=3, y=5 | Calculadora con memoria |
| **Client** | Construye y evalúa el árbol | Parser + evaluador | Persona usando calculadora |

## Diagrama

```mermaid
classDiagram
    namespace InterpreterPattern {
        class AbstractExpression {
            <<interface>>
            +interpret(context) Result
        }
        
        class TerminalExpression {
            +interpret(context) Result
        }
        
        class NonTerminalExpression {
            -expression1: AbstractExpression
            -expression2: AbstractExpression
            +interpret(context) Result
        }
        
        class Context {
            -variables: Map~String, Value~
            +getValue(variable) Value
            +setValue(variable, value)
        }
        
        class Client {
            +buildSyntaxTree() AbstractExpression
            +interpret()
        }
    }
    
    AbstractExpression <|.. TerminalExpression
    AbstractExpression <|.. NonTerminalExpression
    NonTerminalExpression --> AbstractExpression
    Client --> AbstractExpression
    Client --> Context
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace CalculatorExample {
        class Expression {
            <<interface>>
            +interpret(context) double
        }
        
        class NumberExpression {
            -number: double
            +interpret(context) double
        }
        
        class VariableExpression {
            -variable: String
            +interpret(context) double
        }
        
        class AddExpression {
            -left: Expression
            -right: Expression
            +interpret(context) double
        }
        
        class SubtractExpression {
            -left: Expression
            -right: Expression
            +interpret(context) double
        }
        
        class MultiplyExpression {
            -left: Expression
            -right: Expression
            +interpret(context) double
        }
        
        class Context {
            -variables: Map~String, Double~
            +getValue(variable) Double
            +setValue(variable, value)
        }
        
        class ExpressionParser {
            +parse(expression) Expression
        }
    }
    
    Expression <|.. NumberExpression
    Expression <|.. VariableExpression
    Expression <|.. AddExpression
    Expression <|.. SubtractExpression
    Expression <|.. MultiplyExpression
    AddExpression --> Expression
    SubtractExpression --> Expression
    MultiplyExpression --> Expression
    ExpressionParser --> Expression
```

## Árbol de sintaxis

```mermaid
graph TB
    A["+ (AddExpression)"] --> B["* (MultiplyExpression)"]
    A --> C["5 (NumberExpression)"]
    
    B --> D["x (VariableExpression)"]
    B --> E["2 (NumberExpression)"]
    
    subgraph "Expression: x * 2 + 5"
        F["Context: x = 3"]
    end
    
    subgraph "Evaluation"
        G["x * 2 = 3 * 2 = 6"]
        H["6 + 5 = 11"]
    end
```

## Flujo de interpretación

```mermaid
sequenceDiagram
    participant Client
    participant Parser
    participant AddExpr as AddExpression
    participant MultExpr as MultiplyExpression
    participant VarExpr as VariableExpression
    participant NumExpr as NumberExpression
    participant Context
    
    Client->>Parser: parse("x * 2 + 5")
    Parser->>AddExpr: new AddExpression(mult, num5)
    Parser->>MultExpr: new MultiplyExpression(varX, num2)
    Parser-->>Client: expression tree
    
    Client->>Context: setValue("x", 3)
    Client->>AddExpr: interpret(context)
    AddExpr->>MultExpr: interpret(context)
    MultExpr->>VarExpr: interpret(context)
    VarExpr->>Context: getValue("x")
    Context-->>VarExpr: 3
    VarExpr-->>MultExpr: 3
    MultExpr->>NumExpr: interpret(context)
    NumExpr-->>MultExpr: 2
    MultExpr-->>AddExpr: 6
    AddExpr->>NumExpr: interpret(context)
    NumExpr-->>AddExpr: 5
    AddExpr-->>Client: 11
```

## Casos de uso prácticos

```java
// 1. Calculadora dinámica
Expression expr = parser.parse("x * 2 + 5");
Context context = new Context();
context.setValue("x", 3);
double result = expr.interpret(context); // = 11

// 2. Reglas de negocio configurables
Expression rule = parser.parse("age >= 18 AND income > 30000");
Context customer = new Context();
customer.setValue("age", 25);
customer.setValue("income", 45000);
boolean approved = rule.interpret(customer); // = true

// 3. Fórmulas de precios dinámicas
Expression pricing = parser.parse("basePrice * (1 + taxRate) - discount");
Context order = new Context();
order.setValue("basePrice", 100);
order.setValue("taxRate", 0.15);
order.setValue("discount", 10);
double finalPrice = pricing.interpret(order); // = 105
```

## Ventajas
- **Extensibilidad**: Fácil agregar nuevas reglas gramaticales
- **Flexibilidad**: Permite cambiar y extender el lenguaje
- **Reutilización**: Las expresiones pueden reutilizarse
- **Separación**: Separa gramática de interpretación

## Desventajas
- **Complejidad**: Muchas clases para gramáticas complejas
- **Performance**: Puede ser lento para expresiones complejas
- **Mantenimiento**: Difícil mantener gramáticas grandes
- **Memoria**: Consume memoria con árboles de sintaxis grandes

## Cuándo usar
- Necesitas que usuarios configuren reglas sin programar
- Tienes fórmulas o expresiones que cambian frecuentemente
- Quieres crear un DSL (Domain Specific Language) simple
- Las reglas de negocio son complejas pero bien definidas

## Cuándo NO usar
- Las expresiones son simples (usa Strategy)
- La performance es crítica
- El lenguaje es muy complejo (usa un parser real)
- Las reglas no cambian frecuentemente