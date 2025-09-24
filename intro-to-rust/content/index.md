class: center
name: title
count: false

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto">

# Intro to Rust

.grey[Santiago Pastorino]

.grey[.smaller[WyeWorks co-founder | Rust compiler and types team member]]

---

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto" style="position: absolute; right: 0rem; margin-top: -2rem;">

# Objectives

- Memory Layout & Memory Safety
- Rust Key Concepts
  - Ownership
  - Borrowing
  - Lifetimes
- Other type system features

???

- Conceptual talk
- Not going to explain syntax or focus on it

---

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto" style="position: absolute; right: 0rem; margin-top: -2rem;">

# What is Rust?

- Modern & safe systems programming language
  - Originally developed by Mozilla research
  - v1.0 released in 2015
- Emphasizing performance, type safety, and concurrency
- Multiparadigm
  - Ideas from FP, immutability, higher-order functions, algebraic data types, and pattern matching.
  - Also supports OOP via structs, enums, traits, and methods.
- Compiled, powerful type system, statically typed, type inference, generics
- Free and open-source software, MIT License or Apache License 2.0

???

- Mozilla research -> Firefox and Servo
- Safety without GC, all references point to valid memory, Borrow Checker
- Multiparadigm, influenced by functional programming
- Type inference, almost never write types. Only for definitions. Local vs Global Inference, Stability and explicit contracts APIs, Generics, Error messages, compiler complexity, etc

---

# Memory safety without garbage collection

- No segmentation faults
- No double free
- No use after free (dangling pointers)
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

# Memory Layout

- stack: stores local variables
- heap: dynamic memory for programmer to allocate
- data: stores global variables, separated into initialized and uninitialized
- text: stores the code being executed

<img src="content/images/memory_layout.png" alt="Memory Layout" width="250em">

???

- Each running program has its own memory layout, separated from other programs.
- The layout consists of a lot of segments, including ...

---

## The Stack

- Region of memory that stores function calls, local variables, and control flow.
- Automatic allocation & deallocation → when a function is called, its local variables are pushed; when it returns, they’re popped.
- Fast access, but limited size.
- Variables must be known at compile time (size fixed).

???

- Every time a function is called, the machine allocates a stack frame.
- Push to the stack each local var. There are more things pushed to stack, to simplify locals :).
- After the function returns, the stack frame is decallocated. So all variables become invalid.

---

## The Stack

<img src="content/images/stack1.png" alt="Stack 1" width="500em">

---

## The Stack

<img src="content/images/stack2.png" alt="Stack 2" width="500em">

---

## The Stack

<img src="content/images/stack3.png" alt="Stack 3" width="500em">

---

## The Stack

<img src="content/images/stack4.png" alt="Stack 4" width="500em">

---

## The Stack

<img src="content/images/stack5.png" alt="Stack 5" width="500em">

---

## The Stack

<img src="content/images/stack6.png" alt="Stack 6" width="500em">

---

## The Stack

<img src="content/images/stack7.png" alt="Stack 7" width="500em">

---

## The Stack

<img src="content/images/stack8.png" alt="Stack 8" width="500em">

---

## The Heap

- Region of memory for dynamic allocation (objects, arrays, structures).
- Managed manually (e.g., malloc/free in C, new/delete in C++, garbage collector in Java/Python).
- Slower than stack, but much bigger and flexible.
- Can live beyond the function scope (until explicitly freed or collected).

???

- Store things more permanent, longer than a function call without copying.
- Store dynamic memory
- Manually malloc/free
- Potential memory leaks, double free, use after free, etc

---

## The Heap

<img src="content/images/heap1.png" alt="Heap 1" width="500em">

---

## The Heap

<img src="content/images/heap2.png" alt="Heap 2" width="500em">

---

## The Heap

<img src="content/images/heap3.png" alt="Heap 3" width="500em">

---

## The Heap

<img src="content/images/heap4.png" alt="Heap 4" width="500em">

---

## The Heap

<img src="content/images/heap5.png" alt="Heap 5" width="500em">

---

## The Heap

<img src="content/images/heap6.png" alt="Heap 6" width="500em">

---

## The Heap

<img src="content/images/heap7.png" alt="Heap 7" width="500em">

---

## The Heap

<img src="content/images/heap8.png" alt="Heap 8" width="500em">

---

# "Manual" memory management in Rust:

- Values **owned** by creator.
- Values **moved** via assignment.
- When final owner returns, **value and resources are freed**.

All this feels invisible and prevents _double free_ errors, _use after free_ errors and _memory leaks_.

???

- Move semantics / RAII
- Rust enforces the RAII discipline
- Variables can own resources

---

# Ownership

