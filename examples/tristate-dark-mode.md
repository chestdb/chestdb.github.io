# Tristate Dark Mode

In this tutorial, we'll create an app that lets you manually toggle between system, light and dark mode, persisting the setting over app restarts.

First, create a new project.
In the `pubspec.yaml` file, add `chest_flutter` as a dependency:

```yaml
dependencies:
  chest_flutter: any
```

We'll use a global `Chest<ThemeMode>` to store which mode the user chose.
If the app is started for the first time, we'll default to `ThemeMode.system`.

```dart
final themeMode = Chest('themeMode', ifNew: () => ThemeMode.system);
```

To use Chest with Flutter, call `initializeChest` in the `main` method.
To be able to store `ThemeMode`s in the chest, we need to register the `tapers.forFlutter`.
Then, we can open the chest.

```dart
void main() async {
  await initializeChest();
  tape.register({
    ...tapers.forFlutter,
  });
  await themeMode.open();
  runApp(TristateDarkThemeApp());
}
```

We can wrap the whole app inside a `ReferenceBuilder` to update it whenever the theme mode setting changes.
For the theme, we create a `DropdownButton`
For the `Switch` value and for deciding whether to use dark or light mode, we use `isDark.value`.
When pressed, the `Switch` can simply call `isDark.toggle()`, a method defined on `Reference<bool>`.

```dart
class TristateDarkThemeApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ReferenceBuilder(
      reference: themeMode,
      builder: (_) {
        return MaterialApp(
          themeMode: themeMode.value,
          darkTheme: ThemeData.dark(),
          home: Scaffold(
            body: Center(
              child: DropdownButton<ThemeMode>(
                value: themeMode.value,
                elevation: 16,
                underline: Container(
                  height: 2,
                  color: Colors.deepPurpleAccent,
                ),
                onChanged: (newValue) => themeMode.value = newValue,
                items: ThemeMode.values.map((ThemeMode mode) {
                  return DropdownMenuItem<ThemeMode>(
                    value: mode,
                    child: Text('$mode'.substring('$mode'.indexOf('.') + 1)),
                  );
                }).toList(),
              ),
            ),
          ),
        );
      },
    );
  }
}
```
