---
layout: default
title: Command System
parent: Systems
nav_order: 5
---

# Command System

The Command System allows plugins to register custom commands that players or the console can execute.

## Key Classes
*   **`CommandManager`**: The internal system that executes commands.
*   **`CommandRegistry`**: The API exposed to plugins for registering commands.
*   **`AbstractCommand`**: The base class for all commands.
*   **`CommandSender`**: The interface representing the issuer (Player, Console, Block).

## Creating a Command

To create a command, extend `com.hypixel.hytale.server.core.command.system.AbstractCommand`.

```java
public class MyCommand extends AbstractCommand {
    public MyCommand() {
        super("mycommand", "Description of what it does");
        addAliases("mc", "myc");
        // Permissions are automatically generated: pluginName.command.mycommand
    }

    @Override
    public CompletableFuture<Void> execute(CommandContext context) {
        CommandSender sender = context.getSender();
        sender.sendMessage("Hello from MyCommand!");
        return CompletableFuture.completedFuture(null);
    }
}
```

## Registering a Command

Register your command in your plugin's `setup()` method.

```java
@Override
public void setup() {
    getCommandRegistry().registerCommand(new MyCommand());
}
```

## Subcommands
You can add subcommands using `addSubCommand()` in your command's constructor.

```java
public MyCommand() {
    super("parent", "Parent command");
    addSubCommand(new ChildCommand());
}
```

## Permissions
Permissions are automatically checked before `execute` is called.
You can check permissions manually using `sender.hasPermission("my.perm")`.
