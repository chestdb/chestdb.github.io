# The Holy Table of Type Codes

You are free to use non-negative type codes in your apps.
Negative ones are reserved for tapers from packages.

If you want to publish a package containing tapers, follow [these instructions](tapers.md#publishing-tapers-for-a-package).
Don't hesitate to open a PR to reserve type codes.

<!-- TODO: Add link for opening a PR. -->

Type codes are always allocated in batches of 10 – the x stands for any digit.

type code | tapers package | type package | types
---:|---|---|---
 -x | chest | chest           | `Content`, `Version`, `TypeCodes`
-1x | chest | dart:core       | `Null`, `bool`, `String`, `int`, `double`, `BigInt`, `DateTime`, `Duration`, `List`, `Map`, `Set`
-2x | chest | dart:core       | 
-3x | chest | dart:math       | `MutableRectangle`, `Rectangle`, `Point`
-4x | chest | dart:typed_data | 
-5x | flutter_tapers | flutter | `Color`, `MaterialColor`, `ThemeMode`
