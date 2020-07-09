# Known Invariants, "musts"

The purpose of this document is to collect invariants that we know.

# Known Landmines, "shoulds"

## Hazard of "Exotic" internal slots

### Category
"Should"

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

----

Template

## Title
### Description : A long, full description of the invariant and it's purpose
### Specification Details : Which specifications are affected by this invariant
### Rationale : Why did we have this invariant?
