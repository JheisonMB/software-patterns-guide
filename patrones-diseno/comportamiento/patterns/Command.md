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

## Casos de uso comunes
- Sistemas de undo/redo
- Macros y scripts
- Colas de tareas y jobs
- Logging de operaciones
- Transacciones y rollbacks
- Interfaces gráficas (botones, menús)

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
        }
        
        class Invoker {
            -command: Command
            +setCommand(command)
            +executeCommand()
        }
        
        class Client {
            +createCommand()
        }
    }
    
    Command <|.. ConcreteCommand
    ConcreteCommand --> Receiver
    Invoker --> Command
    Client --> ConcreteCommand
    Client --> Receiver
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

## Flujo de ejecución

```mermaid
sequenceDiagram
    participant Client
    participant Invoker
    participant Command
    participant Receiver
    participant History
    
    Client->>Command: new CopyCommand(receiver)
    Client->>Invoker: executeCommand(command)
    Invoker->>Command: execute()
    Command->>Receiver: copy()
    Receiver-->>Command: result
    Command-->>Invoker: success
    Invoker->>History: push(command)
    
    Note over Client: Later - Undo operation
    Client->>Invoker: undo()
    Invoker->>History: pop()
    History-->>Invoker: last command
    Invoker->>Command: undo()
    Command->>Receiver: restore previous state
```

## Casos de uso prácticos

```java
// 1. Undo/Redo
CommandHistory history = new CommandHistory();
Command copyCmd = new CopyCommand(editor);
history.execute(copyCmd);  // Ejecuta y guarda
history.undo();           // Deshace la última operación

// 2. Macros (múltiples operaciones)
MacroCommand formatText = new MacroCommand()
    .add(new SelectAllCommand(editor))
    .add(new BoldCommand(editor))
    .add(new CenterAlignCommand(editor));
formatText.execute();     // Ejecuta todas las operaciones
formatText.undo();        // Deshace todas en orden inverso

// 3. Colas de trabajo
Queue<Command> jobQueue = new LinkedList<>();
jobQueue.add(new SendEmailCommand(email));
jobQueue.add(new GenerateReportCommand(data));
// Procesar más tarde...
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