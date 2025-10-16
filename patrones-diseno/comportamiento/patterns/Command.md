---
layout: page
title: "Command"
---

## Problema
Convertir operaciones en objetos para poder almacenarlas, pasarlas como parámetros, ejecutarlas más tarde o deshacerlas.

## Propósito
Encapsula una operación completa (acción + parámetros + receptor) en un objeto. Esto permite tratar las operaciones como objetos de primera clase: guardarlas, pasarlas, ejecutarlas cuando sea necesario y revertirlas.

## Concepto clave
**Operación como objeto**: En lugar de llamar directamente `editor.copy()`, creas un objeto `CopyCommand` que sabe cómo ejecutar y deshacer esa operación.

**Ejemplo simple**: En lugar de `suma = a + b`, creas un objeto `SumarCommand(a, b)` que:
- Sabe cómo hacer la suma
- Puede ejecutarse más tarde
- Puede deshacerse (restando)
- Puede guardarse en una lista de operaciones

## Casos de uso comunes
- Sistemas de undo/redo
- Macros y scripts
- Colas de tareas y jobs
- Logging de operaciones
- Transacciones y rollbacks
- Interfaces gráficas (botones, menús)

## ¿Quién es quién en Command?

| Actor | Lo que realmente es | Ejemplo | Analogía |
|-------|--------------------|---------|-----------|
| **Command** | Interfaz que define `execute()` y `undo()` | `Command` - operaciones que se pueden deshacer | "Señal infrarroja" (mensaje) |
| **ConcreteCommand** | Sabe QUÉ hacer y CÓMO deshacerlo | `CopyCommand`, `PasteCommand` | Comando específico del control |
| **Receiver** | El que REALMENTE hace el trabajo | `TextEditor` - tiene la lógica real | Televisor (hace el trabajo real) |
| **Invoker** | El que ejecuta comandos | `EditorInvoker` - botones, menús | Control remoto (botón) |

## Diagrama

```mermaid
classDiagram
    namespace CommandPattern {
        class Command {
            <<interface>>
            +execute()
            +undo()
        }
        
        class ConcreteCommand {
            -receiver: Receiver
            -parameters
            +execute()
            +undo()
        }
        
        class Receiver {
            +action()
            note "Hace el trabajo REAL"
        }
        
        class Invoker {
            -command: Command
            +setCommand(command)
            +executeCommand()
            note "Botón, menú, etc."
        }
        
        class Client {
            +createCommand()
            note "Conecta todo"
        }
    }
    
    Command <|.. ConcreteCommand
    ConcreteCommand --> Receiver : "delega a"
    Invoker --> Command : "ejecuta"
    Client --> ConcreteCommand : "crea"
    Client --> Receiver : "conoce"
```

## Ejemplo práctico

```mermaid
classDiagram
    namespace TextEditorExample {
        class Command {
            <<interface>>
            +execute()
            +undo()
        }
        
        class CopyCommand {
            -editor: TextEditor
            -backup: String
            +execute()
            +undo()
        }
        
        class PasteCommand {
            -editor: TextEditor
            -backup: String
            +execute()
            +undo()
        }
        
        class CutCommand {
            -editor: TextEditor
            -backup: String
            +execute()
            +undo()
        }
        
        class TextEditor {
            -content: String
            -clipboard: String
            +copy()
            +paste()
            +cut()
            +getContent() String
            +setContent(text)
        }
        
        class CommandHistory {
            -history: Stack~Command~
            +push(command)
            +pop() Command
            +isEmpty() boolean
        }
        
        class EditorInvoker {
            -history: CommandHistory
            +executeCommand(command)
            +undo()
        }
    }
    
    Command <|.. CopyCommand
    Command <|.. PasteCommand
    Command <|.. CutCommand
    CopyCommand --> TextEditor
    PasteCommand --> TextEditor
    CutCommand --> TextEditor
    EditorInvoker --> CommandHistory
    CommandHistory --> Command
```

## Flujo paso a paso

**Ejemplo concreto**: Usuario hace clic en "Copiar" en un editor

1. **Client** crea: `CopyCommand(textEditor)` 
2. **Client** le dice al **Invoker** (botón): "ejecuta este comando"
3. **Invoker** llama: `command.execute()`
4. **Command** llama: `textEditor.copy()` (delega al Receiver)
5. **Receiver** (TextEditor) hace el trabajo real
6. **Invoker** guarda el comando en historial para undo

```mermaid
sequenceDiagram
    participant Client
    participant Invoker
    participant Command
    participant Receiver
    participant History
    
    Note over Client,Receiver: 1. Preparación
    Client->>Command: new CopyCommand(textEditor)
    Client->>Invoker: executeCommand(command)
    
    Note over Invoker,Receiver: 2. Ejecución
    Invoker->>Command: execute()
    Command->>Receiver: copy() - hace trabajo real
    Receiver-->>Command: result
    Command-->>Invoker: success
    Invoker->>History: push(command) - para undo
    
    Note over Client,Receiver: 3. Undo (más tarde)
    Client->>Invoker: undo()
    Invoker->>History: pop() - último comando
    History-->>Invoker: CopyCommand
    Invoker->>Command: undo()
    Command->>Receiver: restore() - deshace
```

## Ventajas
- **Desacoplamiento**: Separa el objeto que invoca la operación del que la ejecuta
- **Flexibilidad**: Permite parametrizar objetos con operaciones
- **Undo/Redo**: Soporte natural para operaciones reversibles
- **Logging**: Fácil registrar y auditar operaciones
- **Macros**: Combinar múltiples comandos en uno compuesto

## Desventajas
- **Complejidad**: Introduce muchas clases pequeñas
- **Memoria**: Mantener historial puede consumir memoria
- **Overhead**: Puede ser excesivo para operaciones simples
- **Estado**: Manejar estado para undo puede ser complejo

## Cuándo usar
- Necesitas undo/redo en tu aplicación
- Quieres crear macros o secuencias de operaciones
- Necesitas hacer cola de operaciones para ejecutar más tarde
- Quieres registrar/auditar todas las operaciones
- Necesitas transacciones que se puedan revertir

## Cuándo NO usar
- Las operaciones son simples y no necesitas deshacerlas
- No hay beneficio en diferir la ejecución
- La aplicación no requiere historial de operaciones
- La complejidad adicional no se justifica