# Lambda and Closures

Rust allows functions to be used as first-class values that enable
writing _higher-order functions_. Higher-order functions are essentially
functions that accept other functions as arguments to allow for the caller to
participate in the code of the called function. 

Rust has function pointers with the `fn` type being the simplest:

```rust
fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
    f(arg) + f(arg)
}

fn main() {
    let answer = do_twice(|x| x + 1, 5);
    println!("The answer is: {}", answer); // Prints: The answer is: 12
}
```
In JavaScript:
```js
function doTwice(f, arg) {
    return f(arg) + f(arg);
}

function main() {
    const answer = doTwice(x => x + 1, 5);
    console.log(`The answer is: ${answer}`); // Prints: The answer is: 12
}

main();

```

However, Rust makes a distinction between _function pointers_ (where `fn`
defines a type) and _closures_: a closure can reference variables from its
surrounding lexical scope, but not a function pointer. 

Functions and methods that accept closures are written with generic types that
are bound to one of the traits representing functions: `Fn`, `FnMut` and
`FnOnce`. When it's time to provide a value for a function pointer or a
closure, a Rust developer uses a _closure expression_ (like `|x| x + 1` in the
example above).
Whether the closure expression creates a function pointer or a closure depends
on whether the closure expression references its context or not.

When a closure captures variables from its environment then ownership rules
come into play because the ownership ends up with the closure. For more
information, see the “[Moving Captured Values Out of Closures and the Fn
Traits][closure-move]” section of The Rust Programming Language.

  [closure-move]: https://doc.rust-lang.org/book/ch13-01-closures.html#moving-captured-values-out-of-closures-and-the-fn-traits
