---
title: rust take your first steps with rust
date: 2023-01-27 21:43:16
tags:
  - rust
  - basic
categories: rust
---

## What is Rust

### Introduction

Rust is a safe alternative to existing systems software languages like C and C++. Like C and C++, Rust doesn't have a large runtime or garbage collector, which contrasts it with almost all the other modern languages. However, unlike C and C++, Rust guarantees momery safety. Rust prevents many of the bugs related to incorrect use of memory you might see in C and C++.

With Rust, you can manage memory and control other low-level details. But you can also take advantage of high-level concepts like iteration and interfaces. These features set Rust apart from low-level languages like C and C++.

Rust also offers the following advantages that make it a great fit for a wide range of applications:

- Type safe: The compiler assures that no operation will be applied to a variable of a wrong type.

- Memory safe: Rust pointers (known as references) always refer to valid memory.

- Data race free: Rust's borrow checker guarantees thread-safety by ensuring that multiple parts of a program can't mutate the same value at the same time.

- Zero-cost abstractions: Rust allows the use of high-level concepts, like iteration, interfaces, and functional programming, with minimal to no performance costs. The abstractions perform as well, as if you wrote the underlying code by hand.

- Minimal runtime: Rust has a minimal and optional runtime. The language also has no garbage collector to manage memory efficiently. In this way, Rust is most sililar to languages like C and C++.

- Target bare metal: Rust can target embedded and "bare metal" programming, making it suitable to write an operating system kernel or device drivers.

### Unique features of Rust

Rust module system is composed of crates, modules, and paths, and tools to work with those items.

- Crates: A Rust crate is a compilation unit. It's the smallest piece of code the Rust compiler can run. The code in a crate is compiled together to create a binary executable or a library. In Rust only crates are compiled as reusable units. A crate contains a hierachy of Rust modules with an implicit, unnamed top-level module.

- Modules: Rust modules help you to organize your program by letting you manage the scope of the individual code items inside a crate. Related code items or items that are used together can be grouped into the same module. Recursive code definitions can span other modules.

- Paths: In Rust, you can use paths to name items in your code. For example, a path can be a data definition like a vector, a code function, or even a module. The module feature also helps you control the privacy of your paths. You can specify the parts of your code that are accessible publicly versus parts that are private. This feature lets you hide the implementation details.

### Use Rust crates and libraries

The Rust Standard Library `std` contains reusable code for fundamental definitions and operations in Rust programs. This library has definitions for core data types like `String` and `Vec<T>`, operations of Rust primitives, code for commonly used macro functions, support for input and output actions, and many other areas of functionality.

There are tens of thousands of libraries and third-party crates available to use in Rust programs most of which can be accessed through Rust's third-party crate repository [crates.io](https://crates.io/).

- [std](https://doc.rust-lang.org/std/) - The Rust standard library.

  - `std::collections` - Definitions for collection types, such as `HashMap`.

  - `std::env` - Functions for working with your environment.

  - `std::fmt` - Functonality to control output format.

  - `std::fs` - Functions for working with the file system.

  - `std::io` - Definitions and functionality for working with input/output.

  - `std::path` - Definitions and functions that support working with file system path data.

- [structopt](https://crates.io/crates/structopt) - A third-party crate for easily parsing command-line arguments.

- [chrono](https://crates.io/crates/chrono) - A third-party crate to handle date and time data.

- [regex](https://crates.io/crates/regex) - A third-party crate to work with regular expressions.

- [serde](https://crates.io/crates/serde) - A third-party crate of serialization and deserialization operations for Rust data structures.

By default, the `std` library is available to all Rust crates. To access the resuable code in a crate or library, we implement the `use` keyword. With the `use` keyword, the code in the crate or library is "brought into scope" so you can access the definitions and functions in you program. The standard library is accessed in `use` statements with the path `std`, as in `use std::fmt`. Other crates or libraries are accessed with their name, such as `use regex::Regex`.

### Create and manage projects with Cargo

While it's possible to use the Rust compiler (`rustc`) directly to build crates, most projects use the Rust build tool and dependency manager called **Cargo**.

Cargo does lots of things for you, including:

- Create new project templates with the `cargo new` command.

- Build a project with the `cargo build` command.

- Build and run a project with the `cargo run` command.

- Test a project with the `cargo test` command.

- Check project types with the `cargo check` command.

- Build documentation for a project with the `cargo doc` command.

- Publish a library to `crates.io` with the `cargo publish` command.

- Add dependent crates to a project by adding the crate name to the Cargo.toml file.

### The Rust playground

The playground is an IDE for Rust development that's available on the internet at `https://play.rust-lang.org/`.

In the playground, you can access methods and functions in the Rust `std` standard library. The top 100 most-downloaded crates in the `crates.io` library are also available along with dependencies.
