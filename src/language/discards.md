# Discards

Discards express to the compiler and others to ignore the results (or parts) of an expression.

There are multiple contexts where to apply this, for example as a basic example, to ignore the result of an expression. JavaScript doesn't have discards, but you can call a function without assigning a value to any variable to emulate discards. In JavaScript this looks like:

```js
city.getCityInformation(cityName);
```

In Rust, [ignoring the result of an expression][rust-ignoring-values] looks
identical:

```rust
_ = city.get_city_information(city_name);
```

Discards are also applied for deconstructing "tuples" in JavaScript:

```js
const [_, second] = ["first", "second"];
```

and, identically, in Rust:

```rust
let (_, second) = ("first", "second");
```

In addition to destructuring tuples, Rust offers [destructuring][rust-destructuring] of structs and enums using `..`, where `..` stands for the remaining part of a type:

```rust
struct Point {
    x: i32,
    y: i32,
    z: i32,
}

let origin = Point { x: 0, y: 0, z: 0 };

match origin {
    Point { x, .. } => println!("x is {}", x), // x is 0
}
```

When pattern matching, it is often useful to discard or ignore part of a matching expression. But since there are no discards in JavaScript, and the switch statement of js cannot be used in the same way as rust, you have to emulate this feature in an awkward way:

```js
const _ = ("first", "second");
const result = (_ => {
    switch(true) {
        case _.includes("first"):
            return "first element matched";
        default:
            return "first element did not match";
    }
})();

console.log(result);
```
<!--
```csharp
_ = ("first", "second") switch
{
    ("first", _) => "first element matched",
    (_, _) => "first element did not match"
};
```
-->
and again, in Rust:

```rust
_ = match ("first", "second")
{
    ("first", _) => "first element matched",
    (_, _) => "first element did not match"
};
```

[rust-ignoring-values]: https://doc.rust-lang.org/stable/book/ch18-03-pattern-syntax.html#ignoring-values-in-a-pattern
[rust-destructuring]: https://doc.rust-lang.org/reference/patterns.html#destructuring
