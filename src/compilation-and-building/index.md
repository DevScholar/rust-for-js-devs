# Compilation and Building

## JavaScript CLI

There is no concept of CLI in the JavaScript standard. People often use non-browser runtimes such as Node.js and Deno to act as CLIs.The equivalent of the JavaScript CLIs in Rust is [Cargo] (`cargo`). Both tools are entry-point wrappers that simplify use of other low-level tools. For example, although you could invoke the JavaScript engines, developers tend to use third-party tools such as `webpack` and `vite` to build their solution. Similarly in Rust, while you could use the Rust compiler (`rustc`) directly, using `cargo build` is generally far simpler.

[cargo]: https://doc.rust-lang.org/cargo/

## Building

When building JavaScript, the scripts coming from dependent packages are generally co-located with the project's output assembly. [`cargo build`][cargo-build] in Rust compiles the project sources, except the Rust compiler statically links (although there exist other [linking options][linkage]) all code into a single, platform-dependent, binary.

Developers use different ways to prepare a JavaScript executable for distribution, either as a _framework-dependent deployment_ (FDD) or _self-contained deployment_ (SCD). In Rust, there is no way to let the build output already contains a single, platform-dependent binary for each target.

In Rust, the build output is, again, a platform-dependent, compiled library for each library target.

See also:

- [Crate]

[cargo-build]: https://doc.rust-lang.org/cargo/commands/cargo-build.html#cargo-build1
[linkage]: https://doc.rust-lang.org/reference/linkage.html
[crate]: https://doc.rust-lang.org/book/ch07-01-packages-and-crates.html

## Dependencies

There is no concept of dependency in the JavaScript standard. However, some JavaScript runtimes, such as Node.js and Deno, have the concept of dependencies. In Node.js and Deno, the contents of a project file (`package.json`) define the build options and dependencies. In Rust, when using Cargo, a `Cargo.toml` declares the dependencies for a package. A typical project file will look like:

```json
{
  "name": "your-project-name",
  "version": "1.0.0",
  "description": "Your project description",
  "dependencies": {
    "linq": "4.0.3"
  }
}
```

The equivalent `Cargo.toml` in Rust is defined as:

```toml
[package]
name = "hello_world"
version = "0.1.0"

[dependencies]
tokio = "1.0.0"
```

Cargo follows a convention that `src/main.rs` is the crate root of a binary crate with the same name as the package. Likewise, Cargo knows that if the package directory contains `src/lib.rs`, the package contains a library crate with the same name as the package.

## Packages
There is no concept of packages in the JavaScript standard. However, some JavaScript runtimes, such as Node.js, have the concept of packages. NPM is most commonly used to install packages for Node.js, and various tools supported it.
For example, adding a Node.js package reference with the Node,js CLI will add the
dependency to the project file:

  npm install linq

In Rust this works almost the same if using Cargo to add packages.

  cargo add tokio

The most common package registry for Node.js is [npmjs.com] whereas Rust packages are usually shared via [crates.io].

[nuget.org]: https://www.npmjs.com/
[crates.io]: https://crates.io

## Static code analysis

ESLint is an analyzer that provide code quality as well as code-style analysis. The equivalent linting tool in Rust
is [Clippy].

Clippy can fail if the compiler or Clippy emits warnings (`cargo clippy -- -D warnings`).

There are further static checks to consider adding to a Rust CI pipeline:

- Run [`cargo doc`][cargo-doc] to ensure that documentation is correct.
- Run [`cargo check --locked`][cargo-check] to enforce that the `Cargo.lock` file is up-to-date.

[clippy]: https://github.com/rust-lang/rust-clippy
[cargo-doc]: https://doc.rust-lang.org/cargo/commands/cargo-doc.html
[cargo-check]: https://doc.rust-lang.org/cargo/commands/cargo-check.html#manifest-options
