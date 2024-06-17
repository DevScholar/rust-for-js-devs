# Documentation Comments

A third-party tool called JSDoc provides a mechanism to document the API for types using a comment syntax. JSDoc includes a Markdown plugin that automatically converts Markdown-formatted text to HTML. The comment contains structured data representing the comments and the API signatures. Other tools can process that output to provide human-readable documentation in a different form. A simple example in JavaScript:

```js
public class MyClass {}
/**
 * This is a document comment for `MyClass`.
 * @class
 */
class MyClass {}
```

In Rust [doc comments] provide the equivalent to JSDoc documentation comments. Documentation comments in Rust use Markdown syntax. [`rustdoc`][rustdoc] is the documentation compiler for Rust code and is usually invoked through [`cargo doc`][cargo doc], which compiles the comments into documentation. For example:

```rust
/// This is a doc comment for `MyStruct`.
struct MyStruct;
```

In JSDoc, the equivalent to `cargo doc` is `jsdoc`.

See also:

- [How to write documentation]
- [Documentation tests]

[doc comments]: https://doc.rust-lang.org/rust-by-example/meta/doc.html
[rustdoc]: https://doc.rust-lang.org/rustdoc/index.html
[cargo doc]: https://doc.rust-lang.org/cargo/commands/cargo-doc.html
[How to write documentation]: https://doc.rust-lang.org/rustdoc/how-to-write-documentation.html
[documentation tests]: https://doc.rust-lang.org/rustdoc/write-documentation/documentation-tests.html
