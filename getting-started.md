# Getting started

## Add dependencies

Add these dependencies to your `pubspec.yaml` file:

```yaml
dependencies:
  chest: ...
  chest_flutter: ... # if you're using Flutter

dev_dependencies:
  tapegen: ...
  build_runner: ...
```

## Register tapers

Chest uses its own encoding called *tape*.

To store an object of some type in a Chest, a tapers needs to be registered for that type.
For most types from `dart:core`, there are already pre-defined tapers available.

Adding this to the start of your `main` method enables you to store those basic types:

```dart
tape.register({
  ...tapers.forDartCore,
});
```

Each taper is registered under a *type code*. As you add tapers for more types, the register call will soon look something like this:

```dart
tape.register({
  ...tapers.forDartCore,
  ...tapers.forTuple,
  0: taper.forUser(),
  1: taper.forList<User>(),
  2: legacyTaper.forPet().v1,
  3: taper.forPet(),
});
```

## Open a chest

When creating a chest, you need to give it a unique name and a default value that's used if the chest doesn't exist yet.
Before you can use a box, you need to open it.

```dart
var chest = Chest<int>('counter', ifNew: () => 0);
await chest.open();
```

## Access the value of a chest

Each chest is like a persisted variable. Get the value using the `.value` getter:

```dart
print('This program ran ${counter.value} times.');
```

You can set the value using the `.value` setter:

```dart
chest.value++;
```

The change is written to disk as soon as possible.

If you want to make sure that a write operation is successful, you can await `flush`:

```dart
await chest.flush();
```

## Close a chest

When you're sure that you won't use a chest anymore, you can close it:

```dart
await chest.close();
```

You need to call `close` for your program to stop.
If your program is aborted externally (for example, you're writing a Flutter app that may be closed by the user), you typically don't need to bother closing chests.

## Full example

```dart
void main() async {
  tape.register({
    ...tapers.forDartCore,
  });
  final counter = Chest('counter', ifNew: () => 0);
  await counter.open();
  print('This program ran ${counter.value} times.');
  counter.value++;
  await counter.close();
}
```
