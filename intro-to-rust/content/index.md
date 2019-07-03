class: center
name: title
count: false

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto">

# Intro to Rust Workshop

.grey[Santiago Pastorino]

---

# About me

- WyeWorks co-founder
- Member of Rust compiler contributors team
- Rust Latam conference organizer
- Rust Montevideo Meetup organizer
- Ruby on Rails core alumni

---

# About the workshop

- We'll cover the basics
  - Assumes some familiarity with some systems programming concepts.
  - Feel free to stop me at any time.

---

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto" style="position: absolute; right: 0rem; margin-top: -2rem;">

# What is Rust?

- "New" & safe systems programming language
  - Developed by Mozilla research, v1.0 released in 2015
- Multiparadigm
  - Imperative, structured, functional, concurrent, generic, compiled
- Static strong typing
  - Inference
- Emphasizing control, safety, and speed
- Free and open-source software, MIT License or Apache License 2.0
- Most loved programming language (2016, 2017, 2018 & 2019)

---

# Why Rust?

- Performance
- Reliability
- Productivity

---

# Performance

Rust is **blazingly fast** and **memory-efficient**: with **no runtime or garbage collector**, it can power performance-critical services, run on embedded devices, and easily integrate with other languages.

---

# Reliability

Rust’s **rich type system and ownership** model guarantee **memory-safety and thread-safety** — and enable you to eliminate many classes of bugs at compile-time.

---

# Productivity

Rust has **great documentation**, a **friendly compiler** with useful error messages, and **top-notch tooling** — an integrated package manager and build tool, smart multi-editor support with auto-completion and type inspections, an auto-formatter, and more.

---

# Who is using Rust?

- **Mozilla** - Stylo, WebRender, Rustc.
- **Google** - Fuschia operating system.
- **Facebook** - Mercurial rewrite.
- **Amazon** - Firecracker.
- **Microsoft** - Azure IoT work.
- **Dropbox** - Storage system.
- **Redox OS** - Most complete Rust OS, microkernel design.

You can see even more familiar names like **Twitter**, **npm**, **Red Hat**, **Reddit**, **Samsung**, **Cloudflare**, **Gnome**, **Chef**, **Canonical**, **Coursera**, **Tor** and many more.

---

# What is Rust being used for?

- Operating Systems
- Browsers
- Address hot spots of your app (Python, Ruby, Elixir, JavaScript)
- WebAssembly
- Web APIs
- Networking, Blockchain
- Embedded, Microcontrollers, IoT
- Games

---

# Installation

- Install Rust using [https://rustup.rs](https://rustup.rs) or any other way.
  - `curl https://sh.rustup.rs -sSf | sh`
  - Run `rustc -V` to see if you’re golden.
- Ensure you have git installed
- `git clone https://github.com/spastorino/intro-to-rust.git`
- You can also follow along using https://play.rust-lang.org/

---

# Installation

- rustup --version
  - Rust toolchain installer; enables you to easily switch between Rust
    versions
- rustc --version
  - The Rust compiler, you'll be using it most of the time through cargo
- cargo --version
  - The Rust package manager; downloads and compiles your code and dependencies for you

---

# Create a project with cargo

```shell
# Create a minimal skeleton for a new application (--lib for libraries)
$ cargo new myapp

# Inspect the skeleton app
$ cd myapp

# Builds the binary app
$ cargo build

# Builds and run the binary app
$ cargo run

# => Hello, world!

# Builds and tests app
$ cargo test

# Generate Rustdoc and open it in the browser
$ cargo doc --open
```

---

# Functions

```rust
fn add_numbers(x: i32, y: i32) -> i32 {
    x + y
}

fn main() {
    let x = 10;
    let y = 15;

    let z = add_numbers(x, y);
    // format string using Display trait and print it
    println!("{} + {} = {}", x, y, z);
}
```

---

# Control flow

```rust
fn main() {
    // Bindings are immutable by default
    let mut value = 4;

    for i in 0..=100 {
        if value % 2 == 0 {
            while value < 10 {
                value += i;
            }
            // ...
            continue;
        } else if value == 11 {
            // ...
            break;
        } else {
            /* ... */
        }
    }
}
```

---

# Exercise 1 (warm up)

- Go to this `intro-to-rust/exercises/1_control_flow` project
- Try running `cargo test`
- Make tests pass :)
- Run `cargo run` and check that it prints all the leap years since 1000
  until 2000

---

# Structs

```rust
struct Location {
    longitude: f32,
    latitude: f32,
}

impl Location {
    fn new(longitude: f32, latitude: f32) -> Location {
        Location { longitude, latitude }
    }

    fn longitude(&self) -> f32 {
        self.longitude
    }

    fn latitude(&self) -> f32 {
        self.latitude
    }

    fn move_location(&mut self, other: Location) {
        self.longitude += other.longitude();
        self.latitude += other.latitude();
    }
}
```

---

# Enums

```rust
struct Location {
    longitude: f32,
    latitude: f32,
}

struct Map {
    pin: Option<Location>,
    weather: Weather,
}

enum Weather {
    Sunny,
    Windy,
    Rainy,
    Snowy,
}
```

---

# Match

```rust
fn main() {
    let weather = Weather::Sunny;

    match weather {
        Weather::Sunny => {
            println!("Let's go out!!!");
        }

        Weather::Windy | Weather::Rainy | Weather::Snowy => {
            println!("Let's stay home!!!");
        }
    }
}
```

---

# Match

```rust
fn main() {
    let weather = Weather::Sunny;

    match weather {
        Weather::Sunny => {
            println!("Let's go out!!!");
        }

        // Be careful with catch-all
        _ => {
            println!("Let's stay home!!!");
        }
    }
}
```

