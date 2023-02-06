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

## Set up the Rust development environment

### Install Visual C++ build tools

Rust requires the Microsoft C++ build tools for Visual Studio 2013 or later. These build tools must be installed before you can install Rust.

If you don't have the build tools installed, follow these steps:

1. Go to the [Microsoft Visual Studio download page](https://visualstudio.microsoft.com/visual-cpp-build-tools/).

2. Select Download Build Tools.

3. After the download completes, run the installer file. The Visual Studio Installer window opens.

4. In the popup dialog, select Yes. In the next popup dialog, select Continue.

5. In the Installer window, under Desktop & Mobile, select the checkbox for the C++ build tools option on the left.

6. In the Installer details pane on the right, make sure the following options are selected:

   - C++ Build tools core features

   - C++ 2019 Redistributable Update

   - C++ core desktop features

   - MSVC v142 - VS 2019 C++ x64/x86 build tools

   - Windows 10 SDK (10.0.19041.0)

   - C++ CMake tools for Windows

   - Testing tools core features - Build Tools

   - C++ AddressSanitizer

7. At the bottom right, select Install.

After the installation completes, you can continue with the Rust intallation.

### Install Rust

The recommended way to install Rust is to use `rustup`, the Rust toolchain installer. Go to the website [rustup.rs](https://rustup.rs/) to find the appropriate instructions for your operating system.

Rust has a six-week, rapid release process, and supports a great number of platforms, so there are many builds of Rust available at any time. If you've installed `rustup` in the past, you can update to the latest stable version of Rust by running the command `rustup update`.

### Check the Rust installation

After you compete the Rust installation, you should have the `rustc` and `cargo` commands available.

Run the following command in your terminal or command prompt:

```cmd
rustc --version
```

```cmd
cargo --version
```

Both output lines contain the following information on the latest stable versions of Rust and Cargo that are available:

- The release number

- The commit hash

- The commit date

This information appears in the following format:

`<executable-name> <three-part-release-number> (<9-character-hash-code> <4-digit-year>-<2-digit-month>-<2-digit-day>)`

If you see this type of output, both installations were successful. If you don't see this information, check you `PATH` environment variable. Make sure it includes a folder that contains the `rustc.exe` and `cargo.exe` executable files.

### Exercise: Hello world

#### Create a new directory to organize your code

For the Windows command prompt, run the following command:

```cmd
mkdir "%USERPROFILE%\rust-learning"
cd /d "%USERPROFILE%\rust-learning"
mkdir hello-world
cd hello-world
```

#### Write your first Rust program

Next, create a new file named `main.rs` and use your editor to write the following code into it:

```rust
fn main() {
  printLn!("Hello, world!");
}
```

#### Compile and run your program

Your source code is ready. Now it's time to compile your program into an executable file. Return to your terminal window and enter the following commands to compile and run the file.

```cmd
rustc main.rs
.\main.exe
```

You should see the following output:

```cmd
Hello, world!
```

#### Create a project with Cargo

Make sure your terminal is at your `rust-learning` directory, and run the following command:

```cmd
cargo new hello-cargo
```

This command generates a new directory named _hello-cargo_ with a src subdirectory and adds two files:

> - hello-cargo/
>   - Cargo.toml
>   - src/
>     - main.rs

- The _Cargo.toml_ file is the manifest file for Rust. It's where you keep metadata for your project and also any denpendencies.

- The _main.rs_ file in the src subdirectory is where you'll write your application code.

Notice that the `cargo new` command generated a boilerplate "Hello, world!" project for you.

#### Build and run your program with Cargo

To execute the boilerplate program, we'll move into the directory _hello-cargo_, and then use the `cargo run` command.

```cmd
cd hello-cargo
cargo run
```

You should see the output in your terminal. Cargo has built and executed your executable.

## Create your first Rust program

### The Rust Playground

The [Rust Playground](https://play.rust-lang.org/) is a browser interface to the Rust compiler. You can use the Playground to experiment writing Rust code before you install the language locally, or you might not have the compiler available.

### Understand the basic Rust program structure

A function is a block of code that does a specific task. We sepearate the code in our program into blocks based on task. This separation makes the code easier to understand and maintain. After we define a function for a task, we can call the function when we need to do that task.

Every Rust program must have one function named `main`. The code in the `main` function is always the first code run in a Rust program.

```rust
fn main() {
  println!("Hello, world!");
}
```

To declare a function in Rust, we use the `fn` keyword. After the function name, we tell the compiler how many parameters or arguments the function expects as input. The arguments are listed inside the parentheses `()`. The function body is the code that does the task of the function and is defined inside curly brackets `{}`. It's a best practice to format the code so the opening curly bracket for the function body appears right after the argument list in parentheses.

#### Code indentation

In the function body, most code statements end with a semicolon `;`. Rust processes these statements one after the other in order. When a code statement doesn't end with a semicolon, Rust knows the next line of code must be executed before the starting statement can be complete.

To help see the execution relationships in the code, we use indentation. This format shows how the code is organized and reveals the flow of steps to complete the function task. A starting code statement is indented four spaces from the left margin. When the code doesn't end in a semicolon, the next line of code to execute is indented four more spaces.

```rust
fn main() { // The function declaration is not indented

    // First step in function body
        // Substep: execute before First step can be complete

    // Second step in function body
        // Substep A: execute before Second step can be complete
        // Substep B: execute before Second step can be complete
            // Sub-substep 1: execute before Substep B can be complete

    // Third step in function body, and so on...
}
```

#### The todo! macro

A macro in Rust is a like a function that takes a variable number of input arguments. The `todo!` macro is used to identify unfinished code in the Rust program. The macro is helpful for prototyping, or when you want to indicate behavior that isn't complete.

```rust
fn main() {
  todo!("Display the message by using the println!() macro");
}
```

When you compile code that uses the `todo!` macro, the compiler can return a panic message where it expects to find completed functionality.

#### The println! macro

Our `main` function does one task. It calls the `println!` macro that's predefined in Rust. The `println!` macro expects one or more input arguments, which it displays to the screen or standard output.

```rust
fn main() {
  println!("Hello, world!");
}
```

#### Value substitution for {} arguments

In the Rust Learn module lessons, we often call the `println!` macro with a list of arguments that includes text strings with instances of curly brackets `{}` and other values. The `println!` macro replaces each instance of curly brackets `{}` inside a text string with the value of the next argument in the list.

```rust
fn main() {
  // Call println! with three arguments: a string, a value, a value
  println!("The first letter of the English alphabet is {} and the last letter is {}.", 'A', 'Z');
}

// Output
// The first letter of the English alphabet is A and the last letter is Z.
```

### Create and use variables in Rust

We use variables to store our data in a named reference that we can refer to later in our code.

#### Variables

In Rust, a variable is dclared with the keyword `let`. Each variable has a unique name. When a variable is declared, it can be bound to a value, or the value can be bound later in the program.

```rust
let a_number;
```

The `a_number` variable isn't yet bound to a value. We can modify this statement to bind a value to the variable:

```rust
let a_number = 10;
```

> Note
>
> Keywords As with other programming languages, certain keywords like `fn` and `let` are reserved for use only by Rust. Keywords can't be used as names of functions or variables.

```rust
// Declare a variable
let a_number;

// Declare a second variable and bind the value
let a_word = "Ten";

// Bind a value to the first variable
a_number = 10;

println!("The number is {}.", a_number);
println!("The word is {}.", a_word);
```

If we call the `println!` macro and try to show the value of the `a_number` variable before it's bound, the compiler returns an error.

#### Immutable versus mutable

In Rust, variable bindings are immutable by default. When a variable is immutable, after a value is bound to a name, you can't change that value.

For instance, if we try to change the value of the `a_number` variable from the previous example, we receive an error message from the compiler.

To mutate a value, we must first use the `mut` keyword to make a variable binding mutable.

```rust
// The `mut` keyword lets the variable be changed
let mut a_number = 10;
println!("The number is {}.", a_number);

// Change the value of an immutable variable
a_number = 15;
println!("Now the number is {}.", a_number);
```

#### Variable shadowing

You can declare a new variable that uses the name of an existing variable. The new declaration creates a new binding. In Rust, this operation is called "shadowing" because the new variable shadows the previous variable. The old variable still exists, but you can't refer to it in this scope anymore.

```rust
// Declare first variable binding with name "shadow_num"
let shadow_num = 5;

// Declare second variable binding, shadows existing variable "shadow_num"
let shadow_num = shadow_num + 5;

// Declare third variable binding, shadows second binding of variable "shadow_num"
let shadow_num = shadow_num * 2;

println!("This number is {}.", shadow_num);
```

### Explore data types for numbers, text, and true/false values

Rust is a statically typed language. The compiler must know the exact data type for all variables in your code for your program to compile and run. The compiler can usually infer the data type for a variable based on the bound value. You don't always need to explicitly tell the type in your code. When many types are possible, you must inform the compiler the specific type by using type annotations.

In the following example, we tell the compiler to create the `number` variable as a 32-bit integer. We specify the data type `u32` after the variable name. Notice the use of the colon `:` after the variable name.

```rust
let number: u32 = 14;
println!("The number is {}.", number);
```

If we enclose the variable value in double quotation marks, the compiler interprets the value as text rather than a number. The inferred data type of the value doesn't match the `u32` data type specified for the variable, so the compiler issues an error:

```rust
let number: u32 = "14";
```

#### Built-in data types

Rust comes with some built-in primitive data types to express numbers, text, and truth. Several of these types are referred to as scalar because they represent a single value:

- Integer numbers

- Floating point numbers

- Booleans

- Characters

Rust also offers more complex data types to work with data series, such as string and tuple values.

#### Numbers: Integers and floating point values

Integers in Rust are identified by bit size and the signed property. A signed integer can be a positive or negative number. An unsigned integer can be only a positive number.

| Length                 | Signed  | Unsigned |
| ---------------------- | ------- | -------- |
| 8-bit                  | `i8`    | `u8`     |
| 16-bit                 | `i16`   | `u16`    |
| 32-bit                 | `i32`   | `u32`    |
| 64-bit                 | `i64`   | `u64`    |
| 128-bit                | `i128`  | `u128`   |
| architecture-dependent | `isize` | `usize`  |

The `isize` and `usize` types depend on the kind of computer your program is running on. The 64-bit type is used on a 64-bit architecture, and the 32-bit type on a 32-bit architecture. If you don't specify the type for an integer, and the system can't infer the type, it assigns the `i32` type (a 32-bit signed integer) by default.

Rust has two floating-point data types for decimal values: `f32` (32 bits) and `f64` (64 bits). The default floating-point type is `f64`. On modern CPUs, the `f64` type is roughly the same speed as the `f32` type, but it has a greater precision.

```rust
let number_64 = 4.0; // compiler infers the value to use the default type f64

let number_32: f32 = 5.0; // type f32 specified via annotation
```

All of the primitive number types in Rust support mathematical operations like addition, subtraction, multiplication, and division.

```rust
// Addition, Subtraction, and Multiplication
println!("1 + 2 = {} and 8 - 5 = {} and 15 * 3 = {}", 1u32 + 2, 8i32 - 5, 15 * 3);

// Integer and Floating point division
println!("9 / 2 = {} but 9.0 / 2.0 = {}", 9u32 / 2, 9.0 / 2.0);
```

> Note
>
> When we call the `println!` macro, we add the data type suffix to each literal number to inform Rust about the data type. The syntax `1u32` tells the compiler the value is the number 1 and to interpret the value as an unsigned 32-bit integer.
>
> If we don't provide type annotations, Rust tries to infer the type from the context. When the context is ambiguous, it assigns the `i32` type (a 32-bit signed integer) by default.

#### Booleans: True or false

The boolean type in Rust is used to store truth. The `bool` type has two possible values: `true` or `false`. Boolean values are used widely in conditional expressions. If a `bool` statement or value is true, then do this action; otherwise (the statement or value is false), do a different action. A boolean value is often returned by a comparison check.

In the following example, we use the greater than `>` operator to test two values. The operator returns a boolean value that shows the result of the test.

```rust
// Declare variable to store result of "greater than" test, Is 1 > 4? -- false
let is_bigger = 1 > 4;
println!("Is 1 > 4? {}", is_bigger);
```

#### Text: Characters and strings

Rust supports text values with two basic string types and one character type. A character is a single item, while a string is a series of characters. All text types are valid UTF-8 representations.

**Characters**

The `char` type is the most primitive of the text types. The value is specified by enclosing the item in single quotation marks:

```rust
let uppercase_s = 'S';
let lowercase_f = 'f';
let smiley_face = '😃';
```

> Note
>
> Some languages treat their `char` types as 8-bit unsigned integers, which is the equivalent of the Rust `u8` type. The `char` type in Rust contains unicode code points, but they don't use UTF-8 encoding. A `char` in Rust is a 21-bit integer that's padded to be 32 bits wide. The `char` contains the plain code point value directly.

**Strings**

The `str` type, also known as a string slice is a view into string data. Most of the time, we refer to these types by using reference-style syntax that precedes the type with the ampersand `&str`. We'll cover references in the following modules. For now, you can think of `&str` as a pointer to immutable string data. String literals are all of type `&str`.

Although string literals are convenient to use in introductory Rust examples, they aren't suitable for every situation where we might want to use text. Not every string can be known at compile time. An example is when a user interacts with a program during runtime and sends text via a terminal.

For these scenarios, Rust has a second string type named `String`. This type is allocated on the heap. When you use the `String` type, the length of the string (number of characters) doesn't need to be known before the code is compiled.

> Note
>
> If you're familiar with a garbage-collected language, you might be wondering why Rust has two string types. Strings are extremely complex data types. Most languages use their garbage collectors to gloss over this complexity. Rust as a system's language exposes some of the inherent complexity of strings. With the added complexity comes a very fine-grained amount of control over how memory is used in your program.
>
> Actually, Rust has more than two string types. In this module, we cover only the `String` and `&str` types. You can learn more about the string types offered in the [Rust documentation](https://doc.rust-lang.org/book/ch08-02-strings.html).

We won't get a full idea of the difference between `String` and `&str` until we learn about Rust's ownership and borrowing system. Until then, you can think of `String` type data as text data that can change as your program runs. The `&str` references are immutable views into the text data that don't change as your program runs.

```rust
// Specify the data type "char"
let character_1: char = 'S';
let character_2: char = 'f';

// Compiler interprets a single item in quotations as the "char" data type
let smiley_face = '😃';

// Compiler interprets a series of items in quotations as a "str" data type and creates a "&str" reference
let string_1 = "miley ";

// Specifiy the data type "str" with the reference syntax "&str"
let string_2: &str = "ace";

println!("{} is a {}{}{}{}.", smiley_face, character_1, string_1, character_2, string_2);
```

### Define data collections by using tuples and structs

#### Tuples

A tuple is a grouping of values of different types collected into one compound value. The individual values in a tuple are called elements. The values are specified as a comma-separated list enclosed in parentheses `(<value>, <value>, ...)`.

A tuple has a fixed length, which is equal to its number of elements. After a tuple is declared, it can't grow or shrink in size. Elements can't be added or removed. The data Type of a tuple is defined by the sequence of the data types of the elements.

#### Define a tuple

```rust
// Tuple of length 3
let tuple_e = ('E', 5i32, true);
```

The type signature for this tuple is defined by the sequence of the types for the three elements: `(char, i32, bool)`.

#### Access elements in a tuple

The elements in a tuple can be accessed by index position starting from zero. This process is referred to as tuple indexing. To access an element in a tuple, we use the syntax `<tuple>.<index>`.

```rust
// Declare a tuple of three elements
let tuple_e = ('E', 5i32, true);

// Use tuple indexing and show the values of the elements in the tuple
println!("Is '{}' the {}th letter of the alphabet? {}", tuple_e.0, tuple_e.1, tuple_e.2);

// Output
// Is 'E' the 5th letter of the alphabet? true
```

Tuples are useful when you want to combine different types into a single value. Functions can use tuples to return multiple values because tuples can hold any number of values.

#### Structs

A struct is a type that's composed of other types. The elements in a struct are called fields. Like tuples, the fields in a struct can have different data types. A significant benefit of the struct type is that you can name each field so it's clear what the value means.

To work with structs in a Rust program, first you define the struct by name and specify the data type for each field. Then, you create an instance of the struct with another name. When you declare the instance, you provide the specific values for the fields.

Rust supports three struct types: classic structs, tuple structs, and unit structs. These struct types support different ways to group and work with the data.

- **Classic [C structs](<https://wikipedia.org/wiki/Struct_(C_programming_language)>)** are the most commonly used. Each field in the struct has a name and a data type. After a classic struct is defined, the fields in the struct can be accessed by using the syntax `<struct>.<field>`.

- **Tuple structs** are similar to classic structs, but the fields don't have names. To access the fields in a tuple struct, we use the same syntax as we do for indexing a tuple: `<tuple>.<index>`. As with tuples, the index values in the tuple struct start at zero.

- **Unit structs** are most commonly used as markers. We'll learn more about why unit structs are useful when we learn about Rust's traits feature.

```rust
// Classic struct with named fields
struct Student { name: String, level: u8, remote: bool }

// Tuple struct with data types only
struct Grades(char, char, char, char, f32);

// Unit struct
struct Unit;
```

#### Define a struct

To define a struct, enter the `struct` keyword followed by a name for the struct. Choose a name for the struct type that describes the significant characteristic of the grouped data. Unlike the naming convention that we've used so far, the name of a struct type is capitalized.

Struct types are often defined outside of the `main` function and other functions in the Rust program. For this reason, the start of the struct definition isn't indented from the left margin. Only the inside portion of the definition is indented to show how the data is organized.

**Classic struct**

Like a function, the body of a classic struct is defined inside curly brackets `{}`. Each field in the classic struct is given a name that's unique within the struct. The type for each field is specified with the syntax `: <type>`. The fields in the classic struct are specified as a comma-separated list `<field>, <field>, ...`. A classic struct definition doesn't end with a semicolon.

A benefit of the classic struct definition is you can access the value for a struct field by name. To access the field value, we use the syntax `<struct>.<field>`.

**Tuple struct**

Like a tuple, the body of a tuple struct is defined inside parentheses `()`. The parentheses immediately follow the struct name. There's no space between the struct name and the opening parentheses.

Unlike a tuple, the tuple struct definition contains only the data type for each field. The data types in the tuple struct are specified as a comma-separated list `<type>, <type>, ...`.

#### Instantiate a struct

After you define a struct type, you use the struct by creating an instance of the type and specifying values for each field. When you set the field values, you don't need to specify the fields in the same order as they're defined.

The following example uses the definitions that we created for the Student and Grades struct types.

```rust
// Instantiate classic struct, specify fields in random order, or in specified order
let user_1 = Student { name: String::from("Constance Sharma"), remote: true, level: 2 };
let user_2 = Student { name: String::from("Dyson Tan"), level: 5, remote: false };

// Instantiate tuple structs, pass values in same order as types defined
let mark_1 = Grades('A', 'A', 'B', 'A', 3.75);
let mark_2 = Grades('B', 'A', 'A', 'C', 3.25);

println!("{}, level {}. Remote: {}. Grades: {}, {}, {}, {}. Average: {}", user_1.name, user_1.level, user_1.remote, mark_1.0, mark_1.1, mark_1.2, mark_1.3, mark_1.4);
println!("{}, level {}. Remote: {}. Grades: {}, {}, {}, {}. Average: {}", user_2.name, user_2.level, user_2.remote, mark_2.0, mark_2.1, mark_2.2, mark_2.3, mark_2.4);
```

#### Convert a string literal to a String type

String data that's stored inside another data structure, such as a struct or vector, must be converted from a string literal reference (`&str`) to a `String` type. To do the conversion, we use the standard `String::from(&str)` method.

If we don't convert the type before we assign the value, the compiler issues an error.

```txt
error[E0308]: mismatched types
  --> src/main.rs:24:15
   |
24 |         name: "Dyson Tan",
   |               ^^^^^^^^^^^
   |               |
   |               expected struct `String`, found `&str`
   |               help: try using a conversion method: `"Dyson Tan".to_string()`

error: aborting due to previous error
```

The compiler suggests that we can use the `.to_string()` function to make the conversion. In our examples, we use the `String::from(&str)` method.

### Use enum variants for compound data

Enums are types that can be any one of several variants. What Rust calls enums are more commonly known as algebraic data types(ADT). The important detail is that each enum variant can have data to go along with it.

We use the `enum` keyword to create an enum type, which can have any combination of the enum variants. Like structs, enum variants can have named fields, but they can also have fields without names, or no fields at all. Like struct types, enum types are also capitalized.

#### Define an enum

Each variant in the enum is independent and stores different amounts and types of values.

```rust
enum WebEvent {
  // An enum variant can be like a unit struct without fields or data types
  WELoad,
  // An enum variant can be like a tuple struct with data types but no name fields
  WEKeys(String, char),
  // An enum variant can be like a classic struct with named fields and their data types
  WEClick { x: i64, y: i64 }
}
```

We define an enum with variants similar to how we define different kinds of struct types. All the variants are grouped together in the same `WebEvent` enum type. Each variant in the enum isn't its own type. Any function that uses a variant of the `WebEvent` enum must accept all the variants in the enum. We can't have a function that accepts only the `WEClick` variant, but not the other variants.

#### Define an enum with structs

A way to work around enum variant requirements is to define a separate struct for each variant in the enum. Then, each variant in the enum uses the corresponding struct. The struct holds the same data that was held by the corresponding enum variant. This style of definition allows us to refer to each logical variant on its own.

```rust
// Define a tuple struct
struct KeyPress(String, char);

// Define a classic struct
struct MouseClick { x: i64, y: i64 }

// Redefine the enum variants to use the data from the new structs
// Update the page Load variant to have the boolean type
enum WebEvent { WELoad(bool), WEClick(MouseClick), WEKeys(KeyPress) }
```

#### Instantiate an enum

Now let's add code to create instances of our enum variants. For each variant, we use the `let` keyword to make the assignment. To access the specific variant in the enum definition, we use the syntax `<enum>::<variant>` with double colons `::`.

**Simple variant: WELoad(bool)**

The first variant in the `WebEvent` enum has a single boolean value, `WELoad(bool)`. We instantiate this variant in a manner similar to how we worked with booleans in the previous unit:

```rust
let we_load = WebEvent::WELoad(true);
```

**Struct variant: WEClick(MouseClick)**

The second variant contains a classic struct `WEClick(MouseClick)`. The struct has two named fields `x` and `y`, and both fields have the `i64` data type. To create this variant, first we instantiate the struct. Then we pass the struct as an argument in the call to instantiate the variant.

```rust
// Instantiate a MouseClick struct and bind the coordinate values
let click = MouseClick { x: 100, y: 250 };

// Set the WEClick variant to use the data in the click struct
let we_click = WebEvent::WEClick(click);
```

**Tuple variant: WEKeys(KeyPress)**

The last variant contains a tuple `WEKeys(KeyPress)`. The tuple has two fields that use the `String` and `char` data types. To create this variant, first we instantiate the tuple. Then we pass the tuple as an argument in the call to instantiate the variant.

```rust
// Instantiate a KeyPress tuple and bind the key values
let keys = KeyPress(String::from("Ctrl+"), 'N');

// Set the WEKeys variant to use the data in the keys tuple
let we_key = WWebEvent::WEKeys(keys);
```

#### Enums example

```rust
// Define a tuple struct
#[derive(Debug)]
struct KeyPress(String, char);

// Define a classic struct
#[derive(Debug)]
struct MouseClick { x: i64, y: i64 }

// Define the WebEvent enum variants to use the data from the structs
// and a boolean type for the page Load variant
#[derive(Debug)]
enum WebEvent { WELoad(bool), WEClick(MouseClick), WEKeys(KeyPress) }

fn main() {
    // Instantiate a MouseClick struct and bind the coordinate values
    let click = MouseClick { x: 100, y: 250 };
    println!("Mouse click location: {}, {}", click.x, click.y);

    // Instantiate a KeyPress tuple and bind the key values
    let keys = KeyPress(String::from("Ctrl+"), 'N');
    println!("\nKeys pressed: {}{}", keys.0, keys.1);

    // Instantiate WebEvent enum variants
    // Set the boolean page Load value to true
    let we_load = WebEvent::WELoad(true);
    // Set the WEClick variant to use the data in the click struct
    let we_click = WebEvent::WEClick(click);
    // Set the WEKeys variant to use the data in the keys tuple
    let we_key = WebEvent::WEKeys(keys);

    // Print the values in the WebEvent enum variants
    // Use the {:#?} syntax to display the enum structure and data in a readable form
    println!("\nWebEvent enum structure: \n\n {:#?} \n\n {:#?} \n\n {:#?}", we_load, we_click, we_key);
}
```

#### Debug statements

In the previous example, look for the following code statement. This statement is used in several places in the code.

```rust
// Set the Debug flag so we can check the data in the output
#[derive(Debug)]
```

The `#[derive(Debug)]` syntax lets us see certain values during the code execution that aren't otherwise viewable in standard ouput. To view debug data with the `println!` macro, we use the syntax `{:#?}` to format the data in a readable manner.

### Work with functions in Rust

Functions are the primary way code is executed within Rust. You've already seen one of the most important functions in the language, the `main` function. In this unit, we'll cover more of the details about how to define functions.

#### Define a function

Function definitions in Rust start with the `fn` keyword. After the function name, we specify the function's input arguments as a comma-separated list of data types inside parentheses. The curly brackets tell the compiler where the function body begins and ends.

```rust
fn main() {
  println!("Hello, world!");
  goodbye();
}

fn goodbye() {
  println!("Goodbye.");
}
```

We call a function by using its name along with its input arguments in parentheses. If a function doesn't have any input arguments, we leave the parentheses empty. In our example, both the `main` and `goodbye` functions have no input arguments.

You might have noticed that we defined the `goodbye` function after the `main` function. We could have defined the `goodbye` function before we defined `main`. Rust doesn't care where in the file you define your functions, as long as they're defined somewhere in the file.

#### Pass input arguments

When a function has input arguments, we name each argument and specify the data type at the start of the function declaration. Because arguments are named like variables, we can access the arguments in the function body.

Let's modify our `goodbye` function to take a pointer to some string data as an input argument.

```rust
fn goodbye(message: &str) {
  println!("\n{}", message);
}

fn main() {
  let formal = "Formal: Goodbye.";
  let casual = "Casual: See you later!";
  goodbye(formal);
  goodbye(casual);
}
```

#### Return a value

When a function returns a value, we add the syntax `-> <type>` after the list of function arguments and before the opening curly bracket for the function body. The arrow syntax `->` indicates that the function returns a value to the caller. The `<type>` portion lets the compiler know the data type of the value returned.

In Rust, the common practice is to return a value at the end of a function by having the last line of code in the function be equal to the value to return. The following example shows this behavior. The `divide_by_5` function returns the result of dividing the input number by 5 to the calling function:

```rust
fn divide_by_5(num: u32) -> u32 {
  num /5
}

fn main() {
  let num = 25;
  println!("{} divided by 5 = {}", num, divide_by_5(num));
}
```

We can use the `return` keyword at any point in the function to halt execution and send a value back to the caller. Usually, the use of the `return` keyword is used in combination with a conditional test.

```rust
fn divide_by_5(num: u32) -> u32 {
  if num == 0 {
    // Return early
    return 0;
  }
  num / 5
}
```

When you explicitly use the `return` keyword, you end the statement with a semicolon. If you send back a return value without using the `return` keyword, you don't end the statement with a semicolon. You might have noticed that we didn'tt use the ending semicolon for the `num / 5` return value statement.

### Exercise: Write a function to build a car

```rust
// Declare Car struct to describe vehicle with four named fields
struct Car {
    color: String,
    transmission: Transmission,
    convertible: bool,
    mileage: u32,
}

#[derive(PartialEq, Debug)]
// Declare enum for Car transmission type
// Corrected code: Enum definition uses commas to separate values
enum Transmission {
    Manual,
    SemiAuto,
    Automatic,
}

// Build a new "Car" using the values of three input arguments
// - Color of the car (String)
// - Transmission type (enum)
// - Convertible (boolean, true if the car is a convertible)
// Return an instance of a "Car" struct with the arrow `->` syntax
fn car_factory(color: String, transmission: Transmission, convertible: bool) -> Car {

    // Create a new "Car" instance with requested characteristics
    // - Corrected code: return a "Car" struct
    // - Bind first three fields to value of corresponding input argument
    // - Set mileage to 0
    Car {
        color: color,
        transmission: transmission,
        convertible: convertible,
        mileage: 0
    }
}

fn main() {
    // Order three cars
    let mut car = car_factory(String::from("Red"), Transmission::Manual, false);
    println!("Car 1 = {}, {:?} transmission, convertible: {}, mileage: {}", car.color, car.transmission, car.convertible, car.mileage);

    car = car_factory(String::from("Silver"), Transmission::Automatic, true);
    println!("Car 2 = {}, {:?} transmission, convertible: {}, mileage: {}", car.color, car.transmission, car.convertible, car.mileage);

    car = car_factory(String::from("Yellow"), Transmission::SemiAuto, false);
    println!("Car 3 = {}, {:?} transmission, convertible: {}, mileage: {}", car.color, car.transmission, car.convertible, car.mileage);
}
```
