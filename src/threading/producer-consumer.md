# Producer-Consumer

The producer-consumer pattern is very common to distribute work between threads where data is passed from producing threads to consuming threads without the need for sharing or locking. 

```js
const workerCode = `
    self.onmessage = function() {
        const messages = [];
        for (let n = 1; n < 10; n++) {
            messages.push("Message #" + n);
        }
        self.postMessage(messages);
    };
`;

const blob = new Blob([workerCode], { type: "application/javascript" });
const worker = new Worker(URL.createObjectURL(blob));

// The main thread acts as a consumer here
worker.onmessage = function(event) {
    const messages = event.data;
    messages.forEach(message => console.log(message));
};

// Start the worker
worker.postMessage(null);

```
The same can be done in Rust using _channels_. The standard library primarily provides `mpsc::channel`, which is a channel that supports multiple producers and a single consumer. A rough translation of the above C# example in Rust would look as follows:

The same can be done in Rust using _channels_. The standard library primarily provides `mpsc::channel`, which is a channel that supports multiple producers and a single consumer. A rough translation of the above C# example in Rust would look as follows:

```rust
use std::thread;
use std::sync::mpsc;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();

    let producer = thread::spawn(move || {
        for n in 1..10 {
            tx.send(format!("Message #{}", n)).unwrap();
        }
    });

    // main thread is the consumer here
    for received in rx {
        println!("{}", received);
    }

    producer.join().unwrap();
}
```

The equivalent of the [async-friendly channels in the Rust space is offered by the Tokio runtime][tokio-channels].

  [tokio-channels]: https://tokio.rs/tokio/tutorial/channels
