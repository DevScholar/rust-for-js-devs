# Threading

The Rust standard library supports threading, synchronisation and concurrency. Also the language itself and the standard library do have basic support for the concepts, a lot of additional functionality is provided by crates and will not be covered in this document.

JavaScript is a single-threaded scripting language that does not support multithreading.

<!--The following lists approximate mapping of threading types and methods in .NET
to Rust:

| .NET               | Rust                      |
| ------------------ | ------------------------- |
| `Thread`           | `std::thread::thread`     |
| `Thread.Start`     | `std::thread::spawn`      |
| `Thread.Join`      | `std::thread::JoinHandle` |
| `Thread.Sleep`     | `std::thread::sleep`      |
| `ThreadPool`       | -                         |
| `Mutex`            | `std::sync::Mutex`        |
| `Semaphore`        | -                         |
| `Monitor`          | `std::sync::Mutex`        |
| `ReaderWriterLock` | `std::sync::RwLock`       |
| `AutoResetEvent`   | `std::sync::Condvar`      |
| `ManualResetEvent` | `std::sync::Condvar`      |
| `Barrier`          | `std::sync::Barrier`      |
| `CountdownEvent`   | `std::sync::Barrier`      |
| `Interlocked`      | `std::sync::atomic`       |
| `Volatile`         | `std::sync::atomic`       |
| `ThreadLocal`      | `std::thread_local`       |
-->
Below is a simple JavaScript program that creates a thread (where the thread prints some text to standard output) indirectly by using `worker` and then waits for it to end:

```js
// Equivalent JavaScript code using Web Workers
const worker = new Worker(URL.createObjectURL(new Blob([`
    self.onmessage = function(event) {
        console.log(event.data);
    };
`], { type: 'application/javascript' })));

worker.postMessage('Hello from a thread!');
```

The same code in Rust would be as follows:

```rust
use std::thread;

fn main() {
    let thread = thread::spawn(|| println!("Hello from a thread!"));
    thread.join().unwrap(); // wait for thread to finish
}
```

Creating and initializing a thread object and starting a thread are two different actions in JavaScript whereas in Rust both happen at the same time with `thread::spawn`.

In JavaScript, it's possible to send data as an argument to a thread:
```js
const workerCode = `
self.onmessage = function(e) {
    let eventData = e.data;
    eventData += (" World!");
    console.log("Phrase: " + eventData);
};
`;

const blob = new Blob([workerCode], { type: "application/javascript" });
const worker = new Worker(URL.createObjectURL(blob));

const data = "Hello";
worker.postMessage(data);
```

<!--However, a more modern or terser version would use closures:

```csharp
using System;
using System.Text;
using System.Threading;

var data = new StringBuilder("Hello");

var t = new Thread(obj => data.Append(" World!"));

t.Start();
t.Join();

Console.WriteLine($"Phrase: {data}");
```-->

In Rust, there is no variation of `thread::spawn` that does the same. Instead, the data is passed to the thread via a closure:

```rust
use std::thread;

fn main() {
    let data = String::from("Hello");
    let handle = thread::spawn(move || {
        let mut data = data;
        data.push_str(" World!");
        data
    });
    println!("Phrase: {}", handle.join().unwrap());
}
```

A few things to note:

- The `move` keyword is _required_ to _move_ or pass the ownership of `data` to the closure for the thread. Once this is done, it's no longer legal to continue to use the `data` variable of `main`, in `main`. If that is needed, `data` must be copied or cloned (depending on what the type of the value supports).

- Rust thread can return values, which becomes the return value of the `join` method.

- It is possible to also pass data to the JavaScript thread via a closure, like the Rust example, but the JavaScript version does not need to worry about ownership since the memory behind the data will be reclaimed by the GC once no one is referencing it anymore.
