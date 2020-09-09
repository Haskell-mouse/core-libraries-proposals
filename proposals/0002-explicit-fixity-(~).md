---
author: Rinat Stryungis
date-accepted: ""
ticket-url: "https://gitlab.haskell.org/ghc/ghc/-/issues/18252"
implemented: "Yes"
---

This proposal is [discussed at this pull request](https://github.com/haskell-core/core-libraries-proposals/pull/0>).
**After creating the pull request, edit this file again, update the number in
the link, and delete this bold sentence.**

# Specify the fixity of (~) and (~~)

Set the fixity of `(~)` and `(~~)` to 4, 
as it is consistent with the fixity of other equality operators, 
such as `(:~:)`, `(:~~:)`, and `(==)`.

## Motivation
There is an inconsistent behaviour of `(~)` operator. Operator `(:~:)` has an explicit 
fixity and we could write for example some math expression without brackets: 

```
Prelude Data.Type.Equality GHC.TypeLits> :kind! 5 + 5 :~: 10
5 + 5 :~: 10 :: *
= 10 :~: 10
```
But in case of similar type-level expression with `(~)` 
a user would get a probably unexpected type error:
```
Prelude Data.Type.Equality GHC.TypeLits> :kind! 5 + 5 ~ 10

<interactive>:1:5: error:
    • Expected kind ‘Nat’, but ‘5 ~ 10’ has kind ‘Constraint’
    • In the second argument of ‘(+)’, namely ‘5 ~ 10’
      In the type ‘5 + 5 ~ 10’
```


The issue could be solved by adding an explicit fixity for the `(~)` type operator.
It should be set equal to 4:

    infix 4 ~, ~~

And there are two reasons for that:

1. These fixities are equal to the corresponding fixities of `(:~:)`, `(:~~:)` and `(==)`.
2. They are less than casual arithmetic operators and more type-level arithmetic expressions could be written without brackets.

## Proposed Change Specification

1. Type operator `(~)` is now explicitly non-associative with fixity 4 
2. Type operator `(~~)` is now explicitly non-associative with fixity 4 

## Examples

1. `a ~ b ~ c` now would be rejected with an error message about fixity, not with a type error.
2. Expression `5 + 5 ~ 10` now would be parsed as `(5 + 5) ~ 10` instead of `5 + (5 ~ 10)`. 

## Effect and Interactions

Type operators `(~)` and `(~~)` now have a fixity consistent with other equality operators, thereby fixing the motivating example.

## Costs and Drawbacks

It is a breaking change and there are programs that would be parsed differetly. 
For example: 

```
Prelude> data a & b
Prelude> :kind! 1 ~ 2 & 3
1 ~ 2 & 3 :: Type
= (1 ~ 2) & 3
```

Now it would be rejected: 

```
<interactive>:1:5: error:
    • Expected kind ‘Nat’, but ‘2 & 3’ has kind ‘Type’
    • In the second argument of ‘(~)’, namely ‘2 & 3’
      In the type ‘1 ~ 2 & 3’
```

## Alternatives

We could choose any other fixity for these operators, but `infix 4` is consistent 
with fixities of the corresponding `(:~:)` and `(:~~:)` operators and even with the usual boolean operator
 `(==)`. 

## Unresolved Questions

None at the moment

## Implementation Plan

Implementation has been already done. 
https://gitlab.haskell.org/ghc/ghc/-/merge_requests/3610

## Endorsements
