# Proposing a stable modularizable ABI interface for Rust

Based on the points from the discussion [here](https://users.rust-lang.org/t/modular-abi-for-rust/42590).

## Introduction

Rust is a powerful systems programming with strong memory guarantees. Rust allows for concise expression at a high-level, while still producing fast low-level code. However, Rust does not guarantee the calling conventions and layout of structures in memory, which makes it difficult to write external applications that interface with Rust; Rust lacks a standardized ABI. Standardizing Rust's ABI has been brought up before, but has usually gone nowhere due to the difficulty of the task. In this post, we outline the benefits and stumbling-blocks of a stable ABI, as well as suggest a semi-novel technique as to how such an ABI could be implemented.

## Benefits

There are many benefits a standardized ABI would bring to Rust. A stable ABI enables dynamic linking between Rust crates, which would allow for Rust programs to support dynamically loaded plugins (a feature common in C/C++). Dynamic linking would result in shorter compile-times and lower disk-space use for projects, as multiple projects could link to the same dylib. For example, imagine having multiple CLIs all link to the same core library crate.

Although this use case is already rather well covered by [`abi-stable-crates`](https://github.com/rodrimati1992/abi_stable_crates), there are still many more benefits beyond linking crates dynamically. A stable ABI would allow Rust libraries to be loaded by other languages (such as Swift), and would allow Rust to interop with libraries defined in other programming languages. Non-Rust crates could be integrated with Rust toolchains; providing an ABI would also allow outside code to rely on Rust for performance-intensive tasks. Cross-language compatibility would increase the diversity of Rust's package ecosystem.

> **[Quote](https://internals.rust-lang.org/t/dynamically-linking-rust-crates-to-rust-crates/10369/8?u=zicklag):**  Imho one of the biggest mistakes C++ ever made was not stabilizing its abi; swift just stabilized theirs and is already reaping the benefits, swift system libraries, the swift runtime, swift UI libraries, all dynamically linked and backwards abi compatible.

Stabilizing the Rust's ABI would allow for cross language interop and dynamic linking. " `extern "C"`  as the lowest common denominator is too low for Rust" (**[Quote](https://internals.rust-lang.org/t/dynamically-linking-rust-crates-to-rust-crates/10369/35?u=zicklag)**).

Recently, the Fuschia OS Team at Google decided to ban Rust's for use in Fuschia microkernel, citing C as an alternative because of its stable ABI. Not providing a stable ABI ultimately hurts Rust when getting down to the metal. Given similar languages like C and Swift have a stable ABI, I see no reason why a stable ABI would not be implementable for Rust. As discussed [here](https://internals.rust-lang.org/t/dynamically-linking-rust-crates-to-rust-crates/10369/38), some ABIs/FFIs have already been written using proc macro and the like.

## Potential Issues

However, a stable ABI is not all peaches and roses. Having to standardize the memory layout of data can limit the number of optimizations the compiler can perform.There has been a lot of work on [optimizing laying out fields in structs](http://www.catb.org/esr/structure-packing/) in reliable and ABI-compliant ways. There are a large class of optimizations that can be done in compliance with an ABI; since an ABI solidifies the layout of data, more reliable bit-twiddling and the like can occur.

While discussing the matter, a point was brought up that the ABI could be modularized. A modularized ABI would be optional while compiling. This modular ABI could be published as a versioned crate. If the ABI ever needs a backward-compatibility breaking change, the change could be made within Semver. Alternatively, a new ABI-compliant compiler backend could be developed, or the current compiler backend could be extended to support an ABI feature flag that would toggle ABI compliant builds.

However:

> **[Quote](https://users.rust-lang.org/t/modular-abi-for-rust/42590/12?u=isaac):** Depending on the implementation, if we want to make ABI plugins, to avoid stabilizing the compiler's built-in ABI, we might run into another problem because we have to stabilize the plugin interface, which could be another can of worms.


Standardizing the ABI would take a lot of work. A poorly designed ABI is worse than not having an ABI at all. And as we all know, the right solution is often the hardest one.

Another downside is that allowing ABI crates might not stabilize Rust's ABI, there'd just be ABI fragmentation. Although this is a genuine concern, a 'master' ABI crate with Rust's 'official' ABI could be developed. This would standardize Rust's ABI, while still allowing other crates with other ABI's to be written for interop with other ABIs, like Swift's. Additionally, because modular ABIs are opt-in, ABIs would be used only where explicitly necessary.

## Implementation Proposal

So, what might this modularized ABI look like? Roughly speaking, an ABI would be defined by a series of macros in a crate which specify the layout and calling conventions of data structures according to that ABI. During compilation, while determining the layout of the data, the layout information provided by the ABI macros would be used. The end-goal would be for something like  `#[repr(RustABI)]`  or  `$ cargo build --release --abi rust-abi`  to be plausible.

Let's get into more detail. Right now, the closest analogue to a stable Rust ABI is the `abi_stable` crate. `abi_stable` uses `#[repr(C)]` to create ABI-compatible data structures. This is a step in the right direction, but every ABI-complaint type has to pass through `abi_stable`'s mechanisms. These data-structures are also more expressively limited. For example, every `abi_stable` ABI struct has to contain ABI compatible fields - and some Rust types, like `Result`, aren't compatible at all.

A modular ABI could solve this issue. An "ABI" Rust crate is a proc-macro-like crate that determines exactly how each byte of a data-structure should be laid out in memory. To do this, the "ABI" crate should provide a macro each standard Rust data-structure (`struct`, `enum`, `tuple`, etc.) When a data-structure is marked as ABI-compliant (either through a `#[repr(ABI)]` proc macro or compiler flag), the compiler calls out the "ABI" crate which recursively lays out said data-structure in an ABI-compliant manner.

There are a few issues that still need to be addressed. How do pointers and memory management work across FFI boundaries? We propose that when ABI-compliant data is transferred across an FFI boundary, it should be either copied or moved. Once some data has moved across an FFI boundary, the only way to reference that data is to use the copy, or have the program the data was transferred to transfer it back. This copy/move borrowing technique is merely a suggestion, as there is probably a better way to do it ([semi-related post](https://internals.rust-lang.org/t/sketch-minimal-pimpl-style-stable-abi/10952)).

To determine the layout of data for Rust's own ABI, a minimum API would have to be found. Rust currently provides many niche optimizations and field ordering techniques to increase performance - a stable ABI might interrupt or prevent some of this. However, as mentioned in the Potential Issues section, there are ways to work around this. Different calling conventions could be supported through a proxy assemble stub or the like, but the [devil's always in the details](https://internals.rust-lang.org/t/dynamically-linking-rust-crates-to-rust-crates/10369/38?u=zicklag).

Multiple ABI crates would be able to be defined—for example, there could be an `abi_swift` crate for interop with Swift's ABI—Rust itself could have it's own ABI in an ABI crate titled `abi_rust` or the like.

> **[Quote](https://users.rust-lang.org/t/modular-abi-for-rust/42590/33?u=isaac):** The potential to have different ABIs (e.g., `abi_rust`, `abi_swift`) that are used concurrently in the same compilation would permit Rust programs to act as the "glue" between external components that use incompatible ABIs.

## Closing Thoughts
We hope that this outline of a very rough specification will provide a launching point for the ultimate development of a stable modularized ABI interface for Rust. Such an ABI would expand the number of applications that Rust could be used for. A stable ABI would standardize dynamic linking between Rust crates, minimize the amount of space-time used during compilation, allow for cross-compatibility between Rust and other programming languages, and increase the plausibility of Rust as a kernel-level language. Something like this takes hard work and good communication, so if you have any questions, comments, concerns, feedback, or other ideas, please don't hesitate to share.
