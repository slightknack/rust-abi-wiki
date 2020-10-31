# rust-abi-wiki
A wiki about Rust's ABI and plans to stabilize it. Open to editing, all PRs will be merged. Read the [latest version of the wiki here](https://slightknack.github.io/rust-abi-wiki).

## Goals
To make an extensive wiki on the state of Rust's ABI:

- The current API provided
- The locations in the compiler infrastructure
- Ideas on how to improve the ABI

The lofty end goal is to create a Pre-RFC → RFC → Development Fork → Merged into Rust, perhaps at the next Edition boundary.

## Background
Please see the initial discussion: https://internals.rust-lang.org/t/a-stable-modular-abi-for-rust/12347.

This repo was started because of this comment: https://internals.rust-lang.org/t/a-stable-modular-abi-for-rust/12347/126.

Many people have given feedback and given their ideas. I plan to summarize existing points of discussion and write up a more general wiki.

Special thanks to the the commenters on the original discussion:
ckaran,
zicklag,
isaac,
@CAD97,
robinm,
tema2,
josh,
Tom-Phinney,
PoignardAzur,
Aloso,
mzabaluev,
HerrJemine,
kornel,
Nokel81,
hanna-kruppe,
bwb,
zackw,
InfernoDeity,
erlend_sh,
bjorn3,
comex,
Diggsey,
matklad,
HeroicKatora,
and the many more who laid the existing groundwork for this proposal to even be considered!

(If you contribute something, link your name above to wherever people can find you. If your name isn't there, add it!)

## Contributing

Create a new markdown document, named with the `snake_case.md` convention, and open a PR. I'll merge it right away, we'll worry about organization and polish as we go on.
