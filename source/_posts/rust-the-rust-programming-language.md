---
title: rust the rust programming language
date: 2023-06-27 10:33:28
tags: rust
categories: rust
---

## Managing Growing Projects with Packages, Crates, and Modules

A package can contain multiple binary crates and optionally one library crate.

You can create scopes and change which names are in or out of scope. You can't have two items with the same name in the same scope; tools are available to resolve name conflicts.

The Rust module system, include:

- Packages: A Cargo feature that lets you build, test, and share crates.

- Crates: A tree of modules that produces a library or executable.

- Modules and use: Let you control the organization, scope, and privacy of paths.

- Paths: A way of naming an item, such as a struct, function, or module.

### Packages and Crates

A crate is the smallest amount of code that the Rust compiler considers at a time. Even if you run `rustc` rather than `cargo` and pass a single source code file, the compiler considers that file to be a crate. Crates can contain modules, and the modules may be defined in other files that get compiled with the crate.

A crate can come in one of two forms: a binary crate or a library crate.

- Binary crates are programs you can compile to an executable that you can run, such as a command-line program or a server. Each must have a function called `main` that defines what happens when the executable runs.

- Library crates don't have a `main` function, and they don't compile to an executable. Instead, they define functionality intended to be shared with multiple projects.

- Most of the time when Rustaceans say “crate”, they mean library crate, and they use “crate” interchangeably with the general programming concept of a “library".

The crate root is a source file that the Rust compiler starts from and makes up the root module of your crate.

A package is a bundle of one or more crates that provides a set of functionality. A package contains a _Cargo.toml_ file that describes how to build those crates.

A package can contain as many binary crates as you like, but at most only one library crate. A package must contain at least one crate, whether that's a library or binary crate.

Cargo follows a convention that _src/main.rs_ is the crate root of a binary crate with the same name as the package. Likewise, Cargo knows that if the package directory contains _src/lib.rs_, the package contains a library crate with the same name as the package, and _src/lib.rs_ is its crate root. Cargo passes the crate root files to `rustc` to build the library or binary.

A package that only contains _src/main.rs_, meaning it only contains a binary crate which has the same name as the package. If a package contains _src/main.rs_ and _src/lib.rs_, it has two crates: a binary and a library, both with the same name as the package. A package can have multiple binary crates by placing files in the _src/bin_ directory: each file will be a separate binary crate.

### Defining Modules to Control Scope and Privacy

Namely paths that allow you to name items; the `use` keyword that brings a path into scope; and the `pub` keyword to make items public.

#### Modules Cheat Sheet

- **Start from the crate root**: When compiling a crate, the compiler first looks in the crate root file (usually _src/lib.rs_ for a library crate or _src/main.rs_ for a binary crate) for code to compile.

- **Declaring modules**: In the crate root file, you can declare new modules; say, you declare a “garden” module with `mod garden`;. The compiler will look for the module's code in these places:

  - Inline, within curly brackets that replace the semicolon following `mod garden`

  - In the file _src/garden.rs_

  - In the file _src/garden/mod.rs_

- **Declaring submodules**: In any file other than the crate root, you can declare submodules. For example, you might declare `mod vegetables`; in _src/garden.rs_. The compiler will look for the submodule's code within the directory named for the parent module in these places:

  - Inline, directly following `mod vegetables`, within curly brackets instead of the semicolon

  - In the file _src/garden/vegetables.rs_

  - In the file _src/garden/vegetables/mod.rs_

- **Paths to code in modules**: Once a module is part of your crate, you can refer to code in that module from anywhere else in that same crate, as long as the privacy rules allow, using the path to the code. For example, an `Asparagus` type in the garden vegetables module would be found at `crate::garden::vegetables::Asparagus`.

- **Private vs public**: Code within a module is private from its parent modules by default. To make a module public, declare it with `pub mod` instead of `mod`. To make items within a public module public as well, use pub before their declarations.

- **The `use` keyword**: Within a scope, the `use` keyword creates shortcuts to items to reduce repetition of long paths. In any scope that can refer to `crate::garden::vegetables::Asparagus`, you can create a shortcut with `use crate::garden::vegetables::Asparagus`; and from then on you only need to write `Asparagus` to make use of that type in the scope.

Here we create a binary crate named `backyard` that illustrates these rules. The crate's directory, also named `backyard`, contains these files and directories:

```text
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

The crate root file in this case is _src/main.rs_, and it contains:

Filename: src/main.rs

```rust
use crate::garden::vegetables::Asparagus;

pub mod garden;

fn main() {
    let plant = Asparagus {};
    println!("I'm growing {:?}!", plant);
}
```

The `pub mod garden`; line tells the compiler to include the code it finds in _src/garden.rs_, which is:

Filename: src/garden.rs

```rust
pub mod vegetables;
```

Here, `pub mod vegetables`; means the code in _src/garden/vegetables.rs_ is included too. That code is:

```rust
#[derive(Debug)]
pub struct Asparagus {}
```

#### Grouping Related Code in Modules

Modules also allow us to control the privacy of items, because code within a module is private by default. Private items are internal implementation details not available for outside use.

As an example, let's write a library crate that provides the functionality of a restaurant. We'll define the signatures of functions but leave their bodies empty to concentrate on the organization of the code, rather than the implementation of a restaurant.

In the restaurant industry, some parts of a restaurant are referred to as front of house and others as back of house. Front of house is where customers are; this encompasses where the hosts seat customers, servers take orders and payment, and bartenders make drinks. Back of house is where the chefs and cooks work in the kitchen, dishwashers clean up, and managers do administrative work.

To structure our crate in this way, we can organize its functions into nested modules. Create a new library named `restaurant` by running `cargo new restaurant --lib`; then enter the following code into src/lib.rs to define some modules and function signatures. Here's the front of house section:

Filename: src/lib.rs

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}
```

We define a module with the `mod` keyword followed by the name of the module (in this case, `front_of_house`). The body of the module then goes inside curly brackets. Inside modules, we can place other modules, as in this case with the modules `hosting` and `serving`. Modules can also hold definitions for other items, such as structs, enums, constants, traits, and functions.

Earlier, we mentioned that _src/main.rs_ and _src/lib.rs_ are called crate roots. The reason for their name is that the contents of either of these two files form a module named crate at the root of the crate's module structure, known as the module tree.

The following listing shows the module tree for the structure in preceding listing.

