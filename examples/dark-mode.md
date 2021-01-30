# Dark Mode

In this tutorial, we'll create an app that lets you manually toggle between light and dark mode, persisting the setting over app restarts.

First, create a new project.
In the `pubspec.yaml` file, add `chest_flutter` as a dependency:

```yaml
dependencies:
  chest_flutter: any
```

We'll use a global `Chest<bool>` to store whether dark mode is enabled.
If the app is started for the first time, we'll default to light mode by setting the value of the `isDark` chest to `false`.

```dart
final isDark = Chest('isDark', ifNew: () => false);
```

To use Chest with Flutter, call `initializeChest` in the `main` method.
To be able to store `bool`s in the chest, we need to register the `tapers.forDartCore`.
Then, we can open the chest.

```dart
void main() async {
  await initializeChest();
  tape.register({
    ...tapers.forDartCore,
  });
  await isDark.open();
  runApp(TapCounterApp());
}
```

We can wrap the whole app inside a `ReferenceBuilder` to update it whenever the dark mode setting changes.
For the `Switch` value and for deciding whether to use dark or light mode, we use `isDark.value`.
When pressed, the `Switch` can simply call `isDark.toggle()`, a method defined on `Reference<bool>`.

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ReferenceBuilder(
      reference: isDark,
      builder: (_) {
        return MaterialApp(
          themeMode: isDark.value ? ThemeMode.dark : ThemeMode.light,
          darkTheme: ThemeData.dark(),
          home: Scaffold(
            body: Center(
              child: Switch(
                value: isDark.value,
                onChanged: (_) => isDark.toggle(),
              ),
            ),
          ),
        );
      },
    );
  }
}
```
