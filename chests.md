# Chests

Each chest is like a persisted variable.

## Open a chest

When creating a chest, you need to give it a unique name and a default value that's used if the chest doesn't exist yet.
Before you can use a chest, you need to open it.

```dart
final chest = Chest<int>('counter', ifNew: () => 0);
await chest.open();
```

## Access the value of a chest

Get the value using the `.value` getter:

```dart
print('This program ran ${counter.value} times.');
```

You can set the value using the `.value` setter:

```dart
chest.value = 2;
chest.value--;
```

The change is written to disk as soon as possible.

## Access part of a chest

A chest is also a reference to its value – in the above case, a `Reference<int>`.
Some `Reference`s have extensions methods.
For example, references to complex classes usually have extension methods for fields:

```dart
user.pet.value = Pet('dog');
```

Another example is `Reference<bool>`, which has a `.toggle()` extension method:

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