```text
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

Notice that the entire module tree is rooted under the implicit module named crate.

### Paths for Referring to an Item in the Module Tree

A path can take two forms:

- An absolute path is the full path starting from a crate root; for code from an external crate, the absolute path begins with the crate name, and for code from the current crate, it starts with the literal `crate`.

- A _relative path_ starts from the current module and uses `self`, `super`, or an identifier in the current module.

Both absolute and relative paths are followed by one or more identifiers separated by double colons (`::`).

Filename: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

Using the `crate` name to start from the crate root is like using `/` to start from the filesystem root in your shell.

Starting with a module name means that the path is relative.

Choosing whether to use a relative or absolute path is a decision you'll make based on your project, and depends on whether you're more likely to move item definition code separately from or together with the code that uses the item. Our preference in general is to specify absolute paths because it's more likely we'll want to move code definitions and item calls independently of each other.

In Rust, all items (functions, methods, structs, enums, modules, and constants) are private to parent modules by default. If you want to make an item like a function or struct private, you put it in a module.

Items in a parent module can't use the private items inside child modules, but items in child modules can use the items in their ancestor modules (by using the super keyword). This is because child modules wrap and hide their implementation details, but the child modules can see the context in which they're defined.

Rust chose to have the module system function this way so that hiding inner implementation details is the default. However, Rust does give you the option to expose inner parts of child modules' code to outer ancestor modules by using the `pub` keyword to make an item public.

#### Exposing Paths with the pub Keyword

The `pub` keyword on a module only lets code in its ancestor modules refer to it, not access its inner code. Because modules are containers, there's not much we can do by only making the module public; we need to go further and choose to make one or more of the items within the module public as well.

> **Best Practices for Packages with a Binary and a Library**
>
> The module tree should be defined in src/lib.rs. Then, any public items can be used in the binary crate by starting paths with the name of the package. The binary crate becomes a user of the library crate just like a completely external crate would use the library crate: it can only use the public API.

#### Starting Relative Paths with super

We can construct relative paths that begin in the parent module, rather than the current module or the crate root, by using `super` at the start of the path. This is like starting a filesystem path with the `..` syntax. Using `super` allows us to reference an item that we know is in the parent module, which can make rearranging the module tree easier when the module is closely related to the parent, but the parent might be moved elsewhere in the module tree someday.

Filename: src/lib.rs

```rust
fn deliver_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::deliver_order();
    }

    fn cook_order() {}
}
```

The `fix_incorrect_order` function is in the `back_of_house` module, so we can use `super` to go to the parent module of `back_of_house`, which in this case is `crate`, the root.

We think the `back_of_house` module and the `deliver_order` function are likely to stay in the same relationship to each other and get moved together should we decide to reorganize the crate's module tree. Therefore, we used `super` so we'll have fewer places to update code in the future if this code gets moved to a different module.

#### Making Structs and Enums Public

We can also use `pub` to designate structs and enums as public, but there are a few details extra to the usage of `pub` with structs and enums.

If we use `pub` before a struct definition, we make the struct public, but the struct's fields will still be private. We can make each field public or not on a case-by-case basis.

Filename: src/lib.rs

```rust
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // Order a breakfast in the summer with Rye toast
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // Change our mind about what bread we'd like
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // The next line won't compile if we uncomment it; we're not allowed to see or modify the seasonal fruit that comes with the meal
    // meal.seasonal_fruit = String::from("blueberries");
}
```

Because the `toast` field in the `back_of_house::Breakfast` struct is public, in `eat_at_restaurant` we can write and read to the `toast` field using dot notation. Notice that we can't use the `seasonal_fruit` field in `eat_at_restaurant` because `seasonal_fruit` is private.

Also, note that because `back_of_house::Breakfast` has a private field, the struct needs to provide a public associated function that constructs an instance of `Breakfast` (we've named it `summer` here). If `Breakfast` didn't have such a function, we couldn't create an instance of `Breakfast` in `eat_at_restaurant` because we couldn't set the value of the private `seasonal_fruit` field in `eat_at_restaurant`.

In contrast, if we make an enum public, all of its variants are then public. We only need the `pub` before the `enum` keyword.

Filename: src/lib.rs

```rust
mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
}

pub fn eat_at_restaurant() {
    let order1 = back_of_house::Appetizer::Soup;
    let order2 = back_of_house::Appetizer::Salad;
}
```

Enums aren't very useful unless their variants are public; it would be annoying to have to annotate all enum variants with `pub` in every case, so the default for enum variants is to be public.

Structs are often useful without their fields being public, so struct fields follow the general rule of everything being private by default unless annotated with `pub`.

### Bringing Paths into Scope with the use Keyword

We can create a shortcut to a path with the `use` keyword once, and then use the shorter name everywhere else in the scope.

Filename: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

Adding `use` and a path in a scope is similar to creating a symbolic link in the filesystem. Paths brought into scope with `use` also check privacy, like any other paths.

Note that `use` only creates the shortcut for the particular scope in which the `use` occurs. The following listing moves the `eat_at_restaurant` function into a new child module named `customer`, which is then a different scope than the `use` statement, so the function body won't compile:

Filename: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

mod customer {
    pub fn eat_at_restaurant() {
        hosting::add_to_waitlist();
    }
}
```

To fix this problem, move the `use` within the `customer` module too, or reference the shortcut in the parent module with `super::hosting` within the child `customer` module.

#### Creating Idiomatic use Paths

Filename: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting::add_to_waitlist;

pub fn eat_at_restaurant() {
    add_to_waitlist();
}
```

Although this listing accomplish the same task, the preceding one is the idiomatic way to bring a function into scope with `use`.

Bringing the function's parent module into scope with `use` means we have to specify the parent module when calling the function. Specifying the parent module when calling the function makes it clear that the function isn't locally defined while still minimizing repetition of the full path.

On the other hand, when bringing in structs, enums, and other items with `use`, it's idiomatic to specify the full path.

```rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert(1, 2);
}
```

There's no strong reason behind this idiom: it's just the convention that has emerged, and folks have gotten used to reading and writing Rust code this way.

The exception to this idiom is if we're bringing two items with the same name into scope with `use` statements, because Rust doesn't allow that.

Filename: src/lib.rs

```rust
use std::fmt;
use std::io;

fn function1() -> fmt::Result {
    // --snip--
}

fn function2() -> io::Result<()> {
    // --snip--
}
```

As you can see, using the parent modules distinguishes the two `Result` types. If instead we specified `use std::fmt::Result` and `use std::io::Result`, we'd have two `Result` types in the same scope and Rust wouldn't know which one we meant when we used `Result`.

#### Providing New Names with the as Keyword

There's another solution to the problem of bringing two types of the same name into the same scope with `use`: after the path, we can specify `as` and a new local name, or _alias_, for the type.

Filename: src/lib.rs

```rust
use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
}

fn function2() -> IoResult<()> {
    // --snip--
}
```

#### Re-exporting Names with pub use

When we bring a name into scope with the `use` keyword, the name available in the new scope is private. To enable the code that calls our code to refer to that name as if it had been defined in that code's scope, we can combine `pub` and `use`. This technique is called _re-exporting_ because we're bringing an item into scope but also making that item available for others to bring into their scope.

Filename: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

Before this change, external code would have to call the `add_to_waitlist` function by using the path `restaurant::front_of_house::hosting::add_to_waitlist()`. Now that this `pub use` has re-exported the `hosting` module from the root module, external code can now use the path `restaurant::hosting::add_to_waitlist()` instead.

Re-exporting is useful when the internal structure of your code is different from how programmers calling your code would think about the domain.

With pub use, we can write our code with one structure but expose a different structure. Doing so makes our library well organized for programmers working on the library and programmers calling the library.

#### Using External Packages

To use the external package `rand` in our project, we added this line to _Cargo.toml_:

Filename: Cargo.toml

```toml
rand = "0.8.5"
```

Adding `rand` as a dependency in _Cargo.toml_ tells Cargo to download the `rand` package and any dependencies from [crates.io](https://crates.io) and make `rand` available to our project.

Then, to bring `rand` definitions into the scope of our package, we added a use line starting with the name of the crate, `rand`, and listed the items we wanted to bring into scope. We brought the `Rng` trait into scope and called the `rand::thread_rng` function:

```rust
use rand::Rng;

