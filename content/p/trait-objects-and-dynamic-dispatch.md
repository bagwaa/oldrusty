+++
title = "Trait Objects and Dynamic Dispatch"
date = "2022-08-22T23:04:54+01:00"
author = "Richard Bagshaw"
authorTwitter = "bagwaa" #do not include @
cover = ""
tags = ["rust", "traits"]
keywords = ["rust", "programming"]
description = ""
showFullContent = false
readingTime = true
hideComments = false
draft = false
+++
#### TLDR;

Trait objects are like generics in that you can use stand ins for variable types, however rather than being calculated at compile time, these are calculated at runtime.  Determining this information at runtime is called **Dynamic Dispatch**

The alternative to this, where the types are determined at compile time is called **Static Dispatch**

Trait objects can be borrowed using a reference, or moved using a Box<>

It makes more sense to put items in a Box<> before putting it into a Vec that way the vector can own all of it's data.

#### Why?

Say you want a collection of things that are of different types, you would do this at runtime instead, it wouldn't be possible at compile time because we wouldn't know what types we could possibly be adding to the collection in some cases.

As an example, an Employee a Manager and a Supervisor could all be added to a single Vec[] this promotes polymorphism.

#### How?

The following portion of code is pretty simple, we create a trait called Clicky which serves as a contract, anything that implements this trait needs to adhere to the contract, in this case Keyboard has to create a definition for the click function.

```rust
trait Clicky {
	fn click(&self);
}

struct Keyboard;

impl Clicky for Keyboard {
	fn click(&self) {
		println!("Click");
	}
}
```

To then create the trait object, we do the following

```rust
let keeb = Keyboard;

let keeb_obj: &dyn Clicky = &keeb;
```

or

```rust
let keeb: Box<dyn Clicky> = Box::new(Keyboard);
```

I think putting this in a `Box<>` allows us to move the structure around if we wanted to, I also think that the Box<> structure means we can wrap something and force it to be stored on the heap.


#### Using Trait Objects within Functions

Now we can have a bunch of different structs that all implement the same trait, and at runtime we can push all these different types into a Vec[]

If we want to accept a trait object as a function parameter we can do the following, notice that we don't need to define Keyboard as a trait object in this example! since the function is defined as accepting a trait object, when we pass a normal object to it, rust will know to convert this to a trait object for us.  This is generally what we will see in the wild.

```rust
fn borrow_clicky(obj: &dyn Clicky) {
	obj.click();
}

let keeb = Keyboard;
borrow_clicky(&keeb);
```

another example but with Box<>

```rust
fn move_clicky(obj: Box<dyn Clicky>) {
	obj.click();
}

let keeb = Box::new(Keyboard);
move_clicky(keeb);
```


#### A Vector with Multiple Types (Heterogeneous)

```rust
struct Mouse;

impl Click for Mouse {
	fn click(&self) {
		println!("Click!");
	}
}

let keeb: Box<dyn Clicky> = Box::new(Keyboard);
let mouse: Box<dyn Clicky> = Box::new(Mouse);

let clickers = vec![keeb, mouse];

```

Another example of the above, but where the types are calculated from the function signatures instead and we leave them to transfer by magic.

```rust
struct Mouse;

impl Click for Mouse {
	fn click(&self) {
		println!("Click!");
	}
}

let keeb: Box::new(Keyboard);
let mouse: Box::new(Mouse);

let clickers: Vec<Box<dyn Clicky>> = vec![keeb, mouse];

```

Both of these examples end up with a Vec of Box that contain any items that are Clicky
