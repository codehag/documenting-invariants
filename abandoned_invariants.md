The purpose of this document is to collect invariants that have existed in the past, but been dropped for one reason or another. We may not remember why, so this field is optional. Please add invariants as you see fit using the template.

# Abandoned Invariants
Invariants that were broken

## Script Concatination

* **Description** :  Script Concatination can always have a comment added to it at the start without effect.
* **Specification Details** : Was contested in relation to hashbang grammer, but was not upheld.
* **Rationale** : Insertion of license comments.
* **Impact of breakage**: requires more complex parsing. 
* **Documentation**: See hashbang notes (TODO find links)

## Top lexical scope constructs OR Everything is generative

* **Description** : Prior to modules, any expression could be nested in a function and it would be created each time the function was called. Allowed you to take anything and put it into an IIFE and it would work. Anything that you can create, you can multiply instantiate. 
* **Break** : Since import / exports syntax were introduced, this was the first syntax that only appears at top level. it was the first syntax that could not be in the body of the function.
* **Specification Details** : Import / Export .
* **Rationale** : TODO.
* **Documentation**: (TODO find links)

## InstanceOf operator

* **Description** : instanceOf operator used to be a reliable way to . 
* **Break** : Introduction of symbols.
* **Specification Details** : Symbols.
* **Rationale** : TODO
* **Documentation**: (TODO find links)

## Object.prototype.ToString.call as a brand

* **Description** : toString used to be a brand check. 
* **Break** : Introduction of symbols.
* **Specification Details** : 
* **Rationale** : It was initially an accident, but it was useful as a brand check.
* **Documentation**: 

## Setters and Getters / Accessors

* **Description** : Spec invariant
* **Break** : All of the browsers had accessors, `__defineGetter__` `__defineSetter__`.
* **Specification Details** : ES5 rule that anything that is true in 3 out of the 5 browsers is a strong candidate for specification.
* **Rationale** : ...
* **Documentation**: 

# Potentially Abandoned Invariants
Invariants that may be broken soon

## `typeof` and the behavior of `==` and `===`

* **Description** :  if the result of `typeof` a and b were the same, the result from `==` and `===` should be the same. This was relaxed in the cases of anticipated value types and operator overloading.
* **Specification Details** : Was important during ES5 discussions
* **Rationale** : This is a guess -- Consistency.
* **Documentation**: Find documentation

----

## Template

* **Description** : A long, full description of the invariant and it's purpose
* **Specification Details** : When the invariant was added / removed
* **Rationale** : Why did we have this invariant?
* **Documentation**: 
