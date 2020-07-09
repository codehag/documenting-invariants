The purpose of this document is to collect invariants that have existed in the past, but been dropped for one reason or another. We may not remember why, so this field is optional. Please add invariants as you see fit using the template.

# Abandoned Invariants
Invariants that were broken

## Script Concatination

### Description

Script Concatination can always have a comment added to it at the start without effect.

### Specification Details

Was contested in relation to hashbang grammer, but was not upheld.

### Rationale

Insertion of license comments does not break.

### Impact of breakage

requires more complex parsing.

### Documentation

See hashbang notes (TODO find links)

## Top lexical scope constructs OR Everything is generative

### Description

Prior to modules, any expression could be nested in a function and it would be created each time the function was called. Allowed you to take anything and put it into an IIFE and it would work. Anything that you can create, you can multiply instantiate.

### Breakage

Since import / exports syntax were introduced, this was the first syntax that only appears at top level. it was the first syntax that could not be in the body of the function.

### Specification Details

Import / Export .

### Rationale

TODO.

### Documentation

(TODO find links)

## InstanceOf operator

### Description

instanceOf operator used to be a reliable way to .

## Breakage

Introduction of `Symbol.hasInstance`.

### Specification Details

See introduction of Symbols.

### Rationale

TODO

### Documentation

(TODO find links)

## Object.prototype.ToString.call as a brand

### Description

toString used to be a brand check. That `Symbol.toStringTag` would remove this invariant  was discussed immediately prior to ES6, and since all builtin types (or so we thought, at the time) had a prototype method that brand-checked and threw an exception, this was deemed sufficient for all future types - in other words, a new invariant was added at that time, that every builtin type must have a means by which it can be brand-checked, even if indirectly. The Error types remain the only (overlooked at the time) builtin types for which this does not hold.

### Breakage

Introduction of `Symbol.toStringTag`.

### Specification Details

TODO

### Rationale

It was initially an accident, but it was useful as a brand check.

### Documentation

TODO

## Setters and Getters / Accessors

### Description
Spec invariant
### Breakage

All of the browsers had accessors, `__defineGetter__` `__defineSetter__`.

### Specification Details

ES5 rule that anything that is true in 3 out of the 5 browsers is a strong candidate for specification.

### Rationale

TODO

### Documentation

TODO

# Potentially Abandoned Invariants
Invariants that may be broken soon

## `typeof` and the behavior of `==` and `===`

### Description

If the result of `typeof` a and b were the same, the result from `==` and `===` should be the same. This was relaxed in the cases of anticipated value types and operator overloading.

### Specification Details

Was important during ES5 discussions

### Rationale

TODO
This is a guess -- Consistency.

### Documentation

TODO
Find documentation

----

Template

## Title

### Description

### Specification Details

### Rationale

### Documentation

