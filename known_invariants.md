# Known Invariants, "musts"

The purpose of this document is to collect invariants that we know.  These are not "agreed" invariants. Those need to be discussed and integrated in the spec.

## Built-in objects must provide brand checks

### Description

Prior to ES6, `Object.prototype.toString.call` was used as a brand-checking mechanism. Rather than remove `Symbol.toStringTag` from the imminent ES2015 edition ([notes](https://github.com/tc39/notes/blob/8711614630f631cb51dfb803caa087bedfc051a3/meetings/2015-01/jan-28.md#45-tostringtag)), the committee agreed (although unfortunately this was not captured in the notes) that as long as builtins offered a method (prototype or static) that checked internal slots, and threw an exception when the internal slots were not found, this would suffice as a brand check.

### Specification Details
Which specifications are affected by this invariant? How do we detect violations?

### Known Violations

<sub>The only exception to this, unknown at the time, is Error (and associated Error subclasses) - `Error.isError` was [proposed](https://github.com/tc39/notes/blob/8711614630f631cb51dfb803caa087bedfc051a3/meetings/2016-03/march-29.md#erroriserror) to fix this, but rejected in favor of brand checks inside the [stacks proposal](https://github.com/tc39/proposal-error-stacks).</sub>

### Rationale : Why did we have this invariant?

TODO

## Cannot access partially initialized literals

### Description : A long, full description of the invariant and it's purpose
ECMAScript initializes properties ad-hoc per spec, but we never expose partially initialized literals...

This affects things like literals defining get/set pairs and avoiding them being seen as partially initialized.

```js
var _ = 0;
var obj = {
  get field() { return _; }
  // ...
  // No possible code can get a Reference to obj.field , so cannot see that set is not yet applied
  // The following line is not possible in this location (accessing `obj` prior to `set`)
  other: getRefToObjEarly().field = 1,
  // ...
  set field(v) { return _ = v; }
}
```

### Specification Details : Which specifications are affected by this invariant

TODO

### Known Violations

None

### Rationale : Why did we have this invariant?

Developer Mental Model.

This invariant makes it seem as if both [[Get]] and [[Set]] are populated at the same time. It greatly simplifies how to think about objects and avoids forcing defensive programming against partially initialized objects.

## General Purpose Language

The language is implementable independantly of the web platform.

### Specification Details : Which specifications are affected by this invariant

TODO

### Known Violations

None

### Rationale : Why did we have this invariant?

ECMAScript is useful for many applications beyond the web, including on microcontrollers. By
maintaining this invariant, we achieve the goal of maintaining a single language.

## Prototype of (async) iterators

When a built-in function creates an iterator, it should specify its prototype to be either %IteratorPrototype% or some %FooIteratorPrototype%.

Every %FooIteratorPrototype% should have %IteratorPrototype% on its prototype chain when it's created.

### Known Violatons

None so far

### Rationale

Make Iterator Helpers work for all built-in iterators. (initial discussion [tc39/ecma262#2045](https://github.com/tc39/ecma262/pull/2045#issuecomment-664992764))

TODO (likely there is more rational here

## Maintain the integrity of information contained in scopes currently offered by "use strict"
### Description : A long, full description of the invariant and it's purpose

Strict mode created a new invariant worth protecting. The erasure of arguments.caller sealed a leak of information from dynamic scope.

Strict mode also nixed the arguments property of functions. That eliminated both a reëntrancy hazard and leakage of dynamic scope.

```js
function foo() {
  console.log(bar.arguments);
  // [10]
}

function bar(a) {
  foo();
}

bar(10);
```

This invariant is important in evaluating future proposals for a stack trace API and any implicit context propagation proposals.

### Specification Details : Which specifications are affected by this invariant

Any specification impacted by "use strict"

### Known Violations

JS outside of "use strict"

### Rationale : Why did we have this invariant?

A program that involves parties Alice, Bob, Carol, and Dave, Alice should be able to pass an object from Dave to Bob by calling a function. Bob should then be able to call a function of Carol but should not be able to eavesdrop on the arguments Alice passed to Bob, and thereby gain access to the object from Dave.

# Known Landmines, "shoulds"

## Hazard of "Exotic" internal slots

### Definitions

**Exotic internal slots** Similar to Exotic and Ordinary Objects in the spec. Any slot that is not in the defined ordinary object,
any internal slot that is not in that set is for this document defined as an "Exotic internal Slot".

**Near Membrane**: Tries to make objects on the other side of the membrane seem to be regular objects within this realm. The near membrane is not perceived ... > This is different than the transparency Mark has been advocating for.  TODO: Caridy!

### Description

The exotic internal slots should carry only primitives, not objects.

### Significance

The built in methods that access the internal slots, (example: Date `date.prototype.getFullYear` - when a method is applied to a proxy of date, since the proxy does not have the internal slot, while the target may have it. The proxy purposefully does not access that. This is painful for near membranes. When a proxy is part of a near membrane, the new primative that was introduced detects that because it is part of the near membrane implementation and is able to emulate the behavior by applying the method to the target rather than the proxy. This is complicated, and every time you introduce an internal slot, you can only introduce near membrane transparency through monkeypatching. Using Symbol name properties you don't have such a problem. usually proxies don't break practical transparency but in this case they do for near membranes. Data should go through membranes without modification.

Danger: If the internal slot holds an Object -- any kind of punching through mechanism would have to be more deeply embedded in the membrane. You can't convey the capability itself because then you break membrane isolation. The reason this isn't fatal is that you already have to do a complex intervention to create near membranes. The cost is complexity, and potentially breaking separation by making a mistake with something that is already complicated.

### Specification Details

TODO

### Rationale

TODO

##  Exotic Internal Slots only access them on their `this` argument.

### Description
Methods or accessors that access Exotic Internal Slots only access them on their `this` argument.

Signficiance: this is the reason why normal membranes can be practically transparent

Definition: Membrane transparency - if you put them between realms, it acts like a realm boundary as though there isn't a membrane there. So you have all the costs of having a realm there, but by having a membrane there it isn't practically worse. Date is a good example here. date and time value .... Date methods access internal slots, but do it through the `this` argument.

!! TODO !! Get Image from Mark

Yellow | Blue

dateInstance.getFullYear()

date: proxy yellow date

This argument also goes through the membrane.

What is not transparent, even with this invariant,

Yellow | Blue

Date.prototype.getFullYear.call(dateInstance) // will work across realm boundary, but not membrane boundary

Broken by DataView.prototype.buffer.

### Specification Details

**!!TODO!!**

### Known Violations

There are a few, but those violations preserve practical transparency.
  * Array.isArray - checks whether the object is an array, but because it is checking a non-this argument, there is an exception to the proxy mechanism. If a builtin accesses it through a proxy, the proxy approximates its normal behavior.
  * Promise.resolve - when applied to a non-this argument determines if it is a promise. If you apply it to a proxy for a promise (which is not a promise), proxy.resolve returns the promise. proxy.resolve applied to a proxy for the promise does not return the proxy, but because the promise-proxy is thenable, it causes it to practically act as though it preserved the proxy promise behavior. Promise.resolve applies to a non-this, because it has the thenable assimilation behavior. This was a happy accident that it preserved practical transparency

### Rationale
**!!TODO!!**: Membrane practical transparency -- which we have for...

## Right of first access.


### Description: A long, full description of the invariant and it's purpose
If you attach handlers/get access to a value first, you have the "right of first access".

Promises not being synchronously inspected. Violated by both the web and node for inspection/debugging tooling. We have no prose stating this, this is a constraint on all future APIs, not current APIs. In the Promises A+ spec -- they took the stance that promises should mitigate Zalgo (reacting either eagerly and synchronously if resolved *or* waiting asynchronously if pending). Promises always react in the same order that you attach handlers. The concern was that otherwise the handler could perform operations before another handler was attached, you no longer have first in - first out ordering for handlers. This was seen as a way of preempting.

```js
a.then(foo)
// something between, a resolves in that time
a.then(bar)
```

No longer guaranteed that foo executes before bar since `a` could resolve before attaching bar and if bar executed synchronously it would execute before foo. This leads to conversations like: if you can obtain a builtin module and add a handler that mutates the module (E.G. to polyfill an export), then the handler will fire first before any other handler can reference the mutable state. It doesn't matter if the mutation is synchronous then because all access must occur after the mutation.

### Specification Details: Which specifications are affected by this invariant

TODO

### Rationale: Why did we have this invariant?
Enables polyfils

## Protecting Polyfillability

Term to be defined: New Language Semantics

### Description: A long, full description of the invariant and it's purpose
Why: Polyfills allow implementation as long as no new language semantics are required. Allows the use of future features in old code.
2000--2015: syntax didn’t change very much
Now, it's not as important due to focus on syntax in recent years.
The difficulty in passing APIs in the committee has resulted in passing more syntax.
Less viable now that committee uses syntax more than APIs. Syntax requires ahead of time tooling unlike polyfills.
See builtin modules.

Discussion over time: A JS Programmer doesn’t have as much control of the runtime in which their code runs in compared to other languages. We are tailoring towards older run times to have first class support from committee proposals. We are not designing just for the current language, but also for older versions of the specification. The version we design down to is the oldest browser version that is currently active. We go back to 2016 (ie 11) (some would argue for ie 7 or es3).

Design constraints from other environments: Node, other environments. Need delegation here. Example: Import.maps exist on the web and a similar capability is in node. Unclear if polyfilling can be done using them. We rely on solving language issues within the language, we don’t have a way to state that certain invariants are not mandated to be within scope of our work.

### Specification Details: Which specifications are affected by this invariant

TODO

### Rationale: Why did we have this invariant?

Enables supporting the specification up to a specific version (unclear which). The spec is backwards
compatible with itself.

# Precedents

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

----

Template

## Title
### Description : A long, full description of the invariant and it's purpose
### Specification Details : Which specifications are affected by this invariant
### Known Violations
### Rationale : Why did we have this invariant?
