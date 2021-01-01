# Getting started

Add these dependencies to your `pubspec.yaml` file:

```yaml
dependencies:
  chest: ...
  chest_flutter: ... # if you're using Flutter
```

For an in-depth introduction to Chest, just read the chapters.
To get you started, here is an example program that outputs how many times it ran:

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
