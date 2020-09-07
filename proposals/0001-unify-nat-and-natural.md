---
author: Rinat Stryungis
date-accepted: ""
ticket-url: "https://gitlab.haskell.org/ghc/ghc/-/issues/10776"
implemented: "Yes"
---

This proposal is [discussed at this pull request](https://github.com/haskell-core/core-libraries-proposals/pull/0>).
**After creating the pull request, edit this file again, update the number in
the link, and delete this bold sentence.**

# Proposal title

The changes in the proposal remove the separate built-in kind `Nat` 
in favor of promoted type `Natural`.


## Motivation

Existence of separated kind `Nat` and impossibility of promotion 
of usual `Natural` data type (and data constructors with `Natual` fields)
looks like an unnecessary and inconvenient complication. 
It forces to create redundant types with `Nat` fields in their constructors (it makes these types uninhabited in terms)
only for promotional purposes. This is more cumbersome and less safe than the promotion 
of the ordinary data type. 

## Proposed Change Specification

1. This patch removes the separate kind `Nat` and repaces it by promoted type `Natural`. 
2. Allows promoting of the data constructors with Natural numbers in their fields. 
3. Module `GHC.TypeNats` now contains type synonym `type Nat = Natural` for the backward compatibility 

## Examples

The pretty simple example: 

Before this change, we had to create a separate type for the promotion

    data C = MkC Natural
    data CP = MkCP Nat
    type CC = MkCP 1

But `CP` is uninhabited in terms.

Now the following code will be successfully type-checked:
    
    data C = MkC Natural
    type CC = MkC 1

## Effect and Interactions

1. Type checker plugins which work with the natural numbers now
should use ``naturalTy`` kind instead of removed ``typeNatKind``

2. The separate kind ``Nat`` is removed and now it is just a type synonym for
``Natural``. As a consequence, one must enable ``TypeSynonymInstances``
in order to define instances for ``Nat``.


## Costs and Drawbacks

## Alternatives

## Unresolved Questions

## Implementation Plan

Implementation has been already done. 
https://gitlab.haskell.org/ghc/ghc/-/merge_requests/3583

## Endorsements
