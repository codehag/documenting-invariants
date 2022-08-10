# Documenting Invariants and Design Principles

Part of the work that we do at TC39 is ensuring that as the language changes, certain properties of the language remain. We have been referring to them as invariants. Some of them are [written down](https://tc39.es/ecma262/#sec-invariants-of-the-essential-internal-methods), but not all of them. The work around maintaining unwritten invariants usually falls onto a single member or delegate, whose absence may result in certain decisions being taken that then need to be adjusted.

Additionally, during the search for invariants another issue was brought up -- that of properties of
the specification that should be true going into the future. These have been temporarily termed
"design principles" with the name pending discussion.

The goal of this repository is to define a format in which invariants can be introduced into the specification. So far, we have discussed the following as important features of an invariant. For Design Principles, there is still ongoing discussion of their exact form, but a rough sketch is provided. 

## Features of an invariant

* Specifies a rationale -- Invariants [can be abandoned](./abandoned_invariants.md). In order to be able to track why an invariant exists, and make a measured decision on its continued existance, we need to know why it is there. By specifying the rationale and keeping it up to date, we can identify if an invariant is still important or not.
* Uses _must_ langauge if the invariant applies in all cases.
* Uses _should_ if the invariant applies in most cases, but there are clear cases where the invariant does not apply. This may be an implementation limitation, or something similar. In this case, the reasons should be listed and kept up to date.
* has an associated Test262 integration test.

Known invariants are collected [here](./known_invariants.md).

**Intended Representation of an Invariant**

* Is normative text. If the specification is out of step with an invariant, the spec is considered
    buggy. See [invariants of the essential internal methods](https://tc39.es/ecma262/#sec-invariants-of-the-essential-internal-methods) for an exampl.

## Features of a Design Principle

* Specifies a rationale. Much like invariants, design principles can be abandoned. Unlike
    invariants, a design principle has not always been true. An example of a design principle is
    that new Array methods should treat holes as undefined rather than skipping them.
* has an associated Test262 integration test.

Known Design Principles are collected [here](./design_principles.md).

## Process

Please open an issue or a pull request if you see something missing here.

The process for adding an invariant to the specification has yet to be defined. We have a few options, the most obvious one is using the same proposal process as what we have for new features. Alternatively, there is a case for a lighter weight process like what we do for normative specification adjustments. Comments and ideas on this are welcome.
