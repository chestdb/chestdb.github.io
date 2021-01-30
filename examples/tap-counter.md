# Tap Counter

In this tutorial, we'll create the typical Flutter example, but persisting the counter value.

First, create a new project.
In the `pubspec.yaml` file, add `chest_flutter` as a dependency:

```yaml
dependencies:
  chest_flutter: any
```

As with the normal counter, we'll use a `Chest<int>` to store the counter.
If the app is opened for the first time, we'll set the value to `0`.
To do that, you can add the following global variable:

```dart
final counter = Chest('counter', ifNew: () => 0);
```

To use Chest with Flutter, call `initializeChest` in the `main` method.
Also, register the `tapers.forDartCore` so that you can save and load `int`s.
Then, open the chest to be able to use it. 

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

Inside the app, you can use a `ReferenceBuilder` to rebuild part of the widget tree when the counter changes.
You can simply do `counter.value++` to increase the counter's value.

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

And that's it!
If you close your app and restart it, the old value automatically appears.
