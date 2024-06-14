# Structured Types

Commonly used object and collection types in JavaScript and their mapping to Rust

| JavaScript | Rust      |
| ---------- | --------- |
| `array`    | `Array`   |
| `array`    | `Vec`     |
| `array`    | `Tuple`   |
| `object`   | `HashMap` |
## Array

Fixed arrays are supported the same way in Rust as in JavaScript

JavaScript:

```js
let someArray = [1,2];
```

Rust:

```rust
let someArray: [i32; 2] = [1,2];
```

## List

In Rust the equivalent of a `array` is a `Vec<T>`. Arrays can be converted
to Vecs and vice versa.

JavaScript:

```js
let something = ["a", "b"];
something.push("c");
```

Rust:

```rust
let mut something = vec![
    "a".to_owned(),
    "b".to_owned()
];

something.push("c".to_owned());
```

## Tuples

JavaScript:

```js
const let something = [1, 2];
console.log(`a = ${something[0]} b = ${something[1]}`);
```

Rust:

```rust
let something = (1, 2);
println!("a = {} b = {}", something.0, something.1);

// deconstruction supported
let (a, b) = something;
println!("a = {} b = {}", a, b);
```

> **NOTE**: Rust tuple elements cannot be named. The only way to
> access a tuple element is by using the index of the element or deconstructing
> the tuple.

## Dictionary

In Rust the equivalent of a `Dictionary<TKey, TValue>` is a `object`.

JavaScript:

```js
var something = {
    "Foo": "Bar",
    "Baz": "Qux"
};

something["hi"] = "there";
```

Rust:

```rust
let mut something = HashMap::from([
    ("Foo".to_owned(), "Bar".to_owned()),
    ("Baz".to_owned(), "Qux".to_owned())
]);

something.insert("hi".to_owned(), "there".to_owned());
```

See also:

- [Rust's standard library - Collections](https://doc.rust-lang.org/std/collections/index.html)
