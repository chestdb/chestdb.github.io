# Tapers

Tapers are used to serialize objects.

They are usually defined as extensions on `TaperNamespace`, a class which has the `taper` instance.
So, to use tapers, just write `taper.forMyType()`.

<!-- ## Generating tapers automatically

Most of the time, your classes are simple enough that you can generate tapers automatically.

Just annotate your class with `@tape` and add a `part` directive:

```dart
part 'my_file.g.dart';

@tape
class Fruit {
  final String name;
  final Color color;
}
```

When you run `dart pub run build_runner build`, Chest will automatically generate a taper and make it available at `taper.forFruit()`.

The generated tapers uses the field names as keys, so if you rename a field, you'll need to migrate to the new taper.
To counter this, add `TapeKey` annotations to all fields:

```dart
@tape
class Fruit {
  @TapeKey(0) final String name;
  @TapeKey(1) final Color color;
}
```

The keys can be anything, but numbers are pretty small to encode. -->

## Writing tapers

When writing tapers for a type, you can choose from one of two options:

* serialize objects into bytes
* serialize objects into maps of serializable objects

For serializing an object into bytes, add a `byteTaper`:

```dart
extension TaperForBool on TaperNamespace {
  Taper<Bool> forBool() => bytesTaper(
    toBytes: (bool value) => [value ? 1 : 0],
    fromBytes: (Uint8List bytes) => bytes.single != 0,
  );
}
```

For serializing an object into a `Map`, add a `mapTaper`:

```dart
class TaperForFruit on TaperNamespace {
  Taper<Fruit> forFruit() => mapTaper(
    toMap: (Fruit fruit) {
      return {
        'name': fruit.name,
        'color': fruit.color,
      };
    },
    fromMap: (Map<Object?, Object?> map) {
      return Fruit(
        name: map['name'] as String,
        color: map['color'] as Color,
      );
    },
  );
}
```

Then, you can register the tapers:

```dart
void main() {
  tape.register({
    // ...
    2: taper.forBool(),
    3: taper.forUser(),
  });
}
```

## Publishing tapers for a package

If you use a package and want to make the tapers you write available to everyone, follow these steps:

1. Create a new package named `<original package>_tapers`.
   For example, if you're writing tapers for types from the `abc` package, name your package `abc_tapers`.
2. Write tapers, as shown above.
3. Reserve type codes in [the table of type codes](type-codes.md).
4. Write a shortcut for registering all tapers at once under the reserved type codes:
   ```dart
   extension TapersPackageForDartMath on TapersNamespace {
     Map<int, Taper<dynamic>> get forDartMath {
       return {
         -30: taper.forMutableRectangle<int>(),
         -31: taper.forMutableRectangle<double>(),
         -32: taper.forRectangle<int>(),
         -33: taper.forRectangle<double>(),
         -34: taper.forPoint<int>(),
         -35: taper.forPoint<double>(),
       };
     }
   }
   ```
5. Publish your package under the name `<original package>_tapers`
