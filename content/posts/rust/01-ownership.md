+++
authors = ["The Unconcerned Ape"]
title = "Rust Language: Things to Remember"
description = "A brief overview of all the fundamentals of Rust"
date = "2025-01-07 17:46:30"
draft = false
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

# Rust Language: Things to Remember

Rust's memory model revolves around **ownership** and **borrowing**, with a few important rules to ensure safety and efficiency in handling memory. In this post, I'll cover the basics of **ownership**, **borrowing**, **variables**, and more, with examples for each concept.

---

## 1. Ownership and Borrowing

Rust's core concept is **ownership**, which enforces strict rules about who owns what data and when that data can be accessed or modified. This concept eliminates the need for garbage collection.

### 1.1 Ownership Rules

- **You own the thing you create**: Once you create a variable, you own the data it refers to.
  
    ```rust
    let s1 = String::from("Hello, Rust!");
    ```

- **Once you give ownership to someone else, you no longer own it**: Ownership is transferred when you assign a variable to another one.
  
    ```rust
    let s2 = s1; // Ownership of "Hello, Rust!" is transferred to s2.
    // println!("{}", s1); // This would cause an error because s1 no longer owns the data.
    ```

### 1.2 Borrowing

Rust also allows **borrowing**, which means you can temporarily give someone access to your data without giving up ownership.

- **Immutable Borrowing**: You can give a reference to the data, but the data cannot be modified.

    ```rust
    let s1 = String::from("Hello, Borrowing!");
    let s2 = &s1; // s2 is an immutable reference to s1.
    println!("{}", s2); // Works fine because we are only reading from s1.
    ```

- **Mutable Borrowing**: You can allow someone to modify your data using a mutable reference. However, Rust ensures only one mutable reference exists at a time to prevent data races.

    ```rust
    let mut s1 = String::from("Hello, Mutable Borrowing!");
    let s2 = &mut s1; // Mutable reference
    s2.push_str(" Modified!");
    println!("{}", s2); // Output: Hello, Mutable Borrowing! Modified!
    ```

---

## 2. Variables

In Rust, variables are **immutable by default**. You must explicitly make them mutable if you want to change their value.

### 2.1 Immutable Variables

By default, all variables are immutable, meaning once a value is assigned, it cannot be changed.

```rust
let x = 5; 
// x = 6; // This would cause a compile-time error
```

### 2.2 Mutable Variables

You can create mutable variables by adding the `mut` keyword before the variable name.

```rust
let mut y = 10;
y = 20; // This is fine because y is mutable
println!("{}", y); // Output: 20
```

### 2.3 Copying and Cloning Data

In Rust, **stack-allocated types** like `i32` and `f32` are **copied** when you assign them to another variable. For **heap-allocated types** like `String`, you need to use `.clone()` to create a copy.

- **Copying stack-allocated types**:

    ```rust
    let x: i32 = 42;
    let y = x; // x is copied into y, and both are independent.
    ```

- **Cloning heap-allocated types**:

    ```rust
    let s1 = String::from("Hello, Clone!");
    let s2 = s1.clone(); // A deep copy of s1 is made, so s2 is independent.
    ```

---

## 3. More on Borrowing

Rust has additional rules to ensure safe memory access, especially when references are involved.

### 3.1 References and Dereferencing

When you have a reference to data, you can access its value using dereferencing.

- **Immutable references**:

    ```rust
    let s = String::from("Hello, Rust!");
    let s_ref = &s; // Immutable reference to s
    println!("{}", s_ref); // Dereferencing to access the value.
    ```

- **Mutable references**:

    ```rust
    let mut s = String::from("Hello, Mutable Rust!");
    let s_ref = &mut s; // Mutable reference to s
    s_ref.push_str(" Now modified!");
    println!("{}", s_ref); // Dereferencing to access the value.
    ```

### 3.2 Dangling References

Rust ensures that references cannot outlive the data they refer to, preventing **dangling references** (i.e., references to freed memory).

---

## Conclusion

Rust's ownership and borrowing system enforces memory safety without needing a garbage collector, making it both efficient and reliable. The language’s rules around variables and borrowing help prevent bugs that are common in other languages, such as memory leaks and data races.

If you're just getting started with Rust, understanding these fundamentals will go a long way in helping you write safe and efficient code. Happy coding! 🚀

