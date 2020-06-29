# Abandoned Invariants

The purpose of this document is to collect invariants that have existed in the past, but been dropped for one reason or another. We may not remember why, so this field is optional. Please add invariants as you see fit using the template.

## `typeof` and the behavior of `==` and `===`

* **Description** :  if the result of `typeof` a and b were the same, the result from `==` and `===` should be the same. This was relaxed with value types.
* **Specification Details** : Was important during ES6 discussions
* **Rationale** : This is a guess -- Consistency.

----

## Template

* **Description** : A long, full description of the invariant and it's purpose
* **Specification Details** : When the invariant was added / removed
* **Rationale** : Why did we have this invariant?
