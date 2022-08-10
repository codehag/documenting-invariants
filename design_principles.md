# Design Principles

## Enforcement of the order of evaluation of the module graph for modules.

### Description : A long, full description of the invariant and it's purpose

A host is always allowed to run operations on a job queue, regardless of the host hooks provided by JS the language. In 2017, november, the question was if node was allowed to reorder the module graph so that all commonjs modules evaluate before all js Modules evaluate (this would have allowed named exports to be created for those modules) was brought to the committee because it was strange to change the order of evaluation. The spec does not have a prohibition, but the spec is unable to prohibit it. It Cannot be considered an invariant, but it is a design intent. The clear result was a desire to not ensure forward compatibility guarantees if re-ordering as an approach was taken.

### Specification Details : Which specifications are affected by this invariant

TODO

### Rationale : Why did we have this invariant?

To maintain the ability to be Forward Compatible

## Application of Non-determinism

### Description: A long, full description of the invariant and it's purpose

We designed the WeakMap, WeakRef split from the beginning to separate the deterministic economizing on space from the non-deterministic observability of GC decisions. [1][2][3][4]

The non-determinism of WeakRefs present a *severe* problem that postponed their adoption until WASM. Any program making use of WeakRefs might contain a bug that never happens under exhaustive testing, but then does in production because of an unrelated change.

Any non-deterministically enumerable weak collection presents *exactly* the same hazard. Thus, any such non-deterministic abstraction should be used with great caution, and clearly separated from abstractions intended for normal use.

The co-existence of WASM and JS forced us to face the problem of acyclic GC across the boundary between the two systems. This was compelling enough that the browser makers were willing to eat the non-determinism hazard. Only then could WeakRefs advance.

JS may be unique among languages in that even the enumeration order of Maps and Sets is deterministic --- not dependent on non-deterministic identity hashes, and not even exposing the non-deterministic identity hashes themselves.

### Specification Details: Which specifications are affected by this invariant


### Documentation

[1]: https://web.archive.org/web/20160731015502/http://wiki.ecmascript.org/doku.php?id=harmony:weak_maps
[2]: https://web.archive.org/web/20160306175520/http://wiki.ecmascript.org/doku.php?id=strawman:weak_refs
[3]: https://web.archive.org/web/20160402121406/http://wiki.ecmascript.org/doku.php?id=strawman:weak_references
[4]: https://web.archive.org/web/20160402121406/http://wiki.ecmascript.org/doku.php?id=strawman:weak_references#a_weakvaluemap


### Rationale : Why did we have this invariant?

TODO

## Avoid / Hide WTF-16
### Description : A long, full description of the invariant and it's purpose

Although JavaScript strings are technically WTF-16, newer JavaScript language features try to avoid exposing this, instead working on the basis of Unicode code points. Examples include: the introduction of the code-point-based String[Symbol.iterator](); the API codePointAt() to replace charAt(); the API fromCodePoint() to replace fromCharCode(). There's even Intl.Segmenter APIs to get grapheme-cluster-based measurements (e.g. counting the number of characters).

### Specification Details : Which specifications are affected by this invariant


### Known Violations

Older APIs do not hide WTF-16 encoding behavior.

### Rationale : Why did we have this invariant?

WTF-16 behavior is surprising. We want to avoid surprises.

----

# Template

``` markdown

## Title

### Description : A long, full description of the invariant and it's purpose

### Specification Details : Which specifications are affected by this invariant

### Known Violations

### Rationale : Why did we have this invariant?

```
