# Generics

Generics provide a way to create definitions for types and methods that can be parameterized over other types. This improves code reuse, type-safety and performance (e.g. avoid run-time casts). Consider the following example of a generic type that adds a timestamp to any value. However, JavaScript does not have the concept of generics.

```js
class Timestamped {
    constructor(value) {
        this.Timestamp = new Date();
        this.Value = value;
    }
}

```

Rust has generics as shown by the equivalent of the above:

```rust
use std::time::*;

struct Timestamped<T> { value: T, timestamp: SystemTime }

impl<T> Timestamped<T> {
    fn new(value: T) -> Self {
        Self { value, timestamp: SystemTime::now() }
    }
}
```

See also:

- [Generic data types]

[Generic data types]: https://doc.rust-lang.org/book/ch10-01-syntax.html

## Generic type constraints

JavaScript has no concept of generics, and it is a weakly typed scripting language that makes it impossible to add type constraints to it.

```js
class Timestamped {
    constructor(value) {
        this.value = value;
        this.timestamp = Date.now();
    }

    equals(other) {
        return this.value === other.value && this.timestamp === other.timestamp;
    }
}
```

The same can be achieved in Rust:

```rust
use std::time::*;

struct Timestamped<T> { value: T, timestamp: SystemTime }

impl<T> Timestamped<T> {
    fn new(value: T) -> Self {
        Self { value, timestamp: SystemTime::now() }
    }
}

impl<T> PartialEq for Timestamped<T>
    where T: PartialEq {
    fn eq(&self, other: &Self) -> bool {
        self.value == other.value && self.timestamp == other.timestamp
    }
}
```

Generic type constraints are called [bounds][bounds.rs] in Rust.

In Rust, this is flexible because it `Timestamped<T>` _conditionally implements_ `PartialEq`. This means that `Timestamped<T>` instances can still be created for some non-equatable `T`, but then `Timestamped<T>` will not implement equality via `PartialEq` for such a `T`.

See also:

- [Traits as parameters]
- [Returning types that implement traits]

[bounds.rs]: https://doc.rust-lang.org/rust-by-example/generics/bounds.html
[Traits as parameters]: https://doc.rust-lang.org/book/ch10-02-traits.html#traits-as-parameters
[Returning types that implement traits]: https://doc.rust-lang.org/book/ch10-02-traits.html#returning-types-that-implement-traits
