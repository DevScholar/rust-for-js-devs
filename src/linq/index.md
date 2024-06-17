# LINQ

This section discusses LINQ within the context and for the purpose of querying or transforming sequences and typically collections like lists, sets and dictionaries.

## Enumerable items

The equivalent of enumerable items in Rust is [`IntoIterator`][into-iter.rs]. An implementation of `IntoIterator::into_iter` returns an [`Iterator`][iter.rs]. However, when it's time to iterate over the items of a container advertising iteration support through the said types, both languages offer syntactic sugar in the form of looping constructs for iteratables. In JavaScript, there is `forEach`: 

```js
let values = [1, 2, 3, 4, 5];
let output = '';

values.forEach((value, index) => {
    if (index > 0)
        output += ', ';
    output += value;
});

console.log(output); // Outputs: 1, 2, 3, 4, 5
```

In Rust, the equivalent is simply `for`:

```rust
use std::fmt::Write;

fn main() {
    let values = [1, 2, 3, 4, 5];
    let mut output = String::new();

    for value in values {
        if output.len() > 0 {
            output.push_str(", ");
        }
        // ! discard/ignore any write error
        _ = write!(output, "{value}");
    }

    println!("{output}");  // Prints: 1, 2, 3, 4, 5
}
```

The `for` loop over an iterable essentially gets desuraged to the following:

```rust
use std::fmt::Write;

fn main() {
    let values = [1, 2, 3, 4, 5];
    let mut output = String::new();

    let mut iter = values.into_iter();      // get iterator
    while let Some(value) = iter.next() {   // loop as long as there are more items
        if output.len() > 0 {
            output.push_str(", ");
        }
        _ = write!(output, "{value}");
    }

    println!("{output}");
}
```

Rust's ownership and data race condition rules apply to all instances and data, and iteration is no exception. So while looping over an array might look straightforward, one has to be mindful about ownership when needing to iterate the same collection/iterable more than once. The following example iteraters the list of integers twice, once to print their sum and another time to determine and print the maximum integer: 

```rust
fn main() {
    let values = vec![1, 2, 3, 4, 5];

    // sum all values

    let mut sum = 0;
    for value in values {
        sum += value;
    }
    println!("sum = {sum}");

    // determine maximum value

    let mut max = None;
    for value in values {
        if let Some(some_max) = max { // if max is defined
            if value > some_max {     // and value is greater
                max = Some(value)     // then note that new max
            }
        } else {                      // max is undefined when iteration starts
            max = Some(value)         // so set it to the first value
        }
    }
    println!("max = {max:?}");
}
```

However, the code above is rejected by the compiler due to a subtle difference: `values` has been changed from an array to a [`Vec<int>`][vec.rs], a _vector_, which is Rust's type for growable arrays. The first iteration of `values` ends up _consuming_ each value as the integers are summed up. In other words, the ownership of _each item_ in the vector passes to the iteration variable of the loop: `value`. Since `value` goes out of scope at the end of each iteration of the loop, the instance it owns is dropped. Had `values` been a vector of heap-allocated data, the heap memory backing each item would get freed as the loop moved to the next item. To fix the problem, one has to request iteration over _shared_ references via `&values` in the `for` loop. As a result, `value` ends up being a shared reference to an item as opposed to taking its ownership.

  [vec.rs]: https://doc.rust-lang.org/stable/std/vec/struct.Vec.html

Below is the updated version of the previous example that compiles. The fix is to simply replace `values` with `&values` in each of the `for` loops.

```rust
fn main() {
    let values = vec![1, 2, 3, 4, 5];

    // sum all values

    let mut sum = 0;
    for value in &values {
        sum += value;
    }
    println!("sum = {sum}");

    // determine maximum value

    let mut max = None;
    for value in &values {
        if let Some(some_max) = max { // if max is defined
            if value > some_max {     // and value is greater
                max = Some(value)     // then note that new max
            }
        } else {                      // max is undefined when iteration starts
            max = Some(value)         // so set it to the first value
        }
    }
    println!("max = {max:?}");
}
```

The ownership and dropping can be seen in action even with `values` being an array instead of a vector. Consider just the summing loop from the above example over an array of a structure that wraps an integer:

