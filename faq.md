# Frequently asked questions

## Is Chest production-ready yet?

No, it's not yet.
Stay tuned.

These are some of the things planned before 1.0.0 release:

- [x] Support saving to and reading from chests
- [x] Support updating parts of chests
- [x] Support watching (parts of) chests
- [x] Properly handle multiple opens of the same Chest
- [x] Revisit value access syntax
- [x] Handle errors gracefully
- [x] Write docs on how it works in principle
- [x] Implement compaction
- [x] Develop Brand & Logo
  - [x] Color palette
  - [x] Font
  - [x] Logo
- [x] Use more efficient `TransferableTypedData`
- [x] Support manually compacting chests
- [x] Write docs on how to write tapers
- [x] Document the tape format
- [x] Document the file format
- [x] Set up website
- [x] Write docs on how to get started
- [ ] Support taper migration
- [ ] Support transactions
- [ ] Support storing references
- [ ] Support lazy chests
- [ ] Make errors more beautiful
  - [ ] Suggest tapers
- [ ] Add cycle detection
  - [ ] during serialization
  - [ ] during deserialization
- [ ] Code generation using tapegen
  - [ ] Create tapers
- [ ] Write tapers for various common types
  - [x] dart:core
  - [x] dart:math
  - [x] dart:typed_data
  - [ ] tuple
  - [ ] Flutter
- [ ] Write docs on how to migrate tapers
- [ ] Write tests
- [ ] Add CI
- [ ] Benchmark
  - [ ] Write performance suite
  - [ ] Compare with other databases
    - [ ] Hive & Lazy Hive
    - [ ] Sembast
    - [ ] SQLite
    - [ ] Shared Preferences
- [ ] Create ChestTools, a web interface for debugging Chest databases
  - [ ] Event stream
  - [ ] Data
    - [ ] See available chests
    - [ ] See chests' contents
    - [ ] See live updates of the content
    - [ ] Edit content
    - [ ] Clear chests
  - [ ] Performance
    - [ ] Startup
    - [ ] Decoding statistics
      - [ ] How many decodings are made?
    - [ ] How many updates occur?
  - [ ] Storage
    - [ ] File layout: Base data vs deltas
    - [ ] Trigger manual compaction
- [ ] Insert events into the Dart Debugging Timeline
- [ ] Properly handle opening a chest in multiple isolates (blocked by https://github.com/dart-lang/sdk/issues/44495)
- [ ] cross-Isolate adapter registry (blocked by https://github.com/dart-lang/sdk/issues/44495)

## Can Chest run on all platforms?

Chest runs on all platforms where the DartVM runs.
That's pretty much anywhere Dart runs except the browser.

Browser support is planned for the future though.

## Where are my chests stored?

Chests are stored in files inside the directory specified by `Chest.rootPath`.
Usually, that's the directory from where your program is run.

## Is Chest type-safe?

Yes, it is.
You need to register types before though.

## Can objects contain cycles?

Nope. Chest will detect them and throw an error. (TODO: It doesn't yet.)

## Can I open the same chest twice?

Nope. Chest will detect that and throw an error.

## Can I create the same chest twice and open both of them?

Yes! Both chest instances will actually connect to the same backend.
But you could just as easily make the chest global.
Because the objects you get from a chest are not referenced by others, that's okay – Chest is similar to riverpod in that regard.

## Can I create and open the same chest on multiple isolates?

Sadly, no.
Chest's architecture would actually support that – the file access and all other expensive operations happen on another isolate.
But an isolate can't find another isolate without having some communication beforehand, so there's no way for both isolates to find and communicate with a shared backend.

If this is important to you, consider giving [this issue](https://github.com/dart-lang/sdk/issues/44495) an upvote. 👍

## Can I open the same chest on multiple processes?

This won't work.
Please don't try this.

## Do I have to close the chests?

No, you don't. If you don't, your program won't stop automatically though.
So if you have a console application, the program will run infinitely long if you don't close the chest.
If you're using Flutter, don't worry about closing chests.
