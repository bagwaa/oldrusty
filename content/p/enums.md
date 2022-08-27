+++
title = "Enums"
date = "2022-08-27T11:31:00+01:00"
author = "Richard Bagshaw"
authorTwitter = "bagwaa" #do not include @
cover = ""
tags = ["rust", "enums"]
keywords = ["rust", "enums"]
description = "An enum or enumeration allows us to work with data in a type safe manner, enums are good for representing parts of a system with multiple variations of state. The most basic exampe of this could be a switch which has an on or off position."
showFullContent = false
readingTime = true
hideComments = false
draft = false
+++

An enum (or enumeration) allows rust to work with data in a type safe manner, enums are good for representing parts of a system with multiple variations of state.  The most basic example of this could be a switch which has an `on` or `off` position.

```rust
enum Switch {
    On,
    Off
}
```

As we can see, we have specified an enum above called `Switch` which has two variants, `On` and `Off`.  We can represent anything in those two states, another example could be as follows:-

```rust
enum InvoicePaymentStatus {
    Paid,
    Unpaid,
    Unsent,
    PaymentDeclined
}
```

We are able to specify whatever we want for the variants, typically they should be something that conveys as much detail as possible.  Without knowing much about the system (or even rust!) it's easy to tell what this enumeration is and what it's representing.

### Using enums as function parameters

We can type hint any enum variation for a function easily like so.

```rust
enum Direction {
    Up,
    Down,
    Left,
    Right
}

fn which_way(go: Direction) {
    match go {
        Direction::Up => println!("Going Up"),
        Direction::Down => println!("Going Down"),
        Direction::Left => println!("Going Left"),
        Direction::Right => println!("Going Right"),
    }
}

fn main() {
    which_way(Direction::Left);
}
```

In the `main()` function above, we pass an enum into the `which_way` function, we can see that the function is type hinted with `Direction`, this makes sure that the function only ever accepts a variant of the enum `Direction` which in this case is represented by `Direction::Left`


### Matching on enum variants

Using the match statement we **MUST** match on all variants, so in the example above we have to match on all possibilities, in this case `Up`, `Down`, `Left` an `Right`, if we miss one then the program will not compile.

However, sometimes we might not want to match on all enums, in this instance we can use `_` to represent, *"all the other variations"*.

```rust
fn which_way(go: Direction) {
    match go {
        Direction::Down => println!("Going Down"),
        _ => println!("Some other direction"),
    }
}
```

This example will match on `Down` if that variant is passed in, however any other variant will be picked up by the `_` operator.  This will **NOT** cause a compile error since `_` represents "everything else".

### Enums and additional data

In rust, enums can also contain data, this is entirely optional but can however prove to be very useful.

```rust
enum Discount {
    Percent(f64),
    Flat(i32)
}

fn main() {
    // Create a 50% discount
    let discount = Discount::Percent(50);
}
```

One thing to note with using enums in this way is that both variants above will not be forced to take a value, you can no longer just use `Discount::Flat` and instead would have to provide the value `Discount::Flat(20)`.

### Summary

- enums make our code much easier to read
- enums which are paired with the `match` keyword are a superpower, and are used frequently in Rust.
