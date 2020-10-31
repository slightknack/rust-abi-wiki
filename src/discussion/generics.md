# Generics

> > The way Rust compiles generics, for example, necessarily inlines lots of "internal" library code (full of hard-coded field offsets, type sizes, etc.) into consumers of the library.
> 
> Addendum: when I wrote this sentence I also fell into the trap of focusing just on the "ABI" in the narrow sense of choices made while mapping Rust to machine code. Just as important is the fact that the library's code is copied into the consumer at all. So if a bug is fixed in the library, even if you can recompile that library and re-link everything (because you went through great effort to make type sizes and calling conventions and so on stable), the bug fix still won't reach the applications that use the library. Or will reach it inconsistently depending on where a fresh copy of the generic code was instantiated and where an existing copy of the code was reused (see: -Zshare-generics flag).
> 
> — `@hanna-kruppe`, IRLO

> Generics are not a problem as long as all types are known in the interface exposed through the ABI. So it wouldn't be a problem to return `Result<i32, String>` or even `Result<(), Box<dyn Error>>` (assuming trait objects have defined ABI).
>
> They're only an issue for functions like `pub fn generic<T, E>() -> Result<T, E>` that can't be monomorphized on the library side. For these functions the options are:
> 
> - Just forbid them. Library interfaces would have to use dyn Trait or concrete types instead. IMHO this is quite sensible limitation, especially for an MVP.
> 
> - Require defining ahead of time which parameters can be used, and compile monomorphic versions just for these types (similar to template instantiations in C++)
> 
> - Do what Swift does and compile a universal version of the generic function that uses run time type information to support arbitrary types. It's a very clever approach from ABI perspective, but it's equivalent of changing everything into dyn Trait, so it may be a poor fit for Rust.
> 
> — `@kornel`, IRLO

>>Just forbid them. Library interfaces would have to use dyn Trait or concrete types instead. IMHO this is quite sensible limitation, especially for an MVP
> 
> I think doing that is very reasonable. Then the library developer could still get the second and third behaviors in many cases by doing something like:
>
> ```
> pub fn int_generic() -> Result<i32, Box<dyn Error>> {
>     return generic<i32, Box<dyn Error>>();
> }
> 
> pub fn dyn_generic() -> Result<Box<dyn Any>, Box<dyn Error>> {
>     return generic<Box<dyn Any>, Box<dyn Error>>();
> }
> ```
> — `@tmccombs`, IRLO

>> - Just forbid them. Library interfaces would have to use dyn Trait or concrete types instead. IMHO this is quite sensible limitation, especially for an MVP.
>> - Do what Swift does and compile a universal version of the generic function that uses run time type information to support arbitrary types. It's a very clever approach from ABI perspective, but it's equivalent of changing everything into dyn Trait , so it may be a poor fit for Rust.
>
> Both these options make sense; generally speaking, I think Rust is leaving a lot of potential on the table with its current dyn-safe rules. Finding a way to make more traits dyn-safe (for instance, by adding workarounds to the "no associated constants" rule) would help with both of the above options.
>
> Also, implementing the second one would probably help a lot with compile times in debug mode, since it removes the need for monomorphizing most template functions.
> 
> — `@PoignardAzur`, IRLO

> How about defining a stable ABI subset for "easy" parts of Rust, such as non-inline functions, non-generic structs?
>
> Then std could be split internally into std-abi-stable and std-hard-parts. Crates could link to std-abi-stable dynamically to reduce bloat, while linking std-hard-parts statically. I imagine over time, as more ABI and std features are set in stone, more things would be moved to std-abi-stable part.
>
> For example, path functions in libstd are generic for P: AsRef<Path>, but std already uses a pattern of
> ```
> fn join<P>(&self, p: P) { self._join(p.as_ref()) }
> ```
> to reduce generics bloat and defer implementation to non-generic _join method. That non-generic implementation could be moved to an ABI-stable library without need to support generics in the ABI.
>
> This is similar to how C libraries use macros and inline functions in the .h files. The header files are the API, but not everything is in the ABI. The ABI is only for whatever these macros expand to.
> 
> — `@kornel`, IRLO

> @kornel I do want to see a "safe ABI" subset, which is bigger than C and smaller than fully general Rust.
>
> However, having a shared library for cases like _join would require making the internal _join interface stable, which would increase our stability surface area. And in practice, I don't think most people who want shared library support specifically want it to save disk storage space. They do sometimes want it to save RAM, and a shared library might help a little with that when multiple Rust programs are running simultaneously. But mostly, shared libraries make distribution maintenance easier: you can upgrade a library without rebuilding the world. This wouldn't necessarily solve that problem. And I think it would in practice make distribution of Rust binaries harder for many people, because then they'd need to supply the matching library.
> 
> — `@josh`, IRLO