- Each value in Rust has an owner.
- There can only be one owner at a time.
- When the owner goes out of scope, the value will be dropped.

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

???

- Ownership is a set of rules that govern how a Rust program manages memory
- Rules

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

# Move semantics

```rust
fn foo() {
    let x = 5;
    let y = x;

    println!("{x}");
    println!("{y}");
}
```

- Bind the value 5 to x
- Copy the value in x and bind it to y
- 2 variables, x and y, both equal 5

???

- This is indeed what is happening, because integers are simple values with a known, fixed size, and these two 5 values are pushed onto the stack.

---

# Move semantics

```rust
fn foo() {
    let s1 = String::from("hello");
    let s2 = s1;

    println!("{s1}");
    println!("{s2}");
}
```

- Does this works the same way?

--

<img src="content/images/string1.svg" alt="string 1" width="250em">

---

# Move semantics

```rust
fn foo() {
    let s1 = String::from("hello");
    let s2 = s1;

    println!("{s1}");
    println!("{s2}");
}
```

- This is **not** what happens

<img src="content/images/string2.svg" alt="string 2" width="180em">

---

# Move semantics

```rust
fn foo() {
    let s1 = String::from("hello");
    let s2 = s1;

    println!("{s1}");
    println!("{s2}");
}
```

<img src="content/images/string3.svg" alt="string 3" width="250em">

???

- This is what happens but we've said that there's only one owner.
- So ...

---

# Move semantics

```rust
fn foo() {
    let s1 = String::from("hello");
    let s2 = s1; // s1 moved here

    println!("{s1}"); // can't access moved value
    println!("{s2}");
}
```

<img src="content/images/string4.svg" alt="string 4" width="250em">

???

- s1 is moved, no access allowed to it anymore.
- Compilation error.
- The value can be a simple copy value when we don't care or the value is not copy and then move semantics apply

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

???

- Just with ownership we would need to return things back

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
- These rules are enforced by the borrow checker

---

# What about concurrency?

```rust
use std::thread;

fn main() {
    let mut s = String::from("Hello");
    
    thread::spawn(move || {
        s.push_str(" World!");
    });

    println!("{s}");
}
```

---

# What about concurrency?

- Same principles apply

```code
error[E0382]: borrow of moved value: `s`
  --> src/main.rs:10:16
   |
 4 |     let mut `s` = String::from("Hello");
   |         ----- move occurs because `s` has type String, which does not implement the Copy trait
 5 |     
 6 |     thread::spawn(move || {
   |                   ------- value moved into closure here
 7 |         `s`.push_str(" World!");
   |          - variable moved due to use in closure
...
10 |     println!("{`s`}");
```

???

- Nothing special about it.

---

# No null pointers

```rust
fn print_first(v: Vec<String>) {
  let s = v.first();
  println!("{}", s.to_uppercase());
}
```

--

```code
error[E0599]: no method named `to_uppercase` found for enum `Option`
       in the current scope
   --> src/main.rs:3:20
    |
3   |   println!("{}", s.to_uppercase());
    |                    ^^^^^^^^^^^^ method not found in `Option<&String>`
    |
note: the method `to_uppercase` exists on the type `&String`

For more information about this error, try `rustc --explain E0599`.
```

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
    Some(s) => println!("{}", s.to_uppercase()),
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

# Strings (&str vs String)

- &str static and read-only (primitive type)
  - Lives in the .rodata segment of memory
- String dynamic (stdlib)
  - Lives in the heap

```rust
fn main() {
    let a = "hi"; // &str

    a.push_str("something"); // compile error

    let b = String::from("hi"); // String

    b.push_str(" world");
}
```

???

- UTF-8
- str think of it as struct str([u8])
- String a Vec<u8>

---

# Generics

```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };

    println!("p.x = {}", p.x());
}
```

???

- Performance: monomorphization

---

# Traits

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}

pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}
```

---

# Iterator & closures

```rust
fn main() {
    let v1: Vec<i32> = vec![1, 2, 3];

    let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

    assert_eq!(v2, vec![2, 3, 4]);
}
```

???

- Closures capture upvars from environment
- Rust iterator and fp concepts: is a monad, applicative and functor 

---

# Smart pointers

- Box<T>, Rc<T>, Arc<T>, Ref<T>, RefMut<T>, etc
- Box<T> to store data in the heap
- Dereferences using `*` like normal refereces because implements Deref trait

```rust
fn main() {
    let b = Box::new(5);
    println!("b = {b}");
}
```

---

class: center
name: title
count: false

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto">

# Thanks

.grey[Github/Everywhere: spastorino]<br/>
.grey[Email: spastorino@gmail.com]