```rust
struct Int(i32);

impl Drop for Int {
    fn drop(&mut self) {
        println!("{} dropped", self.0)
    }
}

fn main() {
    let values = [Int(1), Int(2), Int(3), Int(4), Int(5)];
    let mut sum = 0;

    for value in values {
        sum += value.0;
    }

    println!("sum = {sum}");
}
```

`Int` implements `Drop` so that a message is printed when an instance get dropped. Running the above code will print:

    value = Int(1)
    Int(1) dropped
    value = Int(2)
    Int(2) dropped
    value = Int(3)
    Int(3) dropped
    value = Int(4)
    Int(4) dropped
    value = Int(5)
    Int(5) dropped
    sum = 15

It's clear that each value is acquired and dropped while the loop is running. Once the loop is complete, the sum is printed. If `values` in the `for` loop is changed to `&values` instead, like this:

```rust
for value in &values {
    // ...
}
```

then the output of the program will change radically:

    value = Int(1)
    value = Int(2)
    value = Int(3)
    value = Int(4)
    value = Int(5)
    sum = 15
    Int(1) dropped
    Int(2) dropped
    Int(3) dropped
    Int(4) dropped
    Int(5) dropped

This time, values are acquired but not dropped while looping because each item doesn't get owned by the interation loop's variable. The sum is printed once the loop is done. Finally, when the `values` array that still owns all the the `Int` instances goes out of scope at the end of `main`, its dropping in turn drops all the `Int` instances.

These examples demonstrate that while iterating collection types may seem to have a lot of parallels between Rust and JavaScript, from the looping constructs to the iteration abstractions, there are still subtle differences with respect to ownership that can lead to the compiler rejecting the code in some instances.

See also:

- [Iterator][iter-mod]
- [Iterating by reference]

[into-iter.rs]: https://doc.rust-lang.org/std/iter/trait.IntoIterator.html
[iter.rs]: https://doc.rust-lang.org/core/iter/trait.Iterator.html
[iter-mod]: https://doc.rust-lang.org/std/iter/index.html
[iterating by reference]: https://doc.rust-lang.org/std/iter/index.html#iterating-by-reference

## Operators

JavaScript doesn't natively support LINQ, but there is a project called [LINQ.js](https://github.com/mihaifm/linq) that implements LINQ in C# for JavaScript.

_Operators_ in LINQ are implemented in the form of LINQ.js extension methods that can be chained together to form a set of operations, with the most common forming a query over some sort of data source. LINQ.js also offers a SQL-inspired _query syntax_ with clauses like `from`, `where`, `select`, `join` and others that can serve as an alternative or a companion to method chaining. Many imperative loops can be re-written as much more expressive and composable queries in LINQ.

Rust does not offer anything like LINQ.js's query syntax. It has methods, called _[adapters]_ in Rust terms, over iterable types and therefore directly comparable to chaining of methods in LINQ.js. However, whlie rewriting an imperative loop as LINQ code in LINQ.js is often beneficial in expressivity, robustness and composability, there is a trade-off with performance. Compute-bound imperative loops _usually_ run faster because they can be optimised by the JIT compiler and there are fewer virtual dispatches or indirect function invocations incurred. The surprising part in Rust is that there is no performance trade-off between choosing to use method chains on an abstraction like an iterator over writing an imperative loop by hand. It's therefore far more common to see the former in code.

The following table lists the most common LINQ methods and their approximate counterparts in Rust.