---

# Exercise 2

- Go to this `intro-to-rust/exercises/2_structs_enums` project
- Open `src/main.rs` you will find guidance there

---

# Memory safety without garbage collection

- No segmentation faults
- No double free
- No dangling pointers
- No iterator invalidation
- No buffer overflows
- No undefined behavior
- No null pointers
- No data races
- Guaranteed by Rust's ownership system at compile time

???

- Every malloc needs one free
- Array capacity is not checked
- Vulnerabilities caused by memory unsafety are still common

---

# "Manual" memory management in Rust:

- Values **owned** by creator.
- Values **moved** via assignment.
- When final owner returns, **value is freed**.

All this feels invisible and prevents _double free_ errors, _use after free_ errors and _memory leaks_.

???

- Move semantics / RAII
- Rust enforces the RAII discipline
- Variables can own resources

---

# Ownership

```rust
fn main() {
    let apple_1 = Apple::new();
    eat(apple_1); // Give ownership of the apple_1.
    eat(apple_1); // Error: apple_1 has been moved.
}

/// eat function takes ownership of the apple
fn eat(apple_2: Apple) {}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-0r.jpg" alt="Ownership 0" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple); // Give ownership
    bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-1r.png" alt="Ownership 1" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
*   let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple); // Give ownership
    bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-2r.png" alt="Ownership 2" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
*   let mut bag = Vec::new();
    bag.push(apple); // Give ownership
    bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-3r.png" alt="Ownership 3" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
*   bag.push(apple); // Give ownership
    bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-4r.png" alt="Ownership 4" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple); // Give ownership
*   bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-6r.png" alt="Ownership 6" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple); // Give ownership
    bag.push(Apple::new());
*   deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Exercise 3

- Go to this `intro-to-rust/exercises/3_ownership/src` project
- Open `src/main.rs` you will find guidance there

---

# What if I want to use bag again?

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple);
    bag.push(Apple::new());
*   let (weight, bag) = weight(bag); // Return the bag back
    println!("Bag {}, weights {}", bag, weight);
}

/// weight function takes an owned bag
/// and return its weight and the bag back
fn weight(bag: Vec<Apple>) -> (u32, Vec<Apple>) {
    // ...
}
```

---

# Borrowing

<img src="content/images/rust-meetup-children-borrowing-0r.png" alt="Borrowing" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple);
    bag.push(Apple::new());
*   let weight = weight(&bag); // Borrow the bag
    println!("Bag {}, weights {}", bag, weight);
}

/// weight function takes a shared
/// reference to the vector
fn weight(bag: &Vec<Apple>) -> u32 {
    // ...
}
```

---

# Mutable borrowing

<img src="content/images/rust-meetup-children-borrowing-0r.png" alt="Borrowing" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple);
    bag.push(Apple::new());
*   deliver(&mut bag); // Borrow the bag for mutation
    println!("Bag is now {}", bag);
}

/// deliver function takes a mutable shared
/// reference to the vector
fn deliver(bag: &mut Vec<Apple>) {
    // mutate the bag
}
```

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
buffer.push_str(" World");
println!("{:?}", slice);
```

---

# Dangers of mutation

```rust
*let mut buffer = format!("Hello");
let slice = &buffer[1..];
buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-1r.png" alt="Mutation 1">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
*let slice = &buffer[1..];
buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-2r.png" alt="Mutation 2">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
*buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-3r.png" alt="Mutation 3">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
*buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-4r.png" alt="Mutation 4">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
*buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-5r.png" alt="Mutation 5">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
*buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-6r.png" alt="Mutation 6">

???

- No aliasing + mutation at the same time

---

# Lifetime of a borrow

```rust
let mut buffer = format!("Hello");
*let slice = &buffer[1..];
*buffer.push_str(" World");
*println!("{:?}", slice);
```

**Lifetime**: span of code where reference is used.

--

**Rules**:

- If there's a shared reference, no writers during the **lifetime of the shared borrow**.
- If there's a mutable reference, no other readers or writers during the **lifetime of the mutable borrow**

---

# Exercise 4

- Go to this `intro-to-rust/exercises/4_borrowing/src` project
- Open `src/main.rs` you will find guidance there

---

# Strings (&str vs String)

- &str static and read-only
- String dynamic

```rust
fn main() {
    let a = "hi"; // &str

    a.push_str("something"); // compile error

    let b = String::from("hi"); // String

    b.push_str(" world");
}
```

???

UTF-8

---

# Option

```rust
// Presence or absense of value of generic type T
enum Option<T> {
    Some(T),
    None,
}
```

---

# Option

```rust
fn print_first(v: Vec<String>) {
    match v.first() {
        Some(elem) => println!("{}", elem),
        None => println!("Not found"),
    }
}
```

---

# Result

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

---

# Error handling

```rust
#[derive(Debug)]
enum Version { Version1, Version2 }

fn parse_version(header: &[u8]) -> Result<Version, &'static str> {
    match header.get(0) {
        None => Err("invalid header length"),
        Some(&1) => Ok(Version::Version1),
        Some(&2) => Ok(Version::Version2),
        Some(_) => Err("invalid version"),
    }
}

fn main() {
    let version = parse_version(&[1, 2, 3, 4]);
    match version {
        Ok(v) => println!("working with version: {:?}", v),
        Err(e) => println!("error parsing header: {:?}", e),
    }
}
```

---

# Exercise 5 - Build your own shell

Write a shell which can run a single command on a separate process.

``` rust
fn main() {
    loop {
        // Read line from standard input
        // "Parse" line into executable command
        // Execute the command in a separate process
        // Show output
    }
}
```
