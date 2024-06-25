# Project Structure

The JavaScript standard does not specify a specification for the structure of the project. Generally speaking, all the files in a JavaScript library are usually placed in a folder named after the library. The following is a common JavaScript project specification:

    .
    +-- src/
    |   +-- project1.js
    +-- styles/
    |   +-- style1.js
    +-- examples/
    |   +-- some-example.js
    +-- tests/
        +-- some-integration-test.js

Cargo uses the following conventions for the [package layout] to make it easy to dive into a new Cargo [package][rust-package]:

    .
    +-- Cargo.lock
    +-- Cargo.toml
    +-- src/
    |   +-- lib.rs
    |   +-- main.rs
    +-- benches/
    |   +-- some-bench.rs
    +-- examples/
    |   +-- some-example.rs
    +-- tests/
        +-- some-integration-test.rs

- `Cargo.toml` and `Cargo.lock` are stored in the root of the package.
- `src/lib.rs` is the default library file, and `src/main.rs` is the default executable file (see [target auto-discovery]).
- Benchmarks go in the `benches` directory, integration tests go in the `tests` directory (see [testing][section-testing], [benchmarking][section-benchmarking]).
- Examples go in the `examples` directory.
- There is no separate crate for unit tests, unit tests live in the same file as the code (see [testing][section-testing]).

[package layout]: https://doc.rust-lang.org/cargo/guide/project-layout.html
[rust-package]: https://doc.rust-lang.org/cargo/appendix/glossary.html#package
[target auto-discovery]: https://doc.rust-lang.org/cargo/reference/cargo-targets.html#target-auto-discovery
[section-testing]: ../testing/index.md
[section-benchmarking]: ../benchmarking/index.md

## Managing large projects

For very large projects in Rust, Cargo offers [workspaces][cargo-workspaces] to organize the project. A workspace can help manage multiple related packages that are developed in tandem. Some projects use [_virtual manifests_][cargo-virtual-manifest], especially when there is no primary package.

[cargo-workspaces]: https://doc.rust-lang.org/book/ch14-03-cargo-workspaces.html
[cargo-virtual-manifest]: https://doc.rust-lang.org/cargo/reference/workspaces.html#virtual-workspace

## Managing dependency versions

There is no concept of dependency in the JavaScript standard. However, some JavaScript runtimes, such as Node.js and Deno, have the concept of dependencies.
When managing larger projects in Node.js, it may be appropriate to manage the versions of dependencies centrally, using strategies such as [nvm]. Deno uses [dvm].  Cargo introduced [workspace inheritance] to manage dependencies centrally. 
[nvm]: https://github.com/nvm-sh/nvm
[dvm]: https://github.com/justjavac/dvm
[workspace inheritance]: https://doc.rust-lang.org/cargo/reference/workspaces.html#the-package-table