| LINQ.js           | Rust         | Note        |
| ----------------- | ------------ | ----------- |
| `aggregate`       | `reduce`     | See note 1. |
| `aggregate`       | `fold`       | See note 1. |
| `all`             | `all`        |             |
| `any`             | `any`        |             |
| `concat`          | `chain`      |             |
| `count`           | `count`      |             |
| `elementAt`       | `nth`        |             |
| `groupBy`         | -            |             |
| `last`            | `last`       |             |
| `max`             | `max`        |             |
| `max`             | `max_by`     |             |
| `maxBy`           | `max_by_key` |             |
| `min`             | `min`        |             |
| `min`             | `min_by`     |             |
| `minBy`           | `min_by_key` |             |
| `reverse`         | `rev`        |             |
| `select`          | `map`        |             |
| `select`          | `enumerate`  |             |
| `selectMany`      | `flat_map`   |             |
| `selectMany`      | `flatten`    |             |
| `sequenceEqual`   | `eq`         |             |
| `single`          | `find`       |             |
| `singleOrDefault` | `try_find`   |             |
| `skip`            | `skip`       |             |
| `skipWhile`       | `skip_while` |             |
| `sum`             | `sum`        |             |
| `take`            | `take`       |             |
| `takeWhile`       | `take_while` |             |
| `toArray`         | `collect`    | See note 2. |
| `toDictionary`    | `collect`    | See note 2. |
| `toList`          | `collect`    | See note 2. |
| `where`           | `filter`     |             |
| `zip`             | `zip`        |             |

1. The `Aggregate` overload not accepting a seed value is equivalent to `reduce`, while the `Aggregate` overload accepting a seed value corresponds to `fold`.

2. [`collect`][collect.rs] in Rust generally works for any collectible type,    which is defined as [a type that can initialize itself from an iterator    (see `FromIterator`)][FromIter.rs]. `collect` needs a target type, which    the compiler sometimes has trouble inferring so the _turbofish_ (`::<>`) is    often used in conjunction with it, as in `collect::<Vec<_>>()`. This is why    `collect` appears next to a number of LINQ extension methods that convert    an enumerable/iterable source to some collection type instance.

  [FromIter.rs]: https://doc.rust-lang.org/stable/std/iter/trait.FromIterator.html

The following example shows how similar transforming sequences in LINQ.js is to doing the same in Rust. First in LINQ.js:

```js
let result = Enumerable.range(0, 10)
    .where(x => x % 2 === 0)
    .selectMany(x => Enumerable.Range(0, x))
    .aggregate(0, (acc, x) => acc + x);

console.log(result); // Output: 50
```

And in Rust:

```rust
let result = (0..10)
    .filter(|x| x % 2 == 0)
    .flat_map(|x| (0..x))
    .fold(0, |acc, x| acc + x);

println!("{result}"); // 50
```

[adapters]: https://doc.rust-lang.org/std/iter/index.html#adapters
[collect.rs]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.collect

## Deferred execution (laziness)

Many operators in LINQ are designed to be lazy such that they only do work when absolutely required. This enables composition or chaining of several operations/methods without causing any side-effects. <!--For example, a LINQ operator can return an `IEnumerable<T>` that is initialized, but does not produce, compute or materialize any items of `T` until iterated. The operator is said to have _deferred execution_ semantics. If each `T` is computed as iteration reaches it (as opposed to when iteration begins) then the operator is said to _stream_ the results.-->

Rust iterators have the same concept of [_laziness_][iter-laziness] and streaming.

  [iter-laziness]: https://doc.rust-lang.org/std/iter/index.html#laziness

In both cases, this allows _infinite sequences_ to be represented, where the underlying sequence is infinite, but the developer decides how the sequence should be terminated. The following example shows this in JavaScript:

```js
function* infiniteRange() {
    for (let i = 0; ; ++i) {
        yield i;
    }
}

for (let x of infiniteRange()) {
    if (x < 5) {
        console.log(x); // Prints "0 1 2 3 4"
    } else {
        break;
    }
}
```

Rust supports the same concept through infinite ranges:

```rust
// Generators and yield in Rust are unstable at the moment, so
// instead, this sample uses Range:
// https://doc.rust-lang.org/std/ops/struct.Range.html

for value in (0..).take(5) {
    print!("{value} "); // Prints "0 1 2 3 4"
}
```

## Iterator Methods (`yield`)

JavaScript has the `yield` keword that enables the developer to quickly write an _iterator method_. <!--The return type of an iterator method can be an `IEnumerable<T>` or an `IEnumerator<T>`. The compiler then converts the body of the method into a concrete implementation of the return type, instead of the developer having to write a full-blown class each time.--> _[Coroutines][coroutines.rs]_, as they're called in Rust, are still considered an unstable feature at the time of this writing.

  [coroutines.rs]: https://doc.rust-lang.org/unstable-book/language-features/coroutines.html
