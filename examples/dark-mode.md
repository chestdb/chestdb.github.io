# Dark mode

```dart
import 'package:flutter/material.dart';
import 'package:chest_flutter/chest_flutter.dart';

final isDark = Chest<bool>('isDark', ifNew: () => false);

void main() async {
  await isDark.open();
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ReferenceBuilder(
      reference: isDark,
      builder: (context, _) {
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