fn main() {
    let secret_number = rand::thread_rng().gen_range(1..=100);
}
```

Members of the Rust community have made many packages available at [crates.io](https://crates.io), and pulling any of them into your package involves these same steps: listing them in your package's _Cargo.toml_ file and using `use` to bring items from their crates into scope.

Note that the standard `std` library is also a crate that's external to our package. Because the standard library is shipped with the Rust language, we don't need to change _Cargo.toml_ to include `std`. But we do need to refer to it with `use` to bring items from there into our package's scope.

```rust
use std::collections::HashMap;
```

#### Using Nested Paths to Clean Up Large use Lists

If we're using multiple items defined in the same crate or same module, listing each item on its own line can take up a lot of vertical space in our files. Instead, we can use nested paths to bring the same items into scope in one line. We do this by specifying the common part of the path, followed by two colons, and then curly brackets around a list of the parts of the paths that differ.

Filename: src/main.rs

```rust
use std::cmp::Ordering;
use std::io;

// Nested
use std::{cmp::Ordering, io};
```

To merge a complete path and its sub path into one `use` statement, we can use `self` in the nested path.

Filename: src/lib.rs

```rust
use std::io;
use std::io::Write;

// Nested
use std::io::{self, Write};
// This line brings std::io and std::io::Write into scope.
```

#### The Glob Operator

If we want to bring _all_ public items defined in a path into scope, we can specify that path followed by the `*` glob operator.

```rust
use std::collections::*;
```

This `use` statement brings all public items defined in `std::collections` into the current scope.

Be careful when using the glob operator! Glob can make it harder to tell what names are in scope and where a name used in your program was defined.

The glob operator is often used when testing to bring everything under test into the tests module. The glob operator is also sometimes used as part of the prelude pattern.

### Separating Modules into Different Files

For example, let's start from the code in previous listing that had multiple restaurant modules. We'll extract modules into files instead of having all the modules defined in the crate root file.

First, we'll extract `front_of_house` module to its own file. Remove the code inside the curly brackets for the `front_of_house` module, leaving only the `mod front_of_house`; declaration, so that _src/lib.rs_ contains the code shown in following list.

Filename: src/lib.rs

```rust
mod front_of_house;

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

Next, place the code that was in the curly brackets into a new file named _src/front_of_house.rs_, as shown in the following listing. The compiler knows to look in this file because it came across the module declaration in the crate root with the name `front_of_house`.

Filename: src/front_of_house.rs

```rust
pub mod hosting {
    pub fn add_to_waitlist() {}
}
```

