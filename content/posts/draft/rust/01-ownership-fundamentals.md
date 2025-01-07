+++
authors = ["The Unconcerned Ape"]
title = "Rust Language: Things to Remember"
description = "A brief overview of all the fundamentals of Rust in no particular order"
date = "2025-01-07"
draft = true
tags = [
    "rust",
    "programming"
]
categories = [
    "rust",
    "rust-fundamentals"
]
series = ["rust-lang"]
+++

## a. Ownership and Borrowing

1. You own the thing you create and if you give it to someone else, you no longer own it.
2. If someone borrows (giving) it, you can’t use it anymore (*a.1*) and you cease to exist.
3. If someone references it, you can’t modify it anymore. You lose the write permission on it.
4. If you send it to another function, they are borrowing it (*a.2*) so you no longer exist.
5. If you want them to reference you, you can send them your address &.
6. If you create a slice of something, you can't modify the original (*a.3*).
7. If references of a variable exist, you can drop it.
8. Data always stays longer than all of its reference.

## b. Variables

1. Everything is immutable by default.
2. You can mutate something by using `mut`.
3. You can `let` create copies of you normally if you are on the stack:
    - `let a: i32 = b` copies the value of b (*b.2*).
    - The same thing for `&str`, `f32`, and such as they all are on the stack.
4. You can `let` others copy you only by using the `clone()` function if you are on the heap:
    - `let a: String = String::from("hello");`
        - `let b = a` here `b` borrows the value of `a` (*a.2*) and now `a` no longer exists.
        - `let b = a.clone()` can be used to create a new copy with the value.
        - Things on the heap like `Strings`, `Tuples`, `Vectors`, and such are on the heap.
5. You can pass the current value instead of copying it by letting it borrow:
    - In `let b = a` (*b.4.1*), `b` now owns the `String`.
6. You can create a reference (*a.5*) to the variable by passing it your address:
    - `let b = &a` holds the reference to `a`.
    - In `b.6.a`, `a` loses write permission but still owns it, and both can be used in the program.
7. Mutable variables are still immutable by the reference unless specified:
    - `let mut a: String = String::from("hello");`
        1. **Immutable reference**:
            - `let b: &String = &a` is an immutable reference, so functions with write requirements (`push_str()`) won't work.
        2. **Mutable reference**:
            - Can be created by using `&mut`.
            - `let b: &mut String = &mut a` is a mutable reference, so functions with write requirements will work on the reference.

