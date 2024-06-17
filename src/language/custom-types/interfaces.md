# Interfaces

Rust doesn't have interfaces. It has _traits_, instead. Similar to an interface, a trait represents an abstraction and its members form a contract that must be fulfilled when implemented on a type.

In Rust, trait implementing the interface/trait can subsequently provide a more suitable and/or optimized implementation.

Traits in Rust can have (instance-based) method, associated functions and constants.

Apart from class hierarchies, interfaces are a core means of achieving polymorphism via dynamic dispatch for cross-cutting abstractions. They enable general-purpose code to be written against the abstractions represented by the interfaces without much regard to the concrete types implementing them. The same can be achieved with Rust's _trait objects_ in a limited fashion. A trait object is essentially a _v-table_ (virtual table) identified with the `dyn` keyword followed by the trait name, as in `dyn Shape` (where `Shape` is the trait name). Trait objects always live behind a pointer, either a reference (e.g. `&dyn Shape`) or the heap-allocated `Box` (e.g. `Box<dyn Shape>`).<!-- This is somewhat like in .NET, where an interface is a reference type such that a value type cast to an interface is automatically boxed onto the managed heap.--> The passing limitation of trait objects mentioned earlier, is that the original implementing type cannot be recovered. In other words, whereas it's quite common to downcast or test an interface to be an instance of some other interface or sub- or concrete type, the same is not possible in Rust (without additional effort and support).
