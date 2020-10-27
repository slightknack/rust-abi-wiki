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

The C ABI is the oldest ABI, dating back to C. The C ABI can be used from Rust through the use of `extern "C"`. The C ABI, however, is fairly lacking, as it only provides basic datatypes and is rather inflexible.

However, it's important to note that 'ABI' in of itself is a bit of an overloaded term:

> More concretely: there are broadly two clusters of meanings for "stable ABI", the first referring to how language concepts are mapped machine code (e.g. data structure layout, calling conventions) and the latter to the ability to change (upgrade) a software component without rebuilding all the software that interacts with this component. The second is ultimately the responsibility of programmers (e.g. deleting a function from a library breaks both source compatibility and ABI compatibility), but since it's required for many of the benefits ascribed to "stable ABI" and requires compiler support too, anyone pondering this subject should either explicitly acknowledge that aspect (and everything that it enables) as out of scope, or think about what it entails -- which goes far beyond "freezing" data structure layouts, calling conventions, etc.
>
> - @hanna-kruppe, IRLO

## What does it mean for an ABI to be stable?
A stable ABI means that a compiler will consistently produce the same ABI for an executable across versions.

Right now, this invariant is not present in the Rust Compiler, as the compiler may perform certain memory-layout optimizations (such as zero-sizing), different across versions.

## Why is a stable ABI a good thing?
To quote from the initial proposal:

> There are many benefits a standardized ABI would bring to Rust. A stable ABI enables dynamic linking between Rust crates, which would allow for Rust programs to support dynamically loaded plugins (a feature common in C/C++). Dynamic linking would result in shorter compile-times and lower disk-space use for projects, as multiple projects could link to the same dylib. For example, imagine having multiple CLIs all link to the same core library crate.

> TODO: more recent examples.

## What would a stable modular ABI look like for Rust?

## What's the difference between an ABI and an API/FFI?
We can go on all day talking about how *nice* and ABI would be, but after you say (or rather write) the word a couple hundred times, it loses it's meaning and rather encapsulates a set of rather lofty ideals.

This shouldn't be the case.

An ABI is something quite simple on the surface level: the memory layout and calling conventions of an executable. It can also be used in a broader sense, but it's best to be specific.

The FFI is the user-code interface the language provides to interact with other executables, which may have a different ABI. The FFI forms the transition layer between different ABIs. Currently, in Rust the FFI is most likened unto `extern "C"` and `repr(C)`.
