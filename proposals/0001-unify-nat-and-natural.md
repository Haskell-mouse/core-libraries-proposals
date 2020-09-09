---
author: Rinat Stryungis
date-accepted: ""
ticket-url: "https://gitlab.haskell.org/ghc/ghc/-/issues/10776"
implemented: "Yes"
---

This proposal is [discussed at this pull request](https://github.com/haskell-core/core-libraries-proposals/pull/0>).
**After creating the pull request, edit this file again, update the number in
the link, and delete this bold sentence.**

# Unify Nat and Natural

Remove the separate built-in kind `Nat` 
in favor of promoting `Natural`.


## Motivation

Existence of separated kind `Nat` and impossibility of promotion 
of usual `Natural` data type (and data constructors with `Natural` fields)
looks like an unnecessary and inconvenient complication. 
It forces us to create redundant types with `Nat` fields in their constructors 
(it makes these types uninhabited in terms)only for promotional purposes. 
This is more cumbersome than the promotion of ordinary data types. 

## Proposed Change Specification

1. Remove the separate kind `Nat` and replace it with `Natural`. 
Type-level numeric literals now have the kind `Natural`, 
i.e. `p :: Proxy (1 :: Nat)` becomes `p :: Proxy (1 :: Natural)`. 
3. Module `GHC.TypeNats` now contains a type synonym `type Nat = Natural` for backwards compatibility.

## Examples

Before this change, we had to create a 
separate type for the promotion, which is uninhabited in terms. 

```
data C = MkC Natural
c1 = MkC 1        -- works
type C1 = MkC 1   -- doesn't work

data P = MkP Nat
p1 = MkP 1        -- doesn't work
type P1 = MkP 1   -- works
```

And after the proposal: 

```
data C = MkC Natural
c1 = MkC 1       -- works
type C1 = MkC 1  -- works
```

## Effect and Interactions

1. Type checker plugins which work with the natural numbers now
should use ``naturalTy`` kind instead of removed ``typeNatKind``

2. The separate kind ``Nat`` is removed and now it is just a type synonym for
``Natural``. As a consequence, one must enable ``TypeSynonymInstances``
in order to define instances for ``Nat``.


## Costs and Drawbacks
This is a breaking change.

For example, the following code would not be compiled due to the
conflicting instances. 

```
class C a
instance C Nat
instance C Natural
```
## Alternatives

Continue to maintain a separate Nat kind indefinitely.
However, this makes type-level programming with numeric literals less convenient than it could be.

## Unresolved Questions

None at the moment.

## Implementation Plan

Implementation has been already done. 
https://gitlab.haskell.org/ghc/ghc/-/merge_requests/3583

## Endorsements
