##  `if let` bilan  Control Flow

`if let` sintaksisi sizga `if` va `let` ni birlashtirib, qolganlarini e'tiborsiz qoldirib, bitta patternga mos keladigan qiymatlarni boshqarishning kamroq batafsil metodiga imkon beradi. 6-6 ro'yxatdagi dasturni ko'rib chiqaylik, u `max_sozlama` o'zgaruvchisidagi `Variant<u8>` qiymatiga mos keladigan, lekin `Some` varianti boʻlsagina kodni bajarishni xohlaydigan dasturni koʻrib chiqamiz.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-06/src/main.rs:here}}
```

<span class="caption">Ro'yxat 6-6. Qiymat `Some` bo'lsagina kodni bajaradigan `match` ifoda</span>

Agar qiymat `Some` bo'lsa, biz qiymatni patterndagi `max` o'zgaruvchisiga bog'lash orqali `Some` variantidagi qiymatni chop qilamiz. Biz `None` qiymati bilan hech narsa qilishni xohlamaymiz. `match` ifodasini qondirish uchun faqat bitta variantni qayta ishlagandan so‘ng `_ => ()` qo‘shishimiz kerak, bu esa qo‘shish uchun zerikarli boilerplate koddir.

Buning o'rniga, biz buni qisqaroq qilib `if let` yordamida yozishimiz mumkin. Quyidagi kod 6-6 ro'yxatdagi `match` bilan bir xil ishlaydi:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-12-if-let/src/main.rs:here}}
```

The syntax `if let` takes a pattern and an expression separated by an equal
sign. It works the same way as a `match`, where the expression is given to the
`match` and the pattern is its first arm. In this case, the pattern is
`Some(max)`, and the `max` binds to the value inside the `Some`. We can then
use `max` in the body of the `if let` block in the same way we used `max` in
the corresponding `match` arm. The code in the `if let` block isn’t run if the
value doesn’t match the pattern.

Using `if let` means less typing, less indentation, and less boilerplate code.
However, you lose the exhaustive checking that `match` enforces. Choosing
between `match` and `if let` depends on what you’re doing in your particular
situation and whether gaining conciseness is an appropriate trade-off for
losing exhaustive checking.

In other words, you can think of `if let` as syntax sugar for a `match` that
runs code when the value matches one pattern and then ignores all other values.

We can include an `else` with an `if let`. The block of code that goes with the
`else` is the same as the block of code that would go with the `_` case in the
`match` expression that is equivalent to the `if let` and `else`. Recall the
`Coin` enum definition in Listing 6-4, where the `Quarter` variant also held a
`UsState` value. If we wanted to count all non-quarter coins we see while also
announcing the state of the quarters, we could do that with a `match`
expression, like this:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-13-count-and-announce-match/src/main.rs:here}}
```

Or we could use an `if let` and `else` expression, like this:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-14-count-and-announce-if-let-else/src/main.rs:here}}
```

If you have a situation in which your program has logic that is too verbose to
express using a `match`, remember that `if let` is in your Rust toolbox as well.

## Summary

We’ve now covered how to use enums to create custom types that can be one of a
set of enumerated values. We’ve shown how the standard library’s `Option<T>`
type helps you use the type system to prevent errors. When enum values have
data inside them, you can use `match` or `if let` to extract and use those
values, depending on how many cases you need to handle.

Your Rust programs can now express concepts in your domain using structs and
enums. Creating custom types to use in your API ensures type safety: the
compiler will make certain your functions only get values of the type each
function expects.

In order to provide a well-organized API to your users that is straightforward
to use and only exposes exactly what your users will need, let’s now turn to
Rust’s modules.

