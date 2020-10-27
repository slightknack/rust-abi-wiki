# Memory Management Across FFI
An issue with an FFI is that any memory transferred across boundaries is, in essence, being managed by two programs at once. This is a bit of an issue - Rust, as a language, provides strong memory guarantees - and the point of a clean FFI API is to make it as simple as possible to use; an `unsafe` block should not need to wrap every call to and from an external ABI.

With that in mind, what is the solution to this? How can data be transferred across programs without violating data lifetimes?

Once some data has been *moved* across a FFI boundary, the program that passed the data on no longer retains ownership of that data. This is similar to move semantics in closures, etc.

What about pointers?

> The only consistent answer to that is that the calling convention of the function pointer (extern "Rust", extern "C", extern "Swift") is part of the function type, the same as layout (#[repr(Rust)], #[repr(C)], #[repr(Swift)]) is part of the struct type.
>
> — @CAD97, IRLO

> TODO: reread through discussion and expand on this point.
