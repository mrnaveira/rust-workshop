# rust-workshop

## Introduction

### Memory safety matters
<figure>
<img src="https://msrc-blog.microsoft.com/wp-content/uploads/2019/07/image-1.png" alt="Trulli" style="width:60%">
</figure>

**Memory safety** is a big concern. As the above picture shows, [~70% of the vulnerabilities Microsoft assigns a CVE each year continue to be memory safety issues](https://msrc-blog.microsoft.com/2019/07/18/we-need-a-safer-systems-programming-language/). This is across *all* Microsoft products and using *all* languages. This types of problems include overflows, dereferencing issues and race conditions.

One may think that this is easily fixed just by using a high-level language (like JavaScript) instead of a low level-one (like C/C++). But this is not so simple because:
* High-level languages need runtimes, virtual machines and interpreters. Those tools *must themselves be written in low-level languages* and that's a source of the memory issues mentioned earlier.
* There is a *big performance penalty*.

### Enter Rust
Because of the memory safety concerns introduced earlier, it's important to have a low-level, totally memory safe language to replace C/C++ and that can provide top performance compared to them.

To address this, Rust was designed by Mozilla Research's Graydon Hore, with contributions from the likes of JavaScript creator Brendan Eich. Rust became the core language for some of the fundamental features of the Firefox browser and its Gecko engine, as well as Mozilla's Servo engine. 

Today, [Rust is considered the most-loved language among developers](https://insights.stackoverflow.com/survey/2021#technology-most-loved-dreaded-and-wanted) and is [used in production by many companies and products](https://www.rust-lang.org/production/users), including Mozilla Firefox, multiple Microsoft products, Dropbox, Discord, Coursera, Tor and recently even [in the Linux Kernel](https://security.googleblog.com/2021/04/rust-in-linux-kernel.html).

Rust was able to achieve **top performance and top safety** at the same time. But of course nothing is perfect, and Rust has its downsides as well.
The main downside of Rust is language ergonomics: assume that **any program written in Rust will be more difficult to write and understand** that if it was written in a high-level language. Also, the **learning curve** is very hard in Rust, as new developers need to understand memory management concepts like [ownership](https://doc.rust-lang.org/stable/book/ch04-00-understanding-ownership.html), [borrowing](https://doc.rust-lang.org/stable/book/ch04-02-references-and-borrowing.html) or [lifetimes](https://doc.rust-lang.org/stable/book/ch10-03-lifetime-syntax.html).

### Rust vs Go

Rust and Go are often compared as they both have very similar goals: safe and fast compiled languages with focus on concurrency.

As many benchmarks show, **Rust significantly out-performs Go** in a multitude of tasks, including [CPU computation](https://benchmarksgame-team.pages.debian.net/benchmarksgame/fastest/rust-go.html) as well as [I/O bound operations](ttps://medium.com/star-gazers/benchmarking-low-level-i-o-c-c-rust-golang-java-python-9a0d505f85f7). An important point is that **Rust performance is more stable** and predictable across time. An very good example of this is why [Discord switched from Go to Rust due to performance issues with Go's garbage collector](https://blog.discord.com/why-discord-is-switching-from-go-to-rust-a190bbca2b1f).

Go is considered a very safe language, but in this aspect the **Rust compiler checks are even more exhaustive**, as [the race condition issue that Microsoft found in Kubernetes Helm shows](https://msrc-blog.microsoft.com/2020/04/29/the-safety-boat-kubernetes-and-rust/).

The **main advantage of Go over Rust is a much easier learning curve**, as memory is handled automatically via the runtime/garbage collector. Rust forces the developer to learn and understand the memory ownership system.

As expected, there is not a clear winner between the two languages. You must weight in Rust performance and safety versus the easier learning curve of Go to make a decision for your particual project.

### So, when to use Rust?

In summary, the main use case that make Rust attractive is when you need to develop any software that has a **high demand for performance and/or concurrency**. Basically anything that in the past would be written in C/C++ but nowadays you want the memory safety that Rust provides:
* Powerful, cross-platform command-line tools.
* Distributed online services.
* Embedded devices.
* Anywhere else you would need systems programming, like browser engines.

Another important use case to mention is when you need to develop in **WebAssembly**. Rust is widely used for that purpose: has very small runtime, generates very efficient wasm code and the Rust WebAssembly tooling is very mature.

## Memory management exercises 
We will take a look a some basic examples of Rust code, to showcase the basic features of the language. We will focus on the memory safety constraints of Rust, which constitute the bigger part of the initial learning curve of the language. 

For this section there is no need to have Rust installed in you machine, you can simple use [**the online rust playground**](https://play.rust-lang.org/).

### Hello, world!
To get things started, lets write the typical Hello World example:
```rust
fn main() {
    println!("Hello, world!");
}
```
Run the above code in the [the online rust playground](https://play.rust-lang.org/). To know how to compile and run this code in a real environment you can check the [Rust reference](https://doc.rust-lang.org/book/ch01-02-hello-world.html).

The example defines a function in Rust. The `main` function is special: it is always the first code that runs in every executable Rust program. Here things start to get confusing, as due to limitations in the language we are using `println!` which is a **macro** and not a function (for more information on this, check the [rust reference book on macros](https://doc.rust-lang.org/book/ch19-06-macros.html#the-difference-between-macros-and-functions)).

### Ownership

The concept that truly separates Rust from other languages is the memory management mechanism called [**ownership**](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html). The Rust compiler has some very restrictive rules regarding memory (heap):
* Each memory value in Rust has **one, and only one variable that's called its owner**.
* There can only be **one owner** at a time.
* When the owner goes out of scope (i.e. the code block of the variable closes), the memory value will be automatically deleted.

The example code below will fail because of ownership rules.
```rust
fn main() {
    // fixed size types go into the stack, not the heap, so this does not generate ownership problems
    let x: i32 = 5;
    let _y: i32 = x; // this performs a copy
    
    // dynamic size types go into the heap, we need to handle ownership
    // Here, "s1" cannot be assigned directly to "s2" because the mamory value can only have one owner
    let s1: String = String::from("hello");
    let _s2: String = s1; // this line will give a compiler error
    
    println!("{}, world!", s1);
}
```
<blockquote>
<h5>Exercise 1</h5>
<p>Fix the above code by using <a href="https://doc.rust-lang.org/std/clone/trait.Clone.html#examples">the <b>clone</b> function</a> to make a copy of the memory value.</p>
<details>
  <summary>Solution</summary>
  
```rust
fn main() {
    let x: i32 = 5;
    let _y: i32 = x;

    let s1: String = String::from("hello");
    // "clone" returns a copy of the memory value 
    let _s2: String = s1.clone();
    
    println!("{}, world!", s1);
}
```
</details>
</blockquote>

Passing a variable to a function will move or copy, just as assignment does. The example code below will fail because of ownership rules. 

```rust
fn main() {
    // s comes into scope
    let s = String::from("hello");  

    // s's value moves into the function...
    takes_ownership(s);             
    
    // ... and so is no longer valid afterwards
    // even more, the memory value in the heap is deleted after the call finishes
   
    // The below code will fail to compile because the ownership was transferred
    println!("{}, world!", s);

}

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.
```

<blockquote>
<h5>Exercise 2</h5>
<p>Fix the above code using again the <b>clone</b> function to make a copy of the memory value.</p>
<details>
  <summary>Solution</summary>
  
```rust
fn main() {
    let s = String::from("hello");  
    takes_ownership(s.clone());             
    println!("{}, world!", s);
}

fn takes_ownership(some_string: String) {
    println!("{}", some_string);
}
```
</details>
</blockquote>

<blockquote>
<h5>Exercise 3</h5>
<p>This time, instead of making a clone, modify the <b>takes_ownership</b> function to <a href="https://doc.rust-lang.org/rust-by-example/fn.html">return the value</a> and reassign it again to the <b>s</b> variable. Variables in Rust are inmmutable by default, so you will also need to use <a href="https://doc.rust-lang.org/rust-by-example/fn.html">the <b>mut</b> keyword</a>.</p>
<details>
  <summary>Solution</summary>
  
```rust
fn main() {
    let mut s = String::from("hello");
    // ownership is returned to "s" after the call, so we can use the variable again
    s = takes_ownership(s);      
    println!("{}, world!", s);
}

fn takes_ownership(some_string: String) -> String {
    println!("{}", some_string);
    return some_string;
}
```
</details>
</blockquote>

The solutions we explored in exercises 2 and 3 are not ideal. Cloning a memory value (exercise 2) has a performance penalty and we lose the ability to modify the original value. Returning the value again (exercise 3) solves those problems but makes the code very difficult to manage when we are manipulating multiple values. In the next section we will explore a better solution, called **borrowing**.

### Borrowing

[Borrowing](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html) is the action of temporarily use a reference of a value wihout taking ownership of it. With borrowing, we have another way of solving the code of the previous section.

Find below the same problem of ownership of the previous section.
```rust
fn main() {
    let s = String::from("hello");  
    borrow(s);             
   
    // The below code will fail to compile because the ownership was transferred
    println!("{}, world!", s);
}

fn borrow(some_string: String) {
    println!("{}", some_string);
}
```

<blockquote>
<h5>Exercise 4</h5>
<p>Use <a href="https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html">borrowing</a> to pass a reference of the variable <b>s</b> to the function <b>borrow</b>.
<details>
  <summary>Solution</summary>
  
```rust
fn main() {
    let s = String::from("hello");  
    borrow(&s);             
    println!("{}, world!", s);
}

fn borrow(some_string: &String) {
    println!("{}", some_string);
}
```
</details>
</blockquote>

But what happens if we want to **modify** the value that we are borrowing? By default, Rust will not let you modify a borrowed value unless both the caller and the called explicitly declare it. The next example code will fail for that same reason:
```rust
fn main() {
    let s = String::from("hello");  
    borrow(&s);             
    println!("{}, world!", s);
}

fn borrow(some_string: &String) {
    // we are modifying the value, adding more characters to the string 
    some_string.push_str("ooooo");
}
```

<blockquote>
<h5>Exercise 5</h5>
<p>Use a <a href="https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html#mutable-references">mutable reference</a> to allow the function <b>borrow</b> to modify the string <b>s</b>.
<details>
  <summary>Solution</summary>
  
```rust
fn main() {
    // note that the variable must also be declared as mutable!
    let mut s = String::from("hello");  
    borrow(&mut s); // passing a mutable reference             
    println!("{}, world!", s);
}

fn borrow(some_string: &mut String) { // receiving a mutable reference
    some_string.push_str("ooooo");
}
```
</details>
</blockquote>

To prevent data races, the Rust compiler imposes [some restrictions on mutable references](https://doc.rust-lang.org/stable/book/ch04-02-references-and-borrowing.html#mutable-references):
* You can have only **one** mutable reference to a particular piece of data in a particular scope.
* You **cannot mix mutable and inmmutable** references.
* You can't return a function defined variable as a reference.

### Lifetimes
The Rust compiler keeps track of how long does a reference last to avoid a variable pointing to data that no longer exists (_dangling pointer_). That's called the [lifetime](https://doc.rust-lang.org/stable/book/ch10-03-lifetime-syntax.html), and begins when the reference is created and ends when the reference is last used. In most cases the lifetime is infered by the compiler so we, as developers, don't need to specify it.

But when it's not possible for the compiler to statically determine when to deallocate a value, we need to manually specify the lifetime. Consider the following example:
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
The result of the `longest` function cannot be determined at compile time. This means that if either of the arguments x or y don’t live long enough to be used safely, the compiler will let you know about it.

The code below will produce an error, since the lifetime of the references mismatch:
```rust
fn main() {
    let string1 = String::from("a very long string");
    let result;
    {
        let string2 = String::from("short string");
        result = longest(string1.as_str(), string2.as_str());
    }
    println!("The longest string is {}", result);
}

fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
<blockquote>
<h5>Exercise 6</h5>
<p>Modify the previous code to fix the compiler error about lifetemes, making the variables <b>string1</b> and <b>string2</b> be in the same scope.</p>
<details>
  <summary>Solution</summary>
  
```rust
fn main() {
    // both strings have the same lifetime, so they can be safely called by "longest" in any shorter scope
    let string1 = String::from("a very long string");
    let string2 = String::from("short string");
    let result;
    {
        result = longest(string1.as_str(), string2.as_str());
    }
    println!("The longest string is {}", result);
}

fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
</details>
</blockquote>

## Other Rust concepts
This workshop was focused on memory management exercises, probably the biggest part of the inital learning curve. But there are other concepts of Rust worth mentioning at this point.

### Concurrency
The examples in this workshop shows how the Rust compiler will not allow a memory value to be modified from different scopes. But what to do if we precisely want to read/write the same memory value multiple threads? One of the core goals of rust to allow [safe concurrency](https://doc.rust-lang.org/book/ch16-00-concurrency.html). To access the same value concurrently from multiple threads at the same time, the programmer must specify the synchronization mechanism to use: either [message passing](https://doc.rust-lang.org/book/ch16-02-message-passing.html) or [by shared state](https://doc.rust-lang.org/book/ch16-03-shared-state.html).
    
### Object orientation
[Rust is object oriented](https://doc.rust-lang.org/book/ch17-00-oop.html), but there is no subtyping and no inheritance of data in Rust. The relationships between various data types are established using [traits](https://doc.rust-lang.org/book/ch17-02-trait-objects.html).

```rust
trait Speaks {
     fn speak(&self);

     fn noise(&self) -> &str;
}

trait Animal {
    fn animal_type(&self) -> &str;
}

struct Dog {}

impl Animal for Dog {
    fn animal_type(&self) -> &str {
        "dog"
    }
}  

impl Speaks for Dog {
    fn speak(&self) {
        println!("The dog said {}", self.noise());
    }

    fn noise(&self) -> &str {
        "woof"
    }
}

fn main() {
    let dog = Dog {};
    dog.speak();
}
```
    
You can think of traits as interfaces. But unlike interfaces in languages like Java, new traits can be implemented for existing types. That means abstractions can be created after-the-fact, and applied to existing libraries.
    
There is also trait inheritance:
```rust    
trait Show {
    fn show(&self) -> String;
}

trait Location {
    fn location(&self) -> String;
}

trait ShowTell: Show + Location {}
```

### Error handling
[Error handling](https://doc.rust-lang.org/book/ch09-00-error-handling.html) in Rust is done by wrapping function return values in the `Result` enum type to later match the outcome from the caller:
```rust
use std::fs::File;
fn main() {
   let f = File::open("main.jpg");
   match f {
      Ok(f)=> {
         println!("file found {:?}",f);
      },
      Err(e)=> {
         println!("file not found \n{:?}",e);   //handled error
      }
   }
   println!("end of main");
}
```
To [better handle errors](https://nick.groenen.me/posts/rust-error-handling/) in Rust, there are usually two libraries that we will need:
* [thiserror](https://crates.io/crates/thiserror) to avoid writing lots of boilerplate code on custom error declaration.
* [anyhow](https://crates.io/crates/anyhow) to avoid boilerplate on error chaining in function calls.

### Asynchronous Programming
Asynchronous Programming is tipically used when we want to run many instances of a _small_ function concurrently (for example, listening to network requests), but we want to avoid creating OS thread for each execution to avoid the performance overhead. But keep in mind that [Async code should never spend a long time without reaching an `await`](https://ryhl.io/blog/async-what-is-blocking/) because each execution will block the thread (i.e. avoid expensive CPU processing), if that's the case it's better to stick to threads.

In Rust, [asynchronous functions return a `Future` value](https://rust-lang.github.io/async-book/01_getting_started/02_why_async.html#async-in-rust-vs-other-languages) similar to _promises_ in JavaScript. We can then specify how and when to block the thread execution.
```rust
async fn get_two_sites_async() {
    // Create two different "futures" which, when run to completion,
    // will asynchronously download the webpages.
    let future_one = download_async("https://www.foo.com");
    let future_two = download_async("https://www.bar.com");

    // Run both futures to completion at the same time.
    join!(future_one, future_two);
}
```    

It's important to mention that nowadays, most Rust developers still use [the Tokio runtime](https://tokio.rs/) instead of the official packages, mainly because it was released earlier and still has more features. But this is an evolving situation, so expect many examples and packages to use one or the other.
    
## Resources
* Text resources:
    * [Getting started](https://www.rust-lang.org/learn/get-started): how to install Rust in your machine.
    * [Rust By Example](https://doc.rust-lang.org/rust-by-example/): a collection of runnable examples that illustrate various Rust concepts.
    * [The Rust Programming Language](https://doc.rust-lang.org/book/): the official reference book.
    * [Async: What is blocking?](https://ryhl.io/blog/async-what-is-blocking/): a very good introduction to Async programming in Rust.
    * [Benchmarking low-level I/O: C, C++, Rust, Golang, Java, Python](https://medium.com/star-gazers/benchmarking-low-level-i-o-c-c-rust-golang-java-python-9a0d505f85f7): an up-to-date and very complete benchmark comparison of Rust and other languages.
* YouTube Videos:
    * [Rust: A Language for the Next 40 Years](): Carol Nichols, Rust Core Team developer, explains the motivation for the creation of Rust.
    * [Rust at Microsoft](https://www.youtube.com/watch?v=NQBVUjdkLAA): Ryan Levick explains why they, at Microsoft, are becoming more interested in Rust and why they see it as a potential game changer.
    * [Rust in the Browser for JavaScripters](https://www.youtube.com/watch?v=ohuTy8MmbLc): introduction of Rust for WebAssembly.
    * [Understanding Ownership in Rust](https://www.youtube.com/watch?v=VFIOSWy93H0): detailed explanation of how memory ownership and borrowing works in Rust.
* Repositories:
    * [Awesome Rust](https://github.com/rust-unofficial/awesome-rust): a curated list of Rust code and resources.
    * [Actix Web](https://github.com/actix/actix-web): powerful, pragmatic, and extremely fast web framework.
    * [Diesel](https://github.com/diesel-rs/diesel): safe, extensible ORM and Query Builder.
    * [Tokio](https://github.com/tokio-rs/tokio): runtime for writing reliable, asynchronous, and slim applications.
    * Error handling libraries:
        * [thiserror](https://github.com/dtolnay/thiserror): a convenient macro to declare custom errors
        * [anyhow](https://github.com/dtolnay/anyhow): easy idiomatic error handling
