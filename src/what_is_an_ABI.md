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

- **Fully specified** - There should be no ambiguous cases, either in how to map Rust to the ABI, or how the ABI maps back to Rust objects.
- **Versioned** - There should be a mechanism to determine which version of the ABI a chunk of code is using by querying the code itself.  The version should follow [semver](https://semver.org/) conventions, and should be both machine and human parseable, so that linkers and loaders can decide if two pieces of code are compatible (from an ABI standpoint).  Under some circumstances, it may be possible for a linker or loader to generate code to bind chunks of code together that have different ABI versions.
- **Designed for introspection** - There are tools for different operating systems to analyze blobs of code to determine what they do.  For Linux, there are [nm](https://linux.die.net/man/1/nm), [objdump](https://linux.die.net/man/1/objdump), and a number of other utilities.  Unfortunately, the tools are limited by the knowledge encoded in the executable formats; in most cases you can't read the docs for a library by reading the library itself.  A useful addition would be the ability to store the documentation for a library in one of the segments of the library, with tools that are able to find and display that information.

## Additional useful traits

While the ABI we're developing is primarily for the Rust language, it need not be solely for the rust language.  Here are some additional traits that the ABI could have that might be useful.

- **Useful outside of rust** - There are numerous languages with numerous ABI specifications.  In some cases, those ABI specifications have been adopted by other languages, even when the ABI is not the best fit for the other languages.