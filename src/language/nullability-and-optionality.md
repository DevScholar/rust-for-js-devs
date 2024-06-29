# Nullability and Optionality

In JavaScript, `null` is often used to represent a value that is missing, absent or logically uninitialized. For example:  

```js
let some = 1;
let none = null;
```

Rust has no `null` and consequently no nullable context to enable. Optional or missing values are instead represented by [`Option<T>`][option]. The equivalent of the JavaScript code above in Rust would be:  

```rust
let some: Option<i32> = Some(1);
let none: Option<i32> = None;
```

`Option<T>` in Rust is practically identical to [`'T option`][opt.fs] from F#.

[opt.fs]: https://fsharp.github.io/fsharp-core-docs/reference/fsharp-core-option-1.html

## Control flow with optionality

In JavaScript, you may have been using `if`/`else` statements for controlling the flow when using nullable values.

```js
let max = 10;
if (max !== null && max !== undefined) {
    let someMax = max;
    console.log(`The maximum is ${someMax}.`); // Output：The maximum is 10.
}
```

You can use pattern matching to achieve the same behavior in Rust:

```rust
let max = Some(10u32);
match max {
    Some(max) => println!("The maximum is {}.", max), // The maximum is 10.
    None => ()
}
```

It would even be more concise to use `if let`:

```rust
let max = Some(10u32);
if let Some(max) = max {
    println!("The maximum is {}.", max); // The maximum is 10.
}
```

## Null-conditional operators

 The null-conditional operators (`?.`) make dealing with `null` in JavaScript more ergonomic. In Rust, they are best replaced by using the [`map`][optmap] method. The following snippets show the correspondence: 

```js
let some = "Hello, World!";
let none = null;
console.log(some?.length); // 13
console.log(none?.length); // undefined
```

```rust
let some: Option<String> = Some(String::from("Hello, World!"));
let none: Option<String> = None;
println!("{:?}", some.map(|s| s.len())); // Some(13)
println!("{:?}", none.map(|s| s.len())); // None
```

## Null-coalescing operator

The null-coalescing operator (`??`) is typically used to default to another value when a nullable is `null`:

```js
let some = 1;
let none = null;
console.log(some ?? 0); // 1
console.log(none ?? 0); // 0
```

In Rust, you can use [`unwrap_or`][unwrap-or] to get the same behavior:

```rust
let some: Option<i32> = Some(1);
let none: Option<i32> = None;
println!("{:?}", some.unwrap_or(0)); // 1
println!("{:?}", none.unwrap_or(0)); // 0
```

 **Note**: If the default value is expensive to compute, you can use `unwrap_or_else` instead. It takes a closure as an argument, which allows you to lazily initialize the default value. 

## Null-forgiving operator

<!--The null-forgiving operator (`!`) does not correspond to an equivalent construct
in Rust, as it only affects the compiler's static flow analysis in C#. -->In Rust,
there is no need to use a substitute for it.

[option]: https://doc.rust-lang.org/std/option/enum.Option.html
[optmap]: https://doc.rust-lang.org/std/option/enum.Option.html#method.map
[unwrap-or]: https://doc.rust-lang.org/std/option/enum.Option.html#method.unwrap_or
