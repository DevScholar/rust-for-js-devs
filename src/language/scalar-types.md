# Scalar Types

The following table lists the primitive types in Rust and their equivalent in JavaScript:

| Rust    | JavaScript                | Note        |
| ------- | ------------------------- | ----------- |
| `bool`  | `boolean`                 |             |
| `char`  | `string`                  | See note 1. |
| `i8`    | `number`                  | See note 2. |
| `i16`   | `number`                  |             |
| `i32`   | `number`                  |             |
| `i64`   | `number`/`bigint`         |             |
| `i128`  | `number`/`bigint`         |             |
| `isize` | `Number.MAX_SAFE_INTEGER` |             |
| `u8`    | `number`                  |             |
| `u16`   | `number`                  |             |
| `u32`   | `number`                  |             |
| `u64`   | `number`/`bigint`         |             |
| `u128`  | `number`/`bigint`         |             |
| `usize` | `Number.MAX_SAFE_INTEGER` |             |
| `f32`   | `number`/`bigdecimal`     |             |
| `f64`   | `number`/`bigdecimal`     |             |
|         | `number`                  |             |
| `()`    | `null`                    |             |
|         | `undefined`               |             |
|         | `object`                  | See note 3. |

Notes:

1. [`char`][char.rs] in Rust and [`string`][string.js] in JavaScript have different    definitions. In Rust, a `char` is 4 bytes wide that is a [Unicode scalar    value], but in JavaScript, a character is 2 bytes wide and stores the character    using the UTF-16 encoding. There is no `char` type equivalent in JavaScript, only `string`. For more information, see the [Rust `char`    documentation][char.rs].  
2.  There are only three number data type in JavaScript, `number`, which is essentially a floating point number. And the `bigint` type for storing numbers that exceed the range -(2<sup>53</sup> - 1) (`Number.MIN_SAFE_INTEGER`) to 2<sup>53</sup> - 1 (`Number.MAX_SAFE_INTEGER`). and the `bigdecimal` type for storing high-precision decimals.  
3. For historical reasons, JavaScript has two empty data types: `null` and `undefined`. `undefined` denotes a value that was never created, and null denotes a value that was created but intentionally left empty. 
See also:

- [Primitives (Rust By Example)][primitives.rs]

[string.js]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type
[char.rs]: https://doc.rust-lang.org/std/primitive.char.html
[Unicode scalar value]: https://www.unicode.org/glossary/#unicode_scalar_value
[primitives.rs]: https://doc.rust-lang.org/rust-by-example/primitives.html
