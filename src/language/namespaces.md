# Namespaces

In JavaScript, there is no such thing as a namespace. Instead, JavaScript developers use sub-objects or conventions to implement namespace-like functionality.

In Rust, namespace refers to a different concept. The equivalent of a namespace
in Rust is a [module][rust-module]. For Rust, visibility of items
can be restricted using access modifiers, respectively visibility modifiers. In
Rust, the default visibility is _private_ (with only few exceptions). For more fine-grained access control, refer to the [visibility
modifiers] reference.

[rust-module]: https://doc.rust-lang.org/reference/items/modules.html
[visibility modifiers]: https://doc.rust-lang.org/reference/visibility-and-privacy.html