Note that you only need to load a file using a `mod` declaration once in your module tree. Once the compiler knows the file is part of the project (and knows where in the module tree the code resides because of where you've put the mod statement), other files in your project should refer to the loaded file's code using a path to where it was declared. In other words, `mod` is not an “include” operation that you may have seen in other programming languages.

Next, we'll extract the `hosting` module to its own file. The process is a bit different because `hosting` is a child module of `front_of_house`, not of the root module. We'll place the file for `hosting` in a new directory that will be named for its ancestors in the module tree, in this case _src/front_of_house/_.

To start moving `hosting`, we change _src/front_of_house.rs_ to contain only the declaration of the `hosting` module:

Filename: src/front_of_house.rs

```rust
pub mod hosting;
```

Then we create a _src/front_of_house_ directory and a file _hosting.rs_ to contain the definitions made in the `hosting` module:

Filename: src/front_of_house/hosting.rs

```rust
pub fn add_to_waitlist() {}
```

The compiler's rules for which files to check for which modules' code means the directories and files more closely match the module tree.

> **Alternate File Paths**
>
> So far we've covered the most idiomatic file paths the Rust compiler uses, but Rust also supports an older style of file path. For a module named `front_of_house` declared in the crate root, the compiler will look for the module's code in:
>
> - _src/front_of_house.rs_ (what we covered)
>
> - _src/front_of_house/mod.rs_ (older style, still supported path)
>
> For a module named `hosting` that is a submodule of `front_of_house`, the compiler will look for the module's code in:
>
> - _src/front_of_house/hosting.rs_ (what we covered)
>
> - _src/front_of_house/hosting/mod.rs_ (older style, still supported path)
>
> If you use both styles for the same module, you'll get a compiler error. Using a mix of both styles for different modules in the same project is allowed, but might be confusing for people navigating your project.
>
> The main downside to the style that uses files named mod.rs is that your project can end up with many files named mod.rs, which can get confusing when you have them open in your editor at the same time.

The mod keyword declares modules, and Rust looks in a file with the same name as the module for the code that goes into that module.

## Common Collections

Most other data types represent one specific value, but collections can contain multiple values. Unlike the built-in array and tuple types, the data these collections p oint to is stored on the heap, which means the amount of data does not need to be known at compile time and can grow or shrink as the program runs.

In this chapter, we'll discuss three collections that are used very often in Rust programs:

- A vector allows you to store a variable number of values next to each other.

- A string is a collection of characters. We've mentioned the String type previously, but in this chapter we'll talk about it in depth.

- A hash map allows you to associate a value with a particular key. It's a particular implementation of the more general data structure called a map.

### Storing Lists of Values with Vectors

The first collection type we'll look at is `Vec<T>`, also known as a vector. Vectors allow you to store more than one value in a single data structure that puts all the values next to each other in memory. Vectors can only store values of the same type.

#### Creating a New Vector

To create a new empty vector, we call the `Vec::new` function.

```rust
let v: Vec<i32> = Vec::new();
```

Note that we added a type annotation here. Because we aren't inserting any values into this vector, Rust doesn't know what kind of elements we intend to store. This is an important point. Vectors are implemented using generics.

More often, you'll create a `Vec<T>` with initial values and Rust will infer the type of value you want to store, so you rarely need to do this type annotation. Rust conveniently provides the `vec!` macro, which will create a new vector that holds the values you give it.

```rust
let v = vec![1, 2, 3];
```

#### Updating a Vector

To create a vector and then add elements to it, we can use the `push` method.

```rust
let mut v = Vec::new();

v.push(5);
v.push(6);
```

As with any variable, if we want to be able to change its value, we need to make it mutable using the `mut` keyword. The numbers we place inside are all of type i32, and Rust infers this from the data, so we don't need the Vec<i32> annotation.

#### Reading Elements of Vectors

There are two ways to reference a value stored in a vector: via indexing or using the get method.

```rust
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
println!("The third element is {third}");

let third: Option<&i32> = v.get(2);
match third {
    Some(third) => println!("The third element is {third}"),
    None => println!("There is no third element."),
}
```

Using `&` and `[]` gives us a reference to the element at the index value. When we use the `get` method with the index passed as an argument, we get an `Option<&T>` that we can use with `match`.

The reason Rust provides these two ways to reference an element is so you can choose how the program behaves when you try to use an index value outside the range of existing elements.

```rust
let v = vec![1, 2, 3, 4, 5];

let does_not_exist = &v[100];
let does_not_exist = v.get(100);
```

When we run this code, the first `[]` method will cause the program to panic because it references a nonexistent element. This method is best used when you want your program to crash if there's an attempt to access an element past the end of the vector.

When the `get` method is passed an index that is outside the vector, it returns `None` without panicking. You would use this method if accessing an element beyond the range of the vector may happen occasionally under normal circumstances. Your code will then have logic to handle having either `Some(&element)` or `None`.

When the program has a valid reference, the borrow checker enforces the ownership and borrowing rules to ensure this reference and any other references to the contents of the vector remain valid.

Recall the rule that states you can't have mutable and immutable references in the same scope. That rule applies that we hold an immutable reference to the first element in a vector and try to add an element to the end.

```rust
let mut v = vec![1, 2, 3, 4, 5];

let first = &v[0];

v.push(6);

println!("The first element is: {first}");
```

The code might look like it should work: why should a reference to the first element care about changes at the end of the vector? This error is due to the way vectors work: because vectors put the values next to each other in memory, adding a new element onto the end of the vector might require allocating new memory and copying the old elements to the new space, if there isn't enough room to put all the elements next to each other where the vector is currently stored. In that case, the reference to the first element would be pointing to deallocated memory. The borrowing rules prevent programs from ending up in that situation.

#### Iterating over the Values in a Vector

To access each element in a vector in turn, we would iterate through all of the elements rather than use indices to access one at a time. You can use a for loop to get immutable references to each element in a vector and print them.

```rust
let v = vec![100, 32, 57];
for i in &v {
    println!("{i}");
}
```

We can also iterate over mutable references to each element in a mutable vector in order to make changes to all the elements.

```rust
let mut v = vec![100, 32, 57];
for i in &mut v {
    *i += 50;
}
```

To change the value that the mutable reference refers to, we have to use the `*` dereference operator to get to the value in i before we can change it.

Iterating over a vector, whether immutably or mutably, is safe because of the borrow checker's rules. If we attempted to insert or remove items in the `for` loop bodies, we would get a compiler error. The reference to the vector that the `for` loop holds prevents simultaneous modification of the whole vector.

#### Using an Enum to Store Multiple Types

Vectors can only store values that are the same type. Fortunately, the variants of an enum are defined under the same enum type, so when we need one type to represent elements of different types, we can define and use an enum.

We can define an enum whose variants will hold the different value types, and all the enum variants will be considered the same type: that of the enum. Then we can create a vector to hold that enum and so, ultimately, holds different types.

```rust
enum SpreadsheetCell {
    Int(i32),
    Float(f64),
    Text(String),
}

let row = vec![
    SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
    SpreadsheetCell::Float(10.12),
];
```

Rust needs to know what types will be in the vector at compile time so it knows exactly how much memory on the heap will be needed to store each element. We must also be explicit about what types are allowed in this vector. If Rust allowed a vector to hold any type, there would be a chance that one or more of the types would cause errors with the operations performed on the elements of the vector. Using an enum plus a match expression means that Rust will ensure at compile time that every possible case is handled.

If you don't know the exhaustive set of types a program will get at runtime to store in a vector, the enum technique won't work. Instead, you can use a trait object.

#### Dropping a Vector Drops Its Elements

Like any other struct, a vector is freed when it goes out of scope.

When the vector gets dropped, all of its contents are also dropped, meaning the integers it holds will be cleaned up. The borrow checker ensures that any references to contents of a vector are only used while the vector itself is valid.

### Storing UTF-8 Encoded Text with Strings

New Rustaceans commonly get stuck on strings for a combination of three reasons: Rust's propensity for exposing possible errors, strings being a more complicated data structure than many programmers give them credit for, and UTF-8.

#### What Is a String?

Rust has only one string type in the core language, which is the string slice str that is usually seen in its borrowed form `&str`.

The `String` type, which is provided by Rust's standard library rather than coded into the core language, is a growable, mutable, owned, UTF-8 encoded string type.

Both `String` and string slices are UTF-8 encoded.

#### Creating a New String

Many of the same operations available with `Vec<T>` are available with `String` as well, because `String` is actually implemented as a wrapper around a vector of bytes with some extra guarantees, restrictions, and capabilities.

```rust
let mut s = String::new();
```

Often, we'll have some initial data that we want to start the string with. For that, we use the `to_string` method, which is available on any type that implements the `Display` trait, as string literals do.

```rust
let data = "initial contents";

let s = data.to_string();

// the method also works on a literal directly:
let s = "initial contents".to_string();
```

We can also use the function `String::from` to create a `String` from a string literal.

```rust
let s = String::from("initial contents");
```

In this case, `String::from` and `to_string` do the same thing, so which you choose is a matter of style and readability.

#### Updating a String

A `String` can grow in size and its contents can change, just like the contents of a `Vec<T>`, if you push more data into it. In addition, you can conveniently use the `+` operator or the `format!` macro to concatenate String values.

- Appending to a String with `push_str` and `push`

  ```rust
  let mut s = String::from("foo");
  s.push_str("bar");
  ```

  The push_str method takes a string slice because we don't necessarily want to take ownership of the parameter.

  ```rust
  let mut s1 = String::from("foo");
  let s2 = "bar";
  s1.push_str(s2);
  println!("s2 is {s2}");
  ```

  The `push` method takes a single character as a parameter and adds it to the `String`.

  ```rust
  let mut s = String::from("lo");
  s.push('l');
  ```

- Concatenation with the `+` Operator or the `format!` Macro

  ```rust
  let s1 = String::from("Hello, ");
  let s2 = String::from("world!");
  let s3 = s1 + &s2; // note s1 has been moved here and can no longer be used
  ```

  The reason `s1` is no longer valid after the addition, and the reason we used a reference to `s2`, has to do with the signature of the method that's called when we use the + operator. The + operator uses the add method, whose signature looks something like this:

  ```rust
  fn add(self, s: &str) -> String {
  ```

  In the standard library, you'll see add defined using generics and associated types. Here, we've substituted in concrete types, which is what happens when we call this method with String values. This signature gives us the clues we need to understand the tricky bits of the `+` operator.

  First, `s2` has an `&`, meaning that we're adding a reference of the second string to the first string. This is because of the `s` parameter in the `add` function: we can only add a `&str` to a `String`; we can't add two `String` values together. But wait - the type of `&s2` is `&String`, not `&str`, as specified in the second parameter to add.

  The reason we're able to use `&s2` in the call to `add` is that the compiler can coerce the `&String` argument into a `&str`. When we call the `add` method, Rust uses a deref coercion, which here turns `&s2` into `&s2[..]`. Because `add` does not take ownership of the `s` parameter, `s2` will still be a valid `String` after this operation.

  Second, we can see in the signature that `add` takes ownership of `self`, because `self` does not have an `&`. This means `s1` will be moved into the `add` call and will no longer be valid after that. So although `let s3 = s1 + &s2;` looks like it will copy both strings and create a new one, this statement actually takes ownership of `s1`, appends a copy of the contents of `s2`, and then returns ownership of the result. In other words, it looks like it's making a lot of copies but isn't; the implementation is more efficient than copying.

  If we need to concatenate multiple strings, the behavior of the + operator gets unwieldy:

  ```rust
  let s1 = String::from("tic");
  let s2 = String::from("tac");
  let s3 = String::from("toe");

  let s = s1 + "-" + &s2 + "-" + &s3;
  ```

  For more complicated string combining, we can instead use the format! macro:

  ```rust
  let s1 = String::from("tic");
  let s2 = String::from("tac");
  let s3 = String::from("toe");

  let s = format!("{s1}-{s2}-{s3}");
  ```

  The `format!` macro works like `println!`, but instead of printing the output to the screen, it returns a `String` with the contents. The version of the code using `format!` is much easier to read, and the code generated by the `format!` macro uses references so that this call doesn't take ownership of any of its parameters.

#### Indexing into Strings

In many other programming languages, accessing individual characters in a string by referencing them by index is a valid and common operation. However, if you try to access parts of a String using indexing syntax in Rust, you'll get an error. Rust strings don't support indexing.

- Internal Representation

  A `String` is a wrapper over a `Vec<u8>`.

  ```rust
  let hello = String::from("Hola");
  // In this case, len will be 4, which means the vector storing the string “Hola” is 4 bytes long. Each of these letters takes 1 byte when encoded in UTF-8.
  ```

  ```rust
  let hello = String::from("Здравствуйте");
  // In this case, len will be 24, not 12. That's the number of bytes it takes to encode “Здравствуйте” in UTF-8, because each Unicode scalar value in that string takes 2 bytes of storage.
  ```

  Different Unicode scalar value takes different bytes of storage. Therefore, an index into the string's bytes will not always correlate to a valid Unicode scalar value.

  ```rust
  let hello = "hello";
  let answer = &hello[0];
  // If &"hello"[0] were valid code that returned the byte value, it would return 104, not h.
  ```

  Another Reason is that when encoded in UTF-8, indexing into a string will return a byte value instead of the character on its own.

  The answer, then, is that to avoid returning an unexpected value and causing bugs that might not be discovered immediately, Rust doesn't compile this code at all and prevents misunderstandings early in the development process.

- Bytes and Scalar Values and Grapheme Clusters! Oh My!

  Another point about UTF-8 is that there are actually three relevant ways to look at strings from Rust's perspective: as bytes (how computers ultimately store this data), scalar values (what Rust's char type is), and grapheme clusters (the closest thing to what we would call letters).

  If we look at the Hindi word “नमस्ते” written in the Devanagari script, it is stored as a vector of u8 values that looks like this:

  ```text
  [224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164, 224, 165, 135]
  ```

  That's 18 bytes and is how computers ultimately store this data. If we look at them as Unicode scalar values, which are what Rust's char type is, those bytes look like this:

  ```text
  ['न', 'म', 'स', '्', 'त', 'े']
  ```

  There are six char values here, but the fourth and sixth are not letters: they're diacritics that don't make sense on their own. Finally, if we look at them as grapheme clusters, we'd get what a person would call the four letters that make up the Hindi word:

  ```text
  ["न", "म", "स्", "ते"]
  ```

  Rust provides different ways of interpreting the raw string data that computers store so that each program can choose the interpretation it needs, no matter what human language the data is in.

  A final reason Rust doesn't allow us to index into a String to get a character is that indexing operations are expected to always take constant time (O(1)). But it isn't possible to guarantee that performance with a String, because Rust would have to walk through the contents from the beginning to the index to determine how many valid characters there were.

