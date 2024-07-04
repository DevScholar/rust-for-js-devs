# Strings

There are two string types in Rust: `String` and `&str`. The former is allocated on the heap and the latter is a slice of a `String` or a `&str`.

The mapping of those to JavaScript is shown in the following table:

| Rust               | JavaScript | Note        |
| ------------------ | ---------- | ----------- |
| `&mut str`         | N/A        |             |
| `&str`             | N/A        |             |
| `Box<str>`         | `string`   | see Note 1. |
| `String`           | `string`   |             |
| `String` (mutable) | `string`   | see Note 1. |

There are differences in working with strings in Rust and JavaScript, but the equivalents above should be a good starting point. One of the differences is that Rust strings are UTF-8 encoded, but JavaScript strings are UTF-16 encoded. Rust strings can be mutable when declared as such, for example `let s = &mut String::from("hello");`.

There are also differences in using strings due to the concept of ownership. To read more about ownership with the String Type, see the [Rust Book][ownership-string-type-example].

[ownership-string-type-example]: https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#the-string-type

Notes:

1. JavaScript has only one string type, `string`. JavaScript has no pointer types.

JavaScript:

```js
let str = "Hello, World!"; 
let str = new String("Hello, World!")
```

Rust:

```rust
let str = Box::new("Hello World!");
let mut sb = String::from("Hello World!");
```

## String Literals

String literals in JavaScript `String` types and allocated on the heap. In Rust, they are `&'static str`, which is immutable and has a global lifetime and does not get allocated on the heap; they're embedded in the compiled binary.
JavaScript:

```js
let str = "Hello, World!";
```

Rust:

```rust
let str: &'static str = "Hello, World!";
```

JavaScript verbatim string literals are equivalent to Rust raw string literals.

JavaScript

```js
let str = `Hello, \World/!`;
```

Rust

```rust
let str = r#"Hello, \World/!"#;
```

Rust UTF-8 string literals:

```rust
let str = b"hello";
```

## String Interpolation

JavaScript has a built-in string interpolation feature that allows you to embed expressions inside a string literal. The following example shows how to use string interpolation in JavaScript:

```javascript
let name = "John";
let age = 42;
let str = `Person Name: ${name}, Age: ${age} `;
```

Rust does not have a built-in string interpolation feature. Instead, the `format!` macro is used to format a string. The following example shows how to use string interpolation in Rust:

```rust
let name = "John";
let age = 42;
let str = format!("Person {{ name: {name}, age: {age} }}");
```

Custom classes and structs can also be interpolated in JavaScript due to the fact that the `toString()` method is available for each type as it inherits from `object`.

```js
class Person {
    constructor({
        name,
        age
    }) {
        this.name = name;
        this.age = age;
        this.toString = function() {
            return `Person Name: ${name}, Age: ${age}`;
        }
    }

}
let person = new Person({
    name: "John",
    age: 42
});
console.log(person);
```

In Rust, there is no default formatting implemented/inherited for each type. Instead, the `std::fmt::Display` trait must be implemented for each type that needs to be converted to a string.

```rust
use std::fmt::*;

struct Person {
    name: String,
    age: i32,
}

impl Display for Person {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result {
        write!(f, "Person {{ name: {}, age: {} }}", self.name, self.age)
    }
}

let person = Person {
    name: "John".to_owned(),
    age: 42,
};

println!("{person}");
```

Another option is to use the `std::fmt::Debug` trait. The `Debug` trait is implemented for all standard types and can be used to print the internal representation of a type. The following example shows how to use the `derive` attribute to print the internal representation of a custom struct using the `Debug` macro. This declaration is used to automatically implement the `Debug` trait for the `Person` struct:

```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: i32,
}

let person = Person {
    name: "John".to_owned(),
    age: 42,
};

println!("{person:?}");
```

> Note: Using the :? format specifier will use the `Debug` trait to print the struct, where leaving it out will use the `Display` trait.

See also:

- [Rust by Example - Debug](https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_debug.html?highlight=derive#debug)
