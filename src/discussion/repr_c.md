# `repr(C)` as the Lowest Common Denominator

## Headers

> In the C world, there is a relatively simple relation between these two aspects: everything you put into ("public") headers is part of the ABI, and if you want the ability to e.g. change the layout of a type without breaking ABI compatibility, then you need to make that struct an opaque type in the headers and e.g. expose getter/setter functions for fields whose existence you want to guarantee.
>
> - @hanna-kruppe, IRLO