#### Slicing Strings

Indexing into a string is often a bad idea because it's not clear what the return type of the string-indexing operation should be: a byte value, a character, a grapheme cluster, or a string slice.

Rather than indexing using `[]` with a single number, you can use `[]` with a range to create a string slice containing particular bytes.

```rust
let hello = "Здравствуйте";

let s = &hello[0..4];
```

If we were to try to slice only part of a character's bytes with something like `&hello[0..1]`, Rust would panic at runtime because index 0 is not a char boundary. Each char has 2 bytes in this case.

#### Methods for Iterating Over Strings

The best way to operate on pieces of strings is to be explicit about whether you want characters or bytes. For individual Unicode scalar values, use the `chars` method. Alternatively, the `bytes` method returns each raw byte.

```rust
for c in "Зд".chars() {
    println!("{c}");
}
// This code will print the following:
// З
// д
```

```rust
for b in "Зд".bytes() {
    println!("{b}");
}
// This code will print the four bytes that make up this string:
// 208
// 151
// 208
// 180
```

But be sure to remember that valid Unicode scalar values may be made up of more than 1 byte.

Getting grapheme clusters from strings as with the Devanagari script is complex, so this functionality is not provided by the standard library.

#### Strings Are Not So Simple

Rust has chosen to make the correct handling of `String` data the default behavior for all Rust programs, which means programmers have to put more thought into handling UTF-8 data upfront. This trade-off exposes more of the complexity of strings than is apparent in other programming languages, but it prevents you from having to handle errors involving non-ASCII characters later in your development life cycle.

### Storing Keys with Associated Values in Hash Maps

The type `HashMap<K, V>` stores a mapping of keys of type K to values of type V using a hashing function, which determines how it places these keys and values into memory.

Many programming languages support this kind of data structure, but they often use a different name, such as hash, map, object, hash table, dictionary, or associative array, just to name a few.

Hash maps are useful when you want to look up data not by using an index, as you can with vectors, but by using a key that can be of any type.

#### Creating a New Hash Map

One way to create an empty hash map is using new and adding elements with insert.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

Note that we need to first `use` the `HashMap` from the collections portion of the standard library. Of our three common collections, this one is the least often used, so it's not included in the features brought into scope automatically in the prelude. Hash maps also have less support from the standard library; there's no built-in macro to construct them, for example.

Just like vectors, hash maps store their data on the heap. Like vectors, hash maps are homogeneous: all of the keys must have the same type as each other, and all of the values must have the same type.

#### Accessing Values in a Hash Map

We can get a value out of the hash map by providing its key to the get method.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let team_name = String::from("Blue");
let score = scores.get(&team_name).copied().unwrap_or(0);
```

The `get` method returns an `Option<&V>`; if there's no value for that key in the hash map, `get` will return `None`. This program handles the `Option` by calling `copied` to get an `Option<i32>` rather than an `Option<&i32>`, then `unwrap_or` to set `score` to zero if `scores` doesn't have an entry for the key.

We can iterate over each key/value pair in a hash map in a similar manner as we do with vectors, using a `for` loop.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

for (key, value) in &scores {
    println!("{key}: {value}");
}
```

#### Hash Maps and Ownership

For types that implement the `Copy` trait, like `i32`, the values are copied into the hash map. For owned values like `String`, the values will be moved and the hash map will be the owner of those values.

```rust
use std::collections::HashMap;

let field_name = String::from("Favorite color");
let field_value = String::from("Blue");

let mut map = HashMap::new();
map.insert(field_name, field_value);
// field_name and field_value are invalid at this point, try using them and see what compiler error you get!
```

If we insert references to values into the hash map, the values won't be moved into the hash map. The values that the references point to must be valid for at least as long as the hash map is valid.

#### Updating a Hash Map

Although the number of key and value pairs is growable, each unique key can only have one value associated with it at a time, but not vice versa.

When you want to change the data in a hash map, you have to decide how to handle the case when a key already has a value assigned. You could replace the old value with the new value, completely disregarding the old value. You could keep the old value and ignore the new value, only adding the new value if the key doesn't already have a value. Or you could combine the old value and the new value.

- Overwriting a Value

  If we insert a key and a value into a hash map and then insert that same key with a different value, the value associated with that key will be replaced.

  ```rust
  use std::collections::HashMap;

  let mut scores = HashMap::new();

  scores.insert(String::from("Blue"), 10);
  scores.insert(String::from("Blue"), 25);

  println!("{:?}", scores); // {"Blue": 25}
  ```

- Adding a Key and Value Only If a Key Isn't Present

  It's common to check whether a particular key already exists in the hash map with a value then take the following actions: if the key does exist in the hash map, the existing value should remain the way it is. If the key doesn't exist, insert it and a value for it.

  Hash maps have a special API for this called `entry` that takes the key you want to check as a parameter. The return value of the `entry` method is an enum called `Entry` that represents a value that might or might not exist.

  ```rust
  use std::collections::HashMap;

  let mut scores = HashMap::new();
  scores.insert(String::from("Blue"), 10);

  scores.entry(String::from("Yellow")).or_insert(50);
  scores.entry(String::from("Blue")).or_insert(50);

  println!("{:?}", scores); // {"Yellow": 50, "Blue": 10}
  ```

  The `or_insert` method on `Entry` is defined to return a mutable reference to the value for the corresponding `Entry` key if that key exists, and if not, inserts the parameter as the new value for this key and returns a mutable reference to the new value.

