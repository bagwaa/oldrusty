+++
title = "My First Post"
date = "2022-08-22T18:31:54+01:00"
author = "Richard Bagshaw"
authorTwitter = "bagwaa" #do not include @
cover = ""
tags = ["rust", "ownership", "structs"]
keywords = ["rust", "programming"]
description = ""
showFullContent = false
readingTime = true
hideComments = false
draft = false
+++
Dictum sit amet justo donec enim diam vulputate ut. Adipiscing enim eu turpis egestas pretium aenean pharetra magna. Nulla facilisi cras fermentum odio. Netus et malesuada fames ac turpis egestas.

{{< image src="/images/xp.jpg" alt="Hello Friend" position="center" style="width: 100%;" >}}

Feugiat nibh sed pulvinar proin gravida hendrerit lectus. Et netus et malesuada fames ac turpis. Cras semper auctor neque vitae tempus quam. Nisl rhoncus mattis rhoncus urna neque viverra justo. Interdum velit euismod in pellentesque massa placerat. Pulvinar elementum integer enim neque volutpat. Ornare aenean euismod elementum nisi quis eleifend quam adipiscing. Nibh tellus molestie nunc non blandit massa enim nec dui. Gravida rutrum quisque non tellus. Adipiscing bibendum est ultricies integer quis auctor elit. In fermentum posuere urna nec tincidunt praesent semper feugiat. Varius sit amet mattis vulputate enim nulla aliquet porttitor. Dui ut ornare lectus sit amet est placerat. Eget nulla facilisi etiam dignissim diam quis enim lobortis scelerisque. Massa massa ultricies mi quis hendrerit dolor magna eget est.


- Some first option here
    - bar
- Some other second option here
    - baz
- And finally a third option
    - foo
    - bar
    - baz


> Tellus id interdum velit laoreet id. Condimentum lacinia quis vel eros. Accumsan tortor posuere ac ut consequat. Ornare quam viverra orci sagittis eu.

Dui accumsan sit amet nulla facilisi morbi. Amet massa vitae tortor condimentum. Suspendisse ultrices gravida dictum fusce. Mattis rhoncus urna neque viverra justo nec ultrices dui. Imperdiet proin fermentum leo vel orci. Viverra accumsan in nisl nisi scelerisque eu ultrices vitae. Dolor sit amet consectetur adipiscing elit pellentesque habitant. Tincidunt vitae semper quis lectus nulla at volutpat. Neque vitae tempus quam pellentesque. Tempor orci dapibus ultrices in iaculis nunc. Tempus imperdiet nulla malesuada pellentesque elit eget gravida cum sociis. Felis eget velit aliquet sagittis id. Facilisi nullam vehicula ipsum a arcu cursus vitae congue mauris. Quam adipiscing vitae proin sagittis nisl rhoncus mattis rhoncus. Sed nisi lacus sed viverra tellus in. Odio aenean sed adipiscing diam donec. Tellus rutrum tellus pellentesque eu tincidunt tortor aliquam. Magna etiam tempor orci eu lobortis elementum nibh. Porta non pulvinar neque laoreet suspendisse. Elit duis tristique sollicitudin nibh sit.

```rust
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    loop {
        println!("Please input your guess");

        let mut guess = String::new();

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {guess}");

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small"),
            Ordering::Greater => println!("Too big"),
            Ordering::Equal => {
                println!("You Win!, {}", secret_number);
                break;
            }
        }
    }
}

```

