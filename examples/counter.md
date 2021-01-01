# Counter

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
