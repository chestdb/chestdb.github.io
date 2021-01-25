# Chests

Databases managed by the Chest framework are called chests.
But instead of thinking about chests as databases and all the associated stuff like tables, indizes etc, I encourage you to think about them more as *persisted variables*.

## Create a chest

When creating a chest, you need to give it a unique name and a default value that's used if the chest doesn't exist yet.

```dart
final counter = Chest<int>('counter', ifNew: () => 0);
```

?> Please don't name your chest variable `chest`. What name would you give it if it was just a normal variable? Use that one.
If you need the distinction between the chest and the value, use `chest` as a suffix, e.g. `counterChest`.

?> It's okay to declare your chests globally.
Chests come with built-in functionality for listening to changes and it's impossible for multiple users of a chest to get references to the same interior mutable Dart object, so the conflict potential for global mutable state is very limited.
Declaring chests globally also is more true to their nature as wrappers around files – files are also globally accessible in your program.

## Open a chest

Before you can use a chest, you need to open it.

```dart
await counter.open();
```

## Access the value of a chest

Get the value using the `.value` getter:

```dart
print('This program ran ${counter.value} times.');
var actualFruit = fruit.value;
```

Set the value using the `.value` setter:

```dart
counter.value = 2;
fruit.value = Fruit(...);
```

The change is written to disk as soon as possible.

## References

Actually, the `.value` getter and setter are defined on the `Reference` type.

A `Reference` is a wrapper around an object. Similar to a `Future`, it doesn't contain the value directly (because the Dart object is not created yet).
Instead, it "promises" to you that you can get and set the value synchronously.

Each `Chest` is a `Reference` to its object – for example, the `counter` from above is a `Reference<int>`.
References to more complex objects usually have extension methods – for example, a `Reference<Fruit>` might have a `color` getter that returns a `Reference<Color>`.

These getters make the navigation inside the chest feel pretty natural, although the objects don't actually exist in memory:

```dart
fruit.color.value = Colors.red; // No Fruit object gets created.
```

Of course, you can define your own extension methods on `Reference`s to make your life easier.
For example, the `Reference<bool>` has a `toggle()` method:

```dart
settings.darkMode.toggle();
```

## Ensuring changes are applied

If you want to make sure that changes are written to disk, you can await `flush`:

```dart
await chest.flush();
```

To make sure that the data of a destructive change is actually deleted from disk, you should call `compact`:

```dart
users = Chest<Map<String, User>>('user', ifNew: () => {});
await users.open();
users.delete('Marcel');
await chest.compact();
// Now, Marcel is unrecoverably gone.
```

This is important for scenarios where you're legally required to delete information.

## Close a chest

When you're sure that you won't use a chest anymore, you can close it:

```dart
await chest.close();
```

You need to call `close` for your program to stop.
If your program is aborted externally (for example, you're writing a Flutter app that may be closed by the user), you typically don't need to bother closing chests.