- Updating a Value Based on the Old Value

  Another common use case for hash maps is to look up a key's value and then update it based on the old value.

  ```rust
  use std::collections::HashMap;

  let text = "hello world wonderful world";

  let mut map = HashMap::new();

  for word in text.split_whitespace() {
      let count = map.entry(word).or_insert(0);
      *count += 1;
  }

  println!("{:?}", map); // {"world": 2, "hello": 1, "wonderful": 1}
  ```

  The `split_whitespace` method returns an iterator over sub-slices, separated by whitespace, of the value in `text`. The `or_insert` method returns a mutable reference (`&mut V`) to the value for the specified key. Here we store that mutable reference in the `count` variable, so in order to assign to that value, we must first dereference `count` using the asterisk `(*).` The mutable reference goes out of scope at the end of the `for` loop, so all of these changes are safe and allowed by the borrowing rules.

#### Hashing Functions

By default, HashMap uses a hashing function called _SipHash_ that can provide resistance to Denial of Service (DoS) attacks involving hash tables. This is not the fastest hashing algorithm available, but the trade-off for better security that comes with the drop in performance is worth it.

If you profile your code and find that the default hash function is too slow for your purposes, you can switch to another function by specifying a different hasher. A hasher is a type that implements the BuildHasher trait.

## Error Handling

Rust groups errors into two major categories: recoverable and unrecoverable errors.

- For a recoverable error, such as a file not found error, we most likely just want to report the problem to the user and retry the operation.

- Unrecoverable errors are always symptoms of bugs, like trying to access a location beyond the end of an array, and so we want to immediately stop the program.

### Unrecoverable Errors with panic!

Sometimes, bad things happen in your code, and there's nothing you can do about it. In these cases, Rust has the panic! macro.

There are two ways to cause a panic in practice: by taking an action that causes our code to panic (such as accessing an array past the end) or by explicitly calling the panic! macro. In both cases, we cause a panic in our program.

By default, these panics will print a failure message, unwind, clean up the stack, and quit. Via an environment variable, you can also have Rust display the call stack when a panic occurs to make it easier to track down the source of the panic.

The call to panic! causes the error message contained in the last two lines. The first line shows our panic message and the place in our source code where the panic occurred: src/main.rs:2:5 indicates that it's the second line, fifth character of our src/main.rs file.

In other cases, the panic! call might be in code that our code calls, and the filename and line number reported by the error message will be someone else's code where the panic! macro is called, not the line of our code that eventually led to the panic! call. We can use the backtrace of the functions the panic! call came from to figure out the part of our code that is causing the problem.

#### Unwinding the Stack or Aborting in Response to a Panic

By default, when a panic occurs, the program starts unwinding, which means Rust walks back up the stack and cleans up the data from each function it encounters. However, this walking back and cleanup is a lot of work. Rust, therefore, allows you to choose the alternative of immediately aborting, which ends the program without cleaning up.

Memory that the program was using will then need to be cleaned up by the operating system. If in your project you need to make the resulting binary as small as possible, you can switch from unwinding to aborting upon a panic by adding `panic = 'abort'` to the appropriate `[profile]` sections in your Cargo.toml file. For example, if you want to abort on panic in release mode, add this:

```toml
[profile.release]
panic = 'abort'
```

#### Using a panic! Backtrace

In C, attempting to read beyond the end of a data structure is undefined behavior. You might get whatever is at the location in memory that would correspond to that element in the data structure, even though the memory doesn't belong to that structure. This is called a buffer overread and can lead to security vulnerabilities if an attacker is able to manipulate the index in such a way as to read data they shouldn't be allowed to that is stored after the data structure.

To protect your program from this sort of vulnerability, if you try to read an element at an index that doesn't exist, Rust will stop execution and refuse to continue.

We can set the `RUST_BACKTRACE` environment variable to any value except 0 to get a backtrace of exactly what happened to cause the error. A backtrace is a list of all the functions that have been called to get to this point. Backtraces in Rust work as they do in other languages: the key to reading the backtrace is to start from the top and read until you see files you wrote. That's the spot where the problem originated. The lines above that spot are code that your code has called; the lines below are code that called your code. These before-and-after lines might include core Rust code, standard library code, or crates that you're using.

In order to get backtraces with this information, debug symbols must be enabled. Debug symbols are enabled by default when using `cargo build` or `cargo run` without the `--release` flag, as we have here.

### Recoverable Errors with Result

The Result enum is defined as having two variants, Ok and Err, as follows:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`T` represents the type of the value that will be returned in a success case within the `Ok` variant, and `E` represents the type of the error that will be returned in a failure case within the `Err` variant. Because Result has these generic type parameters, we can use the Result type and the functions defined on it in many different situations where the successful value and error value we want to return may differ.

```rust
use std::fs::File;

fn main() {
    let greeting_file_result = File::open("hello.txt");
}
```

The return type of `File::open` is a `Result<T, E>`. The generic parameter `T` has been filled in by the implementation of `File::open` with the type of the success value, `std::fs::File`, which is a file handle. The type of `E` used in the error value is `std::io::Error`.

We need to add to the code to take different actions depending on the value `File::open` returns.

```rust
use std::fs::File;

fn main() {
    let greeting_file_result = File::open("hello.txt");

    let greeting_file = match greeting_file_result {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```

Note that, like the `Option` enum, the `Result` enum and its variants have been brought into scope by the prelude, so we don't need to specify `Result::` before the `Ok` and `Err` variants in the match arms.

#### Matching on Different Errors

The preceding code will `panic!` no matter why `File::open` failed. However, we want to take different actions for different failure reasons: if `File::open` failed because the file doesn't exist, we want to create the file and return the handle to the new file. If `File::open` failed for any other reason—for example, because we didn't have permission to open the file - we still want the code to `panic!`.

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let greeting_file_result = File::open("hello.txt");

    let greeting_file = match greeting_file_result {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            other_error => {
                panic!("Problem opening the file: {:?}", other_error);
            }
        },
    };
}
```

The type of the value that `File::open` returns inside the `Err` variant is `io::Error`, which is a struct provided by the standard library. This struct has a method kind that we can call to get an `io::ErrorKind` value. The enum `io::ErrorKind` is provided by the standard library and has variants representing the different kinds of errors that might result from an `io` operation. The variant we want to use is `ErrorKind::NotFound`, which indicates the file we're trying to open doesn't exist yet. So we match on `greeting_file_result`, but we also have an inner match on `error.kind()`.

The condition we want to check in the inner match is whether the value returned by `error.kind()` is the `NotFound` variant of the `ErrorKind` enum. If it is, we try to create the file with `File::create`. However, because `File::create` could also fail, we need a second arm in the inner match expression. When the file can't be created, a different error message is printed. The second arm of the outer match stays the same, so the program panics on any error besides the missing file error.

#### Alternatives to Using `match` with `Result<T, E>`

That's a lot of `match`! The `match` expression is very useful but also very much a primitive. Closures are used with many of the methods defined on `Result<T, E>`. These methods can be more concise than using `match` when handling `Result<T, E>` values in your code.

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let greeting_file = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {:?}", error);
            })
        } else {
            panic!("Problem opening the file: {:?}", error);
        }
    });
}
```

Many more of these methods can clean up huge nested `match` expressions when you're dealing with errors.

#### Shortcuts for Panic on Error: unwrap and expect

The `unwrap` method is a shortcut method implemented just like the `match` expression. If the `Result` value is the `Ok` variant, `unwrap` will return the value inside the `Ok`. If the `Result` is the `Err` variant, `unwrap` will call the `panic!` macro for us.

