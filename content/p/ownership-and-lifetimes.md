+++
title = "Ownership and Lifetimes"
date = "2022-08-23T13:42:01+01:00"
author = "Richard Bagshaw"
authorTwitter = "bagwaa" #do not include @
cover = ""
tags = ["rust", "lifetimes", "ownership", "references"]
keywords = ["rust", "lifetimes", "ownership", "references"]
description = ""
showFullContent = false
readingTime = true
hideComments = false
draft = false
+++

Lifetimes in Rust are a way to tell the compiler that borrowed data will be valid at a specific point in time.   Even without knowing it lifetimes are applied to all borrowed data in Rust, However, for the most part we don't need to concern ourselves with it as the compiler handles them for us at compile time.

Another way to say it, Lifetimes enforce a piece of memory is still valid for a reference.

Sometimes we may need to tweak the lifetime of a piece of borrowed data when storing it in a struct, enum or maybe when returning borrowed data from a function.

```rust
fn main() {
    let a;
    {
        let b = String::from("foo");
        a = &b; // ERROR!
    }
    println!("{}", a);
}
```

In this example we create a variable called `a`, and then inside another scope we create `b` and assign a String.  `b` now points to a place in memory on the heap that stores the string `"foo"`.

We then assign a borrow from `b` to `a`.  However at the end of this scope, `b` is destroyed leaving `a` pointing at nothing.  This is picked up by the compiler with the error `b does not live long enough`.

Lifetimes are really about making sure memory doesn't get cleaned up before a reference can use it.

### Functions returning borrowed data

In the example below we can see another compiler error, because we are returning a borrowed value when there is no value for it to borrow from, Rust is smart enough to know that nothing was passed into that function scope, so anything we borrow from must have been created within the function.  This presents a problem like we had in the first example because this means the value we are borrowing from will be going out of scope when the function ends.

> "The life of the memory we are returning is not long enough" - Doug Millford

```rust
fn get_int_ref() -> &i32 {
    let a = 1;
    &a
    // ERROR! borrowing from a value that originated in the function
}
```

Instead of returning a reference in the above example, we just returned the value and transfered ownership, then this would be perfectly acceptable.

Let's look at another example, except this time we will pass in a value and return a value.  In this example we pass in a reference to an `i32` and we return a reference to an `i32` rust has no problem with this because it knows that we can pass a reference out from this function that originated from outside of it.  The scope providing the reference is the same exact scope that will be receiving the output from the function.

```rust
fn get_int_ref(p1: &i32) -> &i32 {
    p1
}
```

### Lifetime Syntax - Functions

In the below example we are again specifying the lifetime `'a` and then reusing that lifetime over the argument we pass in as well as the return type, this ensures that the data we pass in and return will be valid for the same amount of time.

```rust
fn name<'a>(arg: &'a DataType) -> &'a DataType {}
```

if you don't provide any lifetimes when defining a function, interestingly behind the scenes Rust will do this.  Notice how the two arguments by default have different lifetimes denoted.  this is why sometimes you are asked to specify a return type lifetime, because rust doesn't know if it should attach to the first or second argument for the lifetime.  If we just provided a single argument for this function, then rust knows to use the same lifetime for both the argument and the return type.

```rust
fn name<'a, 'b>(arg1: &'a DataType, arg2: &'b DataType) -> &'a DataType {
    // the return type has to use either a or b as the lifetime.
    // this must be specified by you as there are two choices
}
```

If you write a function that accepts references as arguments then the compiler will apply a lifetime to it behind the scenes as above, if you provide another parameter to the function it will assign another lifetime to that argument as well.  Any parameters that are not references are nothing todo with lifetimes.

### Lifetime Mismatch

If we use the following example you will be met with a mismatch error from the compiler, this is because the return type is only around for the same length of time as `s1` which uses the `'a` lifetime.  In our program we may return s2, which has a lifetime of `'b`.  This means the reference we return is not guarunteed to live the same amount of time.

```rust
fn main() {
    let s1 = 10;
    let s2 = 20;
    let result_ref = get_int_ref(&s1, &s2);
    println!("{}", result_ref);
}

fn get_int_ref<'a, 'b>(s1: &'a i32, s2: &'b i32) -> &'a i32 {
    if s1 > s2 {
        s1
    } else {
        s2 // mismatch!
    }
}
```

To fix the above example, we can just share the same single lifetime across all parameters and return values.  Now we know for a fact that all parameters, and returned values will all live as long as each other.

```rust
fn main() {
    let s1 = 10;
    let s2 = 20;
    let result_ref = get_int_ref(&s1, &s2);
    println!("{}", result_ref);
}

fn get_int_ref<'a>(s1: &'a i32, s2: &'a i32) -> &'a i32 {
    if s1 > s2 {
        s1
    } else {
        s2
    }
}
```

### Common Example - Comparing String Slices

The code block below will not compile, this is because the values that we are returning could end up being a dangling reference, a reference to nothing.
```rust
fn compare_string_slices(p1: &str, p2: &str) -> &str {
    if p1 > p2 {
        p1 // error: explicit lifetime required
    } else {
        p2 // error: explicit lifetime required
    }
}
```

This code can be fixed by adding an explicit lifetime for both the inputs and the outputs, ensuring that they all live as long as each other.

```rust
fn compare_string_slices<'a>(p1: &'a str, p2: &'a str) -> &'a str {
    if p1 > p2 {
        p1
    } else {
        p2
    }
}
```

### Lifetime Syntax - Structures

Lifetimes are a form of generic, so we create the lifetime inside angled brackets.  In the example below we are specifying a lifetime called `<'a>` or "tick a".  Usually lifetimes are just single letters and start from a and upwards, so `'a` `'b` `'c` and so on.

In the example below we can see that the `field` has a piece of **borrowed** data in the struct with a lifetime of `'a` this means the data in this field will still be around even when the Cat struct has been destroyed.  The `'a` tells Rust that the field data we are giving this structure, exists before we construct the Cat.

```rust
struct Cat<'a> {
    field: &'a DataType,
}
```

### Lifetimes in an impl block

When using lifetimes on `impl` blocks, we need to make sure we define it after the `impl` keyword and then apply it after the struct we are implmenting on (see below).

```rust
impl<'a> Articles<'a> {

}
```

### 'static reserved lifetime

This reserved lifetime indicates that the variable should remain in memory for the entire duration of the program.

#### Further Resources

By far the best explanation I have found on lifetimes comes in the form of a YouTube video by Doug Millford, he will explain everything you need to know in this great video.

{{< youtube 1QoT9fmPYr8 >}}

#### Summary

Lifetimes are a tricky to think about as a concept and may take a few attempts to understand correctly.

Lifetime annotations are always on the **function** or **structure**.

Lifetime annotations indicate that their exists some owned data that :-

- Lives at least as long as the borrowed data
- Outlives the scope of a borrow
- Exists longer than the scope of a borrow

Structures utilising borrowed data must :-

- Always be created **after** the owner was created
- Always be destroyed **before** the owner is destroyed

