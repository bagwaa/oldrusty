+++
title = "Ownership and Lifetimes"
date = "2022-08-23T13:42:01+01:00"
author = "Richard Bagshaw"
authorTwitter = "bagwaa" #do not include @
cover = ""
tags = ["rust", "lifetimes", "ownership"]
keywords = ["rust", "lifetimes", "ownership"]
description = ""
showFullContent = false
readingTime = true
hideComments = false
draft = false
+++

Lifetimes in Rust are a way to tell the compiler that borrowed data will be valid at a specific point in time.   Even without knowing it lifetimes are applied to all data in Rust, it's just that most of the time we don't need to concern ourselves with it as the compiler handles them for us.

Sometimes we may need to tweak the lifetime of a piece of borrowed data when storing it in a struct or enum or maybe when returning borrowed data from functions.

### Lifetime Syntax - Structures

Lifetimes are a form of generic, so we create the lifetime inside angled brackets.  In the example below we are specifying a lifetime called `<'a>` or "tick a".  Usually lifetimes are just single letters and start from a and upwards, so `'a` `'b` `'c` and so on.

In the example below we can see that the `field` has a piece of **borrowed** data in the struct with a lifetime of `'a` this means the data in this field will still be around even when the Cat struct has been destroyed.  The `'a` tells Rust that the field data we are giving this structure, exists before we construct the Cat.

```rust
struct Cat<'a> {
    field: &'a DataType,
}
```

### Lifetime Syntax - Functions

In the below example we are again specifying the lifetime `'a` and then reusing that lifetime over the argument we pass in as well as the return type, this ensures that the data we pass in and return will be valid for the same amount of time.

```rust
fn name<'a>(arg: &'a DataType) -> &'a DataType {}
```

if you don't provide any lifetimes when defining a function, interestingly behind the scenes Rust will do this.  Notice how the two arguments by default have different lifetimes denoted.  this is why sometimes you are asked to specify a return type lifetime, because rust doesn't know if it should attach to the first or second argument for the lifetime.  If we just provided a single argument for this function, then rust knows to use the same lifetime for both the argument and the return type.

```rust
fn name<'a, 'b>(arg1: &'a DataType, arg2: &'b DataType) -> &'a DataType {}
```

### Lifetimes in an impl block

When using lifetimes on `impl` blocks, we need to make sure we define it after the `impl` keyword and then apply it after the struct we are implmenting on (see below).

```rust
impl<'a> Articles<'a> {

}
```

### 'static reserved lifetime

This reserved lifetime indicates that the variable should remain in memory for the entire duration of the program.

### Summary

Lifetimes are a tricky to think about as a concept and may take a few attempts to understand correctly.

Lifetime annotations are always on the **function** or **structure**.

Lifetime annotations indicate that their exists some owned data that :-

- Lives at least as long as the borrowed data
- Outlives the scope of a borrow
- Exists longer than the scope of a borrow

Structures utilising borrowed data must :-

- Always be created **after** the owner was created
- Always be destroyed **before** the owner is destroyed

