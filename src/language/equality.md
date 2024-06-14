# Equality

When comparing for equality in JavaScript, this refers to testing for _equivalence_ insome cases (also known as _value equality_), and in other cases it refers to
testing for _reference equality_, which tests whether two variables refer to the
same underlying object in memory. In JavaScript, while there is no syntax for explicitly custom types, custom types can be simulated through constructors and prototypes. Constructors allow you to create objects with specific properties and methods, and you can use prototypes to implement inheritance and shared methods. Every "custom type" can be compared for equality
because it inherits from `object`.

For example, when comparing for equivalence and reference equality in JavaScript:

```js
class Point {
    constructor(X, Y) {
        this.X = X;
        this.Y = Y;
    }
    
    equals(other) {
        return this.X === other.X && this.Y === other.Y;
    }
}

const a = new Point(1, 2);
const b = new Point(1, 2);
const c = a;

console.log(a.equals(b)); // (1) true
console.log(a.equals(new Point(2, 2))); // (1) false
console.log(a === b); // (2) false
console.log(a === c); // (2) true
```

1. In JavaScript, classes are used to implement equals methods to compare the equality of values.
2. For the comparison of reference equality, using the === operator to check if the variable points to the same object in memory.

Equivalently in Rust:

```rust
#[derive(Copy, Clone)]
struct Point(i32, i32);

fn main() {
    let a = Point(1, 2);
    let b = Point(1, 2);
    let c = a;
    println!("{}", a == b); // Error: "an implementation of `PartialEq<_>` might be missing for `Point`"
    println!("{}", a.eq(&b));
    println!("{}", a.eq(&Point(2, 2)));
}
```

The compiler error above illustrates that in Rust equality comparisons are
_always_ related to a trait implementation. To support a comparison using `==`,
a type must implement [`PartialEq`][partialeq.rs].

Fixing the example above means deriving `PartialEq` for `Point`. Per default,
deriving `PartialEq` will compare all fields for equality, which therefore have
to implement `PartialEq` themselves. This is comparable to the equality for
records in C#.

```rust
#[derive(Copy, Clone, PartialEq)]
struct Point(i32, i32);

fn main() {
    let a = Point(1, 2);
    let b = Point(1, 2);
    let c = a;
    println!("{}", a == b); // true
    println!("{}", a.eq(&b)); // true
    println!("{}", a.eq(&Point(2, 2))); // false
    println!("{}", a.eq(&c)); // true
}
```

See also:

- [`Eq`][eq.rs] for a stricter version of `PartialEq`.

[partialeq.rs]: https://doc.rust-lang.org/std/cmp/trait.PartialEq.html
[eq.rs]: https://doc.rust-lang.org/std/cmp/trait.Eq.html
