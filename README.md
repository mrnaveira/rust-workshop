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
The main downside of Rust is language ergonomics: assume that **any program written in Rust will be more verbose and difficult to write and understand** that if it was written in a high-level language.

### Rust vs Go

Go is much simpler and has a cleaner syntax than Rust, has good performance and is considered very safe (handles memory automatically and the runtime performs many security checks). 

So, why would you use Rust instead? There are two situations that could make it more appropriate:
* You need the best **performance** possible. As many benchmarks show, Rust is considerably faster than Go in most cases. An example of this is why [Discord switched from Go to Rust due to performance issues with Go's garbage collector](https://blog.discord.com/why-discord-is-switching-from-go-to-rust-a190bbca2b1f).
* Your application is extremely concurrent and you want the best possible **memory safety**. The Rust compiler checks are more exhaustive, as [the race condition issue that Microsoft found in Kubernetes Helm shows](https://msrc-blog.microsoft.com/2020/04/29/the-safety-boat-kubernetes-and-rust/).


### So, when to use Rust?

In summary, the main use case that make Rust attractive is when you need to develop any software that has a **high demand for performance and/or concurrency**. Basically anything that in the past would be written in C/C++ but nowadays you want the memory safety that Rust provides:
* Powerful, cross-platform command-line tools.
* Distributed online services.
* Embedded devices.
* Anywhere else you would need systems programming, like browser engines.

Another important use case to mention is when you need to develop in **WebAssembly**. Rust is widely used for that purpose: has very small runtime, generates very efficient wasm code and the Rust WebAssembly tooling is very mature.