```rust
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt").unwrap();
}
```

Similarly, the `expect` method lets us also choose the `panic!` error message. Using `expect` instead of `unwrap` and providing good error messages can convey your intent and make tracking down the source of a panic easier.

```rust
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt")
        .expect("hello.txt should be included in this project");
}
```

The error message used by `expect` in its call to `panic!` will be the parameter that we pass to `expect`, rather than the default `panic!` message that `unwrap` uses.

#### Propagating Errors

When a function's implementation calls something that might fail, instead of handling the error within the function itself, you can return the error to the calling code so that it can decide what to do. This is known as _propagating_ the error and gives more control to the calling code, where there might be more information or logic that dictates how the error should be handled than what you have available in the context of your code.

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let username_file_result = File::open("hello.txt");

    let mut username_file = match username_file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut username = String::new();

    match username_file.read_to_string(&mut username) {
        Ok(_) => Ok(username),
        Err(e) => Err(e),
    }
}
```

This function can be written in a much shorter way, but we're going to start by doing a lot of it manually in order to explore error handling; at the end, we'll show the shorter way. Let's look at the return type of the function first: `Result<String, io::Error>`. This means the function is returning a value of the type `Result<T, E>` where the generic parameter `T` has been filled in with the concrete type `String`, and the generic type `E` has been filled in with the concrete type `io::Error`.

We chose `io::Error` as the return type of this function because that happens to be the type of the error value returned from both of the operations we're calling in this function's body that might fail: the `File::open` function and the `read_to_string` method.

If `File::open` succeeds, the file handle in the pattern variable file becomes the value in the mutable variable `username_file` and the function continues. In the `Err` case, instead of calling `panic!`, we use the `return` keyword to return early out of the function entirely and pass the error value from `File::open`, now in the pattern variable `e`, back to the calling code as this function's error value.

The `read_to_string` method also returns a `Result` because it might fail, even though `File::open` succeeded. So we need another `match` to handle that `Result`: if `read_to_string` succeeds, then our function has succeeded, and we return the `username` from the file that's now in `username` wrapped in an `Ok`. If `read_to_string` fails, we return the error value in the same way that we returned the error value in the `match` that handled the return value of `File::open`. However, we don't need to explicitly say return, because this is the last expression in the function.

This pattern of propagating errors is so common in Rust that Rust provides the question mark operator `?` to make this easier.

#### A Shortcut for Propagating Errors: the ? Operator

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username_file = File::open("hello.txt")?;
    let mut username = String::new();
    username_file.read_to_string(&mut username)?;
    Ok(username)
}
```

The `?` placed after a `Result` value is defined to work in almost the same way as the `match` expressions we defined to handle the `Result` values. If the value of the `Result` is an `Ok`, the value inside the `Ok` will get returned from this expression, and the program will continue. If the value is an `Err`, the `Err` will be returned from the whole function as if we had used the `return` keyword so the error value gets propagated to the calling code.

There is a difference between what the `match` expression does and what the `?` operator does: error values that have the `?` operator called on them go through the `from` function, defined in the `From` trait in the standard library, which is used to convert values from one type into another. When the `?` operator calls the `from` function, the error type received is converted into the error type defined in the return type of the current function. This is useful when a function returns one error type to represent all the ways a function might fail, even if parts might fail for many different reasons.

For example, we could change the `read_username_from_file` function to return a custom error type named `OurError` that we define. If we also define `impl From<io::Error> for OurError` to construct an instance of `OurError` from an `io::Error`, then the `?` operator calls in the body of `read_username_from_file` will call `from` and convert the error types without needing to add any more code to the function.

The `?` at the end of the `File::open` call will return the value inside an `Ok` to the variable `username_file`. If an error occurs, the `?` operator will return early out of the whole function and give any `Err` value to the calling code. The same thing applies to the `?` at the end of the `read_to_string` call.

The `?` operator eliminates a lot of boilerplate and makes this function's implementation simpler. We could even shorten this code further by chaining method calls immediately after the `?`.

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username = String::new();

    File::open("hello.txt")?.read_to_string(&mut username)?;

    Ok(username)
}
```

Instead of creating a variable `username_file`, we've chained the call to `read_to_string` directly onto the result of `File::open("hello.txt")?`. We still have a `?` at the end of the `read_to_string` call, and we still return an `Ok` value containing `username` when both `File::open` and `read_to_string` succeed rather than returning errors.

```rust
use std::fs;
use std::io;

fn read_username_from_file() -> Result<String, io::Error> {
    fs::read_to_string("hello.txt")
}
```

Reading a file into a string is a fairly common operation, so the standard library provides the convenient `fs::read_to_string` function that opens the file, creates a new `String`, reads the contents of the file, puts the contents into that `String`, and returns it. Of course, using `fs::read_to_string` doesn't give us the opportunity to explain all the error handling, so we did it the longer way first.

#### Where The ? Operator Can Be Used

The `?` operator can only be used in functions whose return type is compatible with the value the `?` is used on. This is because the `?` operator is defined to perform an early return of a value out of the function.

Let's look at the error we'll get if we use the `?` operator in a main function with a return type incompatible with the type of the value we use `?` on:

```rust
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt")?;
}
```

This code opens a file, which might fail. The `?` operator follows the `Result` value returned by `File::open`, but this main function has the return type of `()`, not `Result`.

This error points out that we're only allowed to use the `?` operator in a function that returns `Result`, `Option`, or another type that implements `FromResidual`.

To fix the error, you have two choices. One choice is to change the return type of your function to be compatible with the value you're using the `?` operator on as long as you have no restrictions preventing that. The other technique is to use a match or one of the `Result<T, E>` methods to handle the `Result<T, E>` in whatever way is appropriate.

The error message also mentioned that `?` can be used with `Option<T>` values as well. As with using `?` on `Result`, you can only use `?` on `Option` in a function that returns an `Option`. If the value is `None`, the `None` will be returned early from the function at that point. If the value is `Some`, the value inside the `Some` is the resulting value of the expression and the function continues.

```rust
fn last_char_of_first_line(text: &str) -> Option<char> {
    text.lines().next()?.chars().last()
}
```

This function returns `Option<char>` because it's possible that there is a character there, but it's also possible that there isn't. This code takes the `text` string slice argument and calls the `lines` method on it, which returns an iterator over the lines in the string. Because this function wants to examine the first line, it calls `next` on the iterator to get the first value from the iterator. If `text` is the empty string, this call to `next` will return `None`, in which case we use `?` to stop and return `None` from `last_char_of_first_line`. If `text` is not the empty string, `next` will return a `Some` value containing a string slice of the first line in `text`.

The `?` extracts the string slice, and we can call `chars` on that string slice to get an iterator of its characters. We're interested in the last character in this first line, so we call `last` to return the last item in the iterator. This is an `Option` because it's possible that the first line is the empty string, for example if text starts with a blank line but has characters on other lines, as in "\nhi". However, if there is a last character on the first line, it will be returned in the `Some` variant.

Note that you can use the `?` operator on a `Result` in a function that returns `Result`, and you can use the `?` operator on an `Option` in a function that returns `Option`, but you can't mix and match. The `?` operator won't automatically convert a `Result` to an `Option` or vice versa; in those cases, you can use methods like the `ok` method on `Result` or the `ok_or` method on `Option` to do the conversion explicitly.

So far, all the `main` functions we've used return `()`. The `main` function is special because it's the entry and exit point of executable programs, and there are restrictions on what its return type can be for the programs to behave as expected.

Luckily, `main` can also return a `Result<(), E>`. We've changed the return type of `main` to be `Result<(), Box<dyn Error>>` and added a return value `Ok(())` to the end. This code will now compile:

```rust
use std::error::Error;
use std::fs::File;

