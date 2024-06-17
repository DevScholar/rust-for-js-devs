# Exception Handling

In JavaScript, an exception should always be an [`Error`][js-system-exception] object or an instance of an `Error` subclass. Exceptions are thrown if a problem occurs in a code section. A thrown exception is passed up the stack until the application handles it or the program terminates.

Rust does not have exceptions, but distinguishes between _recoverable_ and _unrecoverable_ errors instead. A recoverable error represents a problem that should be reported, but for which the program continues. Results of operations that can fail with recoverable errors are of type [`Result<T, E>`][rust-result], where `E` is the type of the error variant. The [`panic!`][panic] macro stops execution when the program encounters an unrecoverable error. An unrecoverable error is always a symptom of a bug.

## Custom error types

An example on [how to create user-defined exceptions][js-user-defined-exceptions]:

```js
class EmployeeListNotFoundException extends Error {
    constructor(message) {
        super(message);
        this.name = 'EmployeeListNotFoundException';
    }
}
```

In Rust, one can implement the basic expectations for error values by implementing the [`Error`][rust-std-error] trait. The minimal user-defined error implementation in Rust is:

```rust
#[derive(Debug)]
pub struct EmployeeListNotFound;

impl std::fmt::Display for EmployeeListNotFound {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        f.write_str("Could not find employee list.")
    }
}

impl std::error::Error for EmployeeListNotFound {}
```

The equivalent to the JavaScript `Error.cause` property is the `Error::source()` method in Rust. However, it is not required to provide an implementation for `Error::source()`, the blanket (default) implementation returns a `None`.

## Raising exceptions

To raise an error in JavaScript, throw an error:

```js
function throwIfNegative(value) {
    if (value < 0) {
        throw new Error('Value cannot be negative');
    }
}

```

For recoverable errors in Rust, return an `Ok` or `Err` variant from a method:

```rust
fn error_if_negative(value: i32) -> Result<(), &'static str> {
    if value < 0 {
        Err("Specified argument was out of the range of valid values. (Parameter 'value')")
    } else {
        Ok(())
    }
}
```

The [`panic!`][panic] macro creates unrecoverable errors:

```rust
fn panic_if_negative(value: i32) {
    if value < 0 {
        panic!("Specified argument was out of the range of valid values. (Parameter 'value')")
    }
}
```

## Error propagation

In .NET, exceptions are passed up until they are handled or the program terminates. In Rust, unrecoverable errors behave similarly, but handling them is uncommon.

Recoverable errors, however, need to be propagated and handled explicitly. Their presence is always indicated by the Rust function or method signature. Catching an exception allows you to take action based on the presence or absence of an error in JavaScript:

```csharp
//JavaScript doesn't have a file system in it. People often implement file systems using the BrowserFS library that mimic Node.js APIs.
function write() {
    try {
        fs.writeFileSync('file.txt', 'content');
    } catch (error) {
        console.log('Writing to file failed.');
    }
}

```

In Rust, this is roughly equivalent to:

```rust
fn write() {
    match std::fs::File::create("temp.txt")
        .and_then(|mut file| std::io::Write::write_all(&mut file, b"content"))
    {
        Ok(_) => {}
        Err(_) => println!("Writing to file failed."),
    };
}
```

Frequently, recoverable errors need only be propagated instead of being handled. For this, the method signature needs to be compatible with the types of the propagated error. The [`?` operator][question-mark-operator] propagates errors ergonomically:

```rust
fn write() -> Result<(), std::io::Error> {
    let mut file = std::fs::File::create("file.txt")?;
    std::io::Write::write_all(&mut file, b"content")?;
    Ok(())
}
```

**Note**: to propagate an error with the question mark operator the error implementations need to be _compatible_, as described in [_a shortcut for propagating errors_][propagating-errors-rust-book]. The most general "compatible" error type is the error [trait object] `Box<dyn Error>`.

## Stack traces

<!--Throwing an unhandled exception in .NET will cause the runtime to print a stack
trace that allows debugging the problem with additional context.-->

For unrecoverable errors in Rust, [`panic!` Backtraces][panic-backtrace] offer a similar behavior.

Recoverable errors in stable Rust do not yet support Backtraces, but it is currently supported in experimental Rust when using the [provide method].

[js-system-exception]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error
[rust-result]: https://doc.rust-lang.org/std/result/enum.Result.html
[panic-backtrace]: https://doc.rust-lang.org/book/ch09-01-unrecoverable-errors-with-panic.html#using-a-panic-backtrace
[js-user-defined-exceptions]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/Error
[rust-std-error]: https://doc.rust-lang.org/std/error/trait.Error.html
[provide method]: https://doc.rust-lang.org/std/error/trait.Error.html#method.provide
[question-mark-operator]: https://doc.rust-lang.org/std/result/index.html#the-question-mark-operator-
[panic]: https://doc.rust-lang.org/std/macro.panic.html
[propagating-errors-rust-book]: https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html#a-shortcut-for-propagating-errors-the--operator
[trait object]: https://doc.rust-lang.org/reference/types/trait-object.html
