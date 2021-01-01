# Tapers

Chest uses its own encoding called *tape*.

To store an object of some type in a Chest, a *taper* needs to be registered for that type.
The taper takes care of turning that object into bytes and later converting those bytes back into the object.

## Registering tapers

Each taper is registered under a *type code* (a number).
Tapers are usually registered at the beginning of the `main` method with a call that looks like this:

```dart
tape.register({
  ...tapers.forDartCore,
  ...tapers.forTuple,
  ...tapers.forFlutter,
  0: taper.forUser(),
  1: taper.forList<User>(),
  2: legacyTaper.forPet().v1,
  3: taper.forPet(),
});
```

Note that for [for many popular packages](type-codes.md), including `dart:core`, tapers are already available.
For your own types, use type codes `>= 0`.

?> Tapers are usually defined as extension methods on `TaperNamespace`, a class which has the `taper` instance.
Those methods typically follow the naming scheme `for<TypeName>`. That makes creating tapers for types with generics intuitive: `taper.forList<User>()`

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
extension TaperForFruit on TaperNamespace {
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

If you want to make tapers you write available to the community, follow these steps:

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
5. Publish your package.
