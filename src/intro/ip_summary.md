# Summary of the Initial Proposal
The title of the initial post was, quite succinctly, 'A Stable Modular ABI for Rust'. Let's go over each of these terms in a bit more detail to get a better picture of what we're dealing with.

## 'Stable'
Currently, Rust's ABI is only stable across same versions of the compiler. This means that binaries that are built with different versions can not be dynamically linked, as the compiler does not guarantee that executables built with different versions will share a common interface.

This proposal calls for the stability of such an ABI to be guaranteed, at the least allowing for the compiler to link to older dynamic libraries built with older versions of the same compiler.

## 'Modular'
A stable ABI is no good if it is not resilient to future change.

## 'ABI'

## 'for Rust'

## Line of Reasoning
Here's the line of reasoning behind the proposal, quoted:

> - A stable ABI would be really nice for Rust
> - But it would also be difficult to do
> - We propose a modular ABI, where compiler-time macros can determine the layout of datastructures so that they are ABI-compliant.
> - We discuss caveats
> - We ask for feedback.
>
> — `@isaac`, IRLO

## Difference Between the Initial Proposal and the General Consensus Now
After the initial proposal, quite a lot has changed. The initial proposal was very focused on using compile-time macros to determine memory layout. Since this proposal, it's become apparent that more than just memory layout is at stake, and new features must be introduced for FFI creation, binary linking, calling convention, niche expression, etc.
