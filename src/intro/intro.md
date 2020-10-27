# Introduction
Welcome to the Rust ABI Wiki! This is a wiki about Rust's ABI and plans to stabilize it. This wiki is very much so a work-in-progress, and we're trying to flesh it out. If you think of anything, open an issue or pull request to discuss it further! Here is this Wiki's [Github Repository](https://github.com/slightknack/rust-abi-wiki).

## Overview
This wiki is currently organized into three sections: The *Introduction*, which is what you're reading now; this outlines the reasoning behind, history of, and terms used throughout the rest of this Wiki.

The *Discussion* pages summarize various points of discussion and link out to other resources for consideration. After reading this, you should have a pretty solid idea of the challenges we face and goals we have.

The *Rust Compiler* section outlines how the rust compiler currently works in regards to the ABI. This section should highlight all relevant parts of the compiler, and discuss how the ideas proposed via the discussion may be implemented.

With the Rust compiler infrastructure in mind, the *Towards an RFC* topic intends to form a precise proposal and actionable plan as to what needs to be done next. It's important that we understand the scope of what's possible:

> You can focus exclusively on that aspect if you want, but in that case you still need to think very carefully to avoid over-stating what use cases your proposal will actually enable.
>
> - @hanna-kruppe, IRLO

> So, tossing around the term "ABI" hides a world of work. And you need to consider ABIs you're not working on to preclude conflicts with the ones you are.
>
> - @vomlehn, IRLO

Although this Wiki serves as a general purpose knowledge-base and proposal silo, any the implications of any final proposals must be fully understood.
