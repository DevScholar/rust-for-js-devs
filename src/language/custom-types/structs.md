# Structures (`struct`)

In JavaScript, there is no direct concept of a structure, but you can use objects to model similar structures.
Structures in Rust:

- In Rust, `struct` simply
  defines the data/fields. The behavioural aspects in terms of functions and
  methods, are defined separately in an _implementation block_ (`impl`).

- They can implement multiple traits in Rust.

- They cannot be sub-classed.

- They are allocated on stack by default, unless:
  - In Rust, wrapped in a smart pointer like `Box`, `Rc`/`Arc`.

In Rust, a `struct` is the primary construct for modeling any data structure (the
other being an `enum`).

A `struct` in Rust requires just one more step using [the
`#derive` attribute][derive] and listing the traits to be implemented:

  [derive]: https://doc.rust-lang.org/stable/reference/attributes/derive.html

```rust
#[derive(Clone, Copy, PartialEq, Eq, Hash)]
struct Point {
    x: i32,
    y: i32,
}
```

Value types in JavaScript are usually designed by a developer to be mutable.
It's considered best practice speaking semantically, but the language does not
prevent designing a `struct` that makes destructive or in-place modifications.
In Rust, it's the same. A type has to be consciously developed to be
immutable.

Since Rust doesn't have classes and consequently type hierarchies based on
sub-classing, shared behaviour is achieved via traits and generics and
polymorphism via virtual dispatch using [trait objects].

  [trait objects]: https://doc.rust-lang.org/book/ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types

In JavaScript:

```js
class Rectangle {
    constructor(x1, y1, x2, y2) {
        this.x1 = x1;
        this.y1 = y1;
        this.x2 = x2;
        this.y2 = y2;
    }

    length() {
        return this.y2 - this.y1;
    }

    width() {
        return this.x2 - this.x1;
    }

    top_left() {
        return [this.x1, this.y1];
    }

    bottom_right() {
        return [this.x2, this.y2];
    }

    area() {
        return this.length() * this.width();
    }

    is_square() {
        return this.width() === this.length();
    }

    toString() {
        return `(${this.x1}, ${this.y1}), (${this.x2}, ${this.y2})`;
    }
}

const rect = new Rectangle(0, 0, 4, 4);
console.log(rect.area());
console.log(rect.toString());
```

The equivalent in Rust would be:

```rust
#![allow(dead_code)]

struct Rectangle {
    x1: i32, y1: i32,
    x2: i32, y2: i32,
}

impl Rectangle {
    pub fn new(x1: i32, y1: i32, x2: i32, y2: i32) -> Self {
        Self { x1, y1, x2, y2 }
    }

    pub fn x1(&self) -> i32 { self.x1 }
    pub fn y1(&self) -> i32 { self.y1 }
    pub fn x2(&self) -> i32 { self.x2 }
    pub fn y2(&self) -> i32 { self.y2 }

    pub fn length(&self) -> i32 {
        self.y2 - self.y1
    }

    pub fn width(&self)  -> i32 {
        self.x2 - self.x1
    }

    pub fn top_left(&self) -> (i32, i32) {
        (self.x1, self.y1)
    }

    pub fn bottom_right(&self) -> (i32, i32) {
        (self.x2, self.y2)
    }

    pub fn area(&self)  -> i32 {
        self.length() * self.width()
    }

    pub fn is_square(&self)  -> bool {
        self.width() == self.length()
    }
}

use std::fmt::*;

impl Display for Rectangle {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result {
        write!(f, "({}, {}), ({}, {})", self.x1, self.y2, self.x2, self.y2)
    }
}
```

Since there is no inheritance in Rust, the way a type
advertises support for some _formatted_ representation is by implementing the
`Display` trait. This then enables for an instance of the structure to
participate in formatting, such as shown in the call to `println!` below:

```rust
fn main() {
    let rect = Rectangle::new(12, 34, 56, 78);
    println!("Rectangle = {rect}");
}
```
