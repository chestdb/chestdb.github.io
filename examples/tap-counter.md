# Tap Counter

In this tutorial, we'll create the typical Flutter example, but persisting the counter value.
As with the normal counter, we'll use a `Chest<int>` to store the counter.

First, create a new project.
In the `pubspec.yaml` file, add `chest` and `chest_flutter` as dependencies:

```yaml
dependencies:
  chest: any
  chest_flutter: any
```

Add the following global variable:

```dart
final counter = Chest('counter', ifNew: () => 0);
```

To use Chest with Flutter, call `initializeChest` in the `main` method.
Also, open the chest to be able to use it. Also, register the `tapers.forDartCore` so that you can save and load `int`s:

```dart
void main() async {
  await initializeChest();
  tape.register({
    ...tapers.forDartCore,
  });
  await counter.open();
  runApp(TapCounterApp());
}
```

The `TapCounterApp` looks as follows:

```dart
class TapCounterApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Tap Counter',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: Scaffold(
        appBar: AppBar(title: Text('Tap Counter')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Text('You have pushed the button this many times:'),
              ReferenceBuilder(
                reference: counter,
                builder: (_) {
                  return Text(
                    '${counter.value}',
                    style: Theme.of(context).textTheme.headline4,
                  );
                },
              ),
            ],
          ),
        ),
        floatingActionButton: FloatingActionButton(
          onPressed: () => counter.value++,
          tooltip: 'Increment',
          child: Icon(Icons.add),
        ),
      ),
    );
  }
}
```

Note that the `FloatingActionButton`'s `onPressed` simply increases the counter.
The counter text itself is wrapped in a `ReferenceBuilder`, which rebuilds everytime the value of the counter reference changes.

And that's it!
If you close your app and restart it, the old value automatically appears.
