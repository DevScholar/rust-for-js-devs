# Inheritance

As explained in [structures] section, Rust does not provide (class-based) inheritance. A way to provide shared behavior between structs is via making use of traits. However, Rust allows to define relationships between traits by using [_supertraits_][supertrait.rs].

[structures]: ./custom-types/structs.md
[supertrait.rs]: https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#using-supertraits-to-require-one-traits-functionality-within-another-trait
