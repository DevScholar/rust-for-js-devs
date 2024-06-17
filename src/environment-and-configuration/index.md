# Environment and Configuration

## Accessing environment variables

JavaScript doesn't provide access to environment variables natively. However, some non-browser JavaScript runtimes, such as Node.js and Node provides access to environment variables.

In Node.js:

```js
const name = "EXAMPLE_VARIABLE";

let value = process.env[name];
if (!value) {
    console.log(`Variable '${name}' not set.`);
} else {
    console.log(`Variable '${name}' set to '${value}'.`);
}
```
In Deno:

```js
const name = "EXAMPLE_VARIABLE";

let value = Deno.env.get(name);
if (!value) {
    console.log(`Variable '${name}' not set.`);
} else {
    console.log(`Variable '${name}' set to '${value}'.`);
}
```

Rust is providing the same functionality of accessing an environment variable at runtime via the `var` and `var_os` functions from the `std::env` module.

`var` function is returning a `Result<String, VarError>`, either returning the variable if set or returning an error if the variable is not set or it is not valid Unicode.

`var_os` has a different signature giving back an `Option<OsString>`, either returning some value if the variable is set, or returning None if the variable is not set. An `OsString` is not required to be valid Unicode.

```rust
use std::env;


fn main() {
    let key = "ExampleVariable";
    match env::var(key) {
        Ok(val) => println!("{key}: {val:?}"),
        Err(e) => println!("couldn't interpret {key}: {e}"),
    }
}
```

```rust
use std::env;

fn main() {
    let key = "ExampleVariable";
    match env::var_os(key) {
        Some(val) => println!("{key}: {val:?}"),
        None => println!("{key} not defined in the enviroment"),
    }
}
```

Rust is also providing the functionality of accessing an environment variable at compile time. The `env!` macro from `std::env` expands the value of the variable at compile time, returning a `&'static str`. If the variable is not set, an error is emitted.

```rust
use std::env;

fn main() {
    let example = env!("ExampleVariable");
    println!("{example}");
}
```

## Configuration

JavaScript doesn't support configurations.
<!--Configuration in .NET is possible with configuration providers. The framework is
providing several provider implementations via
`Microsoft.Extensions.Configuration` namespace and NuGet packages.

Configuration providers read configuration data from key-value pairs using
different sources and provide a unified view of the configuration via the
`IConfiguration` type.

```csharp
using Microsoft.Extensions.Configuration;

class Example {
    static void Main()
    {
        IConfiguration configuration = new ConfigurationBuilder()
            .AddEnvironmentVariables()
            .Build();

        var example = configuration.GetValue<string>("ExampleVar");

        Console.WriteLine(example);
    }
}
```

Other providers examples can be found in the official documentation
[Configurations provider in .NET][conf-net].

A similar configuration experience in Rust is available via use of third-party
crates such as [figment] or [config].
-->
In Rust it is available via use of third-party crates such as [figment] or [config]. See the following example making use of [config] crate:

```rust
use config::{Config, Environment};

fn main() {
    let builder = Config::builder().add_source(Environment::default());

    match builder.build() {
        Ok(config) => {
            match config.get_string("examplevar") {
                Ok(v) => println!("{v}"),
                Err(e) => println!("{e}")
            }
        },
        Err(_) => {
            // something went wrong
        }
    }
}

```

[conf-net]: https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers
[figment]: https://crates.io/crates/figment
[config]: https://crates.io/crates/config
