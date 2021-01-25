# Counter

In this tutorial, we'll write a program that counts how many times it ran.
We'll use a `Chest<int>` to store the counter.

First, create a new project.
In the `pubspec.yaml` file, add `chest` as a dependency:

```yaml
dependencies:
  chest: any
```

Add the following global variable:

```dart
final counter = Chest('counter', ifNew: () => 0);
```

Because the `ifNew` function returns an `int`, Dart will automatically infer that `counter` is a `Chest<int>`.

To actually be able to put an `int` into a chest and get it out again, we'll need to register a taper for the `int` type.
Chest already comes with a handful of builtin tapers, so you can add the following at the beginning of your `main` method:

```dart
tape.register({
  ...tapers.forDartCore, // contains a taper for int
});
```

To use the chest, we'll need to open it. So, let's do that next:

```dart
await counter.open();
```

You'll need to make the `main` function async to be able to use `await`:

```dart
void main() async {
  ...
}
```

Now that the chest is opened, we can access its value:

```dart
print('This program ran ${counter.value} times.');
```

If you want to handle singular and plural correctly, you can instead use this snippet:

```dart
final count = counter.value;
print('This program ran $count ${count == 1 ? 'time' : 'times'}.');
```

Once we printed the counter, it's time to increase it:

```dart
counter.value++;
```

Finally, close the chest so that the chest file gets closed and the program can stop:

```dart
await counter.close();
```

And that's it!

If you run the program multiple times, you'll see that its output is different each time:

```dart
This program ran 0 times.
This program ran 1 time.
This program ran 2 times.
This program ran 3 times.
...
```

You can find the complete source code at TODO.
