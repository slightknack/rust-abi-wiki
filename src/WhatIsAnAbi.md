# What Even Is An ABI?
ABIs, FFIs, ABI APIs, what's the difference?
This document stands to serve as a FAQ as to what an ABI is, why they're important, and so on.

If you think of a question, add it. If you feel an answer is incomplete or lacking, raise an issue - or even better - a PR.

## So, what is an ABI anyway?
An ABI, or Application Binary Interface, is the public-facing API of an executable that determines how other programs can call into it.

Wikipedia explains this much more succinctly:
> In computer software, an application binary interface (ABI) is an interface between two binary program modules; often, one of these modules is a library or operating system facility, and the other is a program that is being run by a user.
>
> — Wikipedia

ABIs describe two main facilities:

- How data is laid out in memory
- How functions are called.

The C ABI is the oldest ABI, dating back to C. The C ABI can be used from Rust through the use of `extern "C"`. The C ABI, however, is fairly lacking, as it only provides basic datatypes and so on.

## What does it mean for an ABI to be stable?
A stable ABI means that a compiler will consistently produce the same ABI for an executable across versions.

Right now, this invariant is not present in the Rust Compiler, as the compiler may perform certain memory-layout optimizations (such as zero-sizing), different across versions.

## Why is a stable ABI a good thing?
To quote from the initial proposal:

> There are many benefits a standardized ABI would bring to Rust. A stable ABI enables dynamic linking between Rust crates, which would allow for Rust programs to support dynamically loaded plugins (a feature common in C/C++). Dynamic linking would result in shorter compile-times and lower disk-space use for projects, as multiple projects could link to the same dylib. For example, imagine having multiple CLIs all link to the same core library crate.

> TODO: more recent examples.

## What would a stable modular ABI look like for Rust?
