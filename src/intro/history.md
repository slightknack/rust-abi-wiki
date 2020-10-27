# History
Before we get started, I think it's important we highlight the loose history of the proposal. This will make it easier for new people to get indoctrinated into the conversation we've had thus far.

For those unfamiliar with Application Binary Interfaces, please read [the FAQ](what_is_an_ABI.html).

## Pre IRLO discussion
So far, there have been three primary stages in the history of this proposal. The idea for a stable ABI arose in a discussion in the Rust User's forum about programming languages that pair well with Rust. Inevitably, the discussion of FFIs arose, which lead to an constructive debate about Rust's ABI, from which then arose the [initial proposal](initial_proposal.html).

## IRLO disussion
On May 14th 2020, the Initial Proposal was posted to IRLO, where it received quite a lot of attention. There was a lot of discussion surrounding the proposal; so much, in fact, that the discussion became very deep and general at the same time - everyone attacked the problem from a different angle, and, although, after a 3 month period, the conversation subsided and a general consensus was reached, the length and complexity of the intertwining narratives was a bit hard to follow, to say the least. Now that the idea and interest were there, what was needed next was a bazaar-style, collected and directed effort towards enumerating design decisions and painting a clearer picture of what needed to be done. Hence, the Wiki.

## Rust ABI Wiki
On October 23rd, A new effort to summarize the discussion and develop a solid, technical RFC. This effort is divided into three parts: First, the discussion on IRLO needs to be summarized and woven together into a coherent narrative; second, using the outline of the discussion, the relevant parts of the current Rust Compiler Infrastructure need to be targeted and Understood; finally, a terms must be defined and a solid RFC must be created.

## The Path Ahead
Once this RFC has been created, this wiki will have fulfilled its purpose, and we can move towards following the RFC to create an actionable implementation. This does not mean that no experimentation should occur in the interim - au contraire - rather, a solid actionable plan is needed before any extensive implementation can be developed.
