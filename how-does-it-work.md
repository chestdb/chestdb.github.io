# How does it work?

Databases usually store data in files and Chest is no different.
When you open a chest, the file's raw bytes are loaded into memory without doing any deserialization.
So, to Dart's garbage collector, the chest's content is just one big object.

Those bytes are optimized for quick partial deserialization and a low memory footprint.
That makes accessing values pretty fast.

If you change part of the value, only the update is appended to the end of the file.
As more updates accumulate, Chest periodically merges the updates with the existing value.

By the way:
Merging updates and accessing files happen on another `Isolate` (Dart's version of threads), so they don't impact performance of your main `Isolate`.
And if you open a chest multiple times, the same instance is reused.