fn main() -> Result<(), Box<dyn Error>> {
    let greeting_file = File::open("hello.txt")?;

    Ok(())
}
```

The `Box<dyn Error>` type is a trait object, which we'll talk about in Chapter 17. For now, you can read `Box<dyn Error>` to mean “any kind of error.” Using `?` on a `Result` value in a `main` function with the error type `Box<dyn Error>` is allowed, because it allows any `Err` value to be returned early.

When a `main` function returns a `Result<(), E>`, the executable will exit with a value of `0` if `main` returns `Ok(())` and will exit with a nonzero value if `main` returns an `Err` value.

The `main` function may return any types that implement the `std::process::Termination` trait, which contains a function `report` that returns an `ExitCode`.

### To panic! or Not to panic!

When you choose to return a Result value, you give the calling code options. The calling code could choose to attempt to recover in a way that's appropriate for its situation, or it could decide that an Err value in this case is unrecoverable, so it can call panic! and turn your recoverable error into an unrecoverable one. Therefore, returning Result is a good default choice when you're defining a function that might fail.

In situations such as examples, prototype code, and tests, it's more appropriate to write code that panics instead of returning a Result.

#### Examples, Prototype Code, and Tests

When you're writing an example to illustrate some concept, also including robust error-handling code can make the example less clear. In examples, it's understood that a call to a method like `unwrap` that could panic is meant as a placeholder for the way you'd want your application to handle errors, which can differ based on what the rest of your code is doing.

Similarly, the `unwrap` and `expect` methods are very handy when prototyping, before you're ready to decide how to handle errors. They leave clear markers in your code for when you're ready to make your program more robust.

If a method call fails in a test, you'd want the whole test to fail, even if that method isn't the functionality under test. Because `panic!` is how a test is marked as a failure, calling `unwrap` or `expect` is exactly what should happen.

#### Cases in Which You Have More Information Than the Compiler

It would also be appropriate to call `unwrap` or `expect` when you have some other logic that ensures the `Result` will have an `Ok` value, but the logic isn't something the compiler understands. If you can ensure by manually inspecting the code that you'll never have an `Err` variant, it's perfectly acceptable to call `unwrap`, and even better to document the reason you think you'll never have an `Err` variant in the `expect` text. Here's an example:

```rust
use std::net::IpAddr;

let home: IpAddr = "127.0.0.1"
    .parse()
    .expect("Hardcoded IP address should be valid");
```

However, having a hardcoded, valid string doesn't change the return type of the `parse` method: we still get a `Result` value, and the compiler will still make us handle the `Result` as if the `Err` variant is a possibility because the compiler isn't smart enough to see that this string is always a valid IP address. If the IP address string came from a user rather than being hardcoded into the program and therefore did have a possibility of failure, we'd definitely want to handle the `Result` in a more robust way instead. Mentioning the assumption that this IP address is hardcoded will prompt us to change `expect` to better error handling code if in the future, we need to get the IP address from some other source instead.

#### Guidelines for Error Handling

It's advisable to have your code panic when it's possible that your code could end up in a bad state. In this context, a bad state is when some assumption, guarantee, contract, or invariant has been broken, such as when invalid values, contradictory values, or missing values are passed to your code—plus one or more of the following:

- The bad state is something that is unexpected, as opposed to something that will likely happen occasionally, like a user entering data in the wrong format.

- Your code after this point needs to rely on not being in this bad state, rather than checking for the problem at every step.

- There's not a good way to encode this information in the types you use.

If someone calls your code and passes in values that don't make sense, it's best to return an error if you can so the user of the library can decide what they want to do in that case. However, in cases where continuing could be insecure or harmful, the best choice might be to call `panic!` and alert the person using your library to the bug in their code so they can fix it during development. Similarly, `panic!` is often appropriate if you're calling external code that is out of your control and it returns an invalid state that you have no way of fixing.

However, when failure is expected, it's more appropriate to return a `Result` than to make a `panic!` call.

When your code performs an operation that could put a user at risk if it's called using invalid values, your code should verify the values are valid first and panic if the values aren't valid. This is mostly for safety reasons: attempting to operate on invalid data can expose your code to vulnerabilities. This is the main reason the standard library will call `panic!` if you attempt an out-of-bounds memory access: trying to access memory that doesn't belong to the current data structure is a common security problem. Functions often have contracts: their behavior is only guaranteed if the inputs meet particular requirements. Panicking when the contract is violated makes sense because a contract violation always indicates a caller-side bug and it's not a kind of error you want the calling code to have to explicitly handle. In fact, there's no reasonable way for calling code to recover; the calling programmers need to fix the code. Contracts for a function, especially when a violation will cause a panic, should be explained in the API documentation for the function.

However, having lots of error checks in all of your functions would be verbose and annoying. Fortunately, you can use Rust's type system (and thus the type checking done by the compiler) to do many of the checks for you. If your function has a particular type as a parameter, you can proceed with your code's logic knowing that the compiler has already ensured you have a valid value. For example, if you have a type rather than an `Option`, your program expects to have _something_ rather than _nothing_. Your code then doesn't have to handle two cases for the `Some` and `None` variants: it will only have one case for definitely having a value. Code trying to pass nothing to your function won't even compile, so your function doesn't have to check for that case at runtime. Another example is using an unsigned integer type such as `u32`, which ensures the parameter is never negative.

#### Creating Custom Types for Validation

Recall the guessing game in Chapter 2 in which our code asked the user to guess a number between 1 and 100. We never validated that the user's guess was between those numbers before checking it against our secret number; we only validated that the guess was positive. But it would be a useful enhancement to guide the user toward valid guesses and have different behavior when a user guesses a number that's out of range versus when a user types, for example, letters instead.

One way to do this would be to parse the guess as an i32 instead of only a u32 to allow potentially negative numbers, and then add a check for the number being in range, like so:

```rust
loop {
    // --snip--

    let guess: i32 = match guess.trim().parse() {
        Ok(num) => num,
        Err(_) => continue,
    };

    if guess < 1 || guess > 100 {
        println!("The secret number will be between 1 and 100.");
        continue;
    }

    match guess.cmp(&secret_number) {
        // --snip--
}
```

However, this is not an ideal solution: if it was absolutely critical that the program only operated on values between 1 and 100, and it had many functions with this requirement, having a check like this in every function would be tedious (and might impact performance).

Instead, we can make a new type and put the validations in a function to create an instance of the type rather than repeating the validations everywhere. That way, it's safe for functions to use the new type in their signatures and confidently use the values they receive. The following example shows one way to define a Guess type that will only create an instance of Guess if the new function receives a value between 1 and 100.

```rust
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {}.", value);
        }

        Guess { value }
    }

    pub fn value(&self) -> i32 {
        self.value
    }
}
```

The code in the body of the `new` function tests `value` to make sure it's between 1 and 100.

The public method `value` is necessary because the `value` field of the `Guess` struct is private. It's important that the `value` field be private so code using the `Guess` struct is not allowed to set `value` directly: code outside the module must use the `Guess::new` function to create an instance of `Guess`, thereby ensuring there's no way for a `Guess` to have a `value` that hasn't been checked by the conditions in the `Guess::new` function.
