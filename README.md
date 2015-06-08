% PrtScrn - Language specification

About
====

This document tries to describe PrtScrn programs lexically, gramatically and semantically.
I try to be clear and precise, without getting too formal.
Since this is probably the only documentation for the foreseeable future, it also includes examples,
hints, opinions, conventions and other things that aren't strictly part of a language specification document.

PrtScrn is still under development. Everything here is subject to change.
A lot of things aren't implemented yet. Some things are implemented, but not documented.


PrtScrn in a nutshell
================

PrtScrn intends to be a light-weight, general-purpose programming language.
Here are some of its properties you're likely to know from existing languages:

* Newlines separate statements. Curly braces are mandatory.
* Block scoping.
* Values have types, variables don't. Values are not implicitely coerced.
* Functions are first-class values and can have free variables (lexical closure).
* Object-oriented programming with multiple inheritance. Classes are first-class values too, and support lexical closure.
* Memory is garbage-collected.
* Concurrency is achieved through fibers.
* It compiles to bytecode at runtime. This bytecode is (to some small degree) self-optimizing.

\

Of course there's more to PrtScrn than that. Here are some of the twists that really set PrtScrn apart:

\

**Typing**

PrtScrn's types are more like mathematical sets than the type systems you are probably familiar with.
Think Venn diagrams rather than family trees.
It has the usual types like `Integer` or `String`, but also `Positive` or `Empty`.

There are a lot of types in the standard library, but you can create your own as well.
Types are first-class values and can be combined and created ad hoc:

```
let $Real = Integer | Float
let $NonEmptyString = String + not Empty
let $HttpUrl = String.starting_with( "http://" )
let $CustomType = function( $value ) { return <boolean expression> }
```

\

**Object capabilities**

PrtScrn follows an object-capability model.
Capabilities are functions or objects that can do things with side-effects (e.g. read a file or print to stdout).
All capabilities are given to the program entry point (i.e. `main`), and it is then responsible for passing them to other parts of the program.
This is basically nothing more than language-enforced dependency injection.

* You can, at any time, safely import a library or load a script. It can only have side-effects if and when you give it the required capabilities.
* An untrusted PrtScrn program can be run with reduced capabilities, or by lazily prompting them from the user.
* You can safely embed PrtScrn without cutting off access to third party libraries by simply giving it reduced capabilities.
* PrtScrn programs are probably more likely to be reusable and testable.



Lexical structure
=================

## Comments

PrtScrn supports block and line comments.

<div class="side_by_side"><div>

Block comments start with a `/` followed by one or more `*`.
They end with the exact same amount of stars followed by a `/`, or at the end of the file.

</div><div>

```
/* One block comment */

/** Another **/

/*	I don't end here: **/
	I end here: */

/*
	I end at the end of the file...
```

</div></div>

<div class="side_by_side"><div>

Block comments can't actually be nested.
One block comment can, incidentally, contain another if it has a different amount of stars.

</div><div>

```
/**
	Block comment containing
	/* Another block comment */
**/
```

</div></div>

<div class="side_by_side"><div>

Line comments start with `//` and end at the first newline.

</div><div>

```
// Line comment
print "Hello" // Be polite!
```

</div></div>

<div class="note">
There are no doc comments or attributes at this time.
A generalized annotation syntax is in the works.
</div>

## Whitespace

Spaces and tabs are not significant, besides separating tokens.

## Newlines

Newlines *are* significant. They separate statements:

```
statement
if expression {
	statement
	statement
}
```

This makes PrtScrn code easy to read and write.
Sometimes you might want to use newlines to break long expressions and constructs, so they are ignored...

* After binary operators.
* Within parenthesized expressions.
* At the start or end of blocks.
* After keywords, until the end of the expression or construct, but not within their blocks.

## Symbols

```grammar
{ } ( ) [ ]
, -> .
< > == != <= >=
+ - * / % |
+= -= *= /= %=
```

## Keywords

Keywords have special meaning and can't be used where an identifier is expected.

```grammar
and
catch class
else extends
false finally for function
if in is
let
new not nothing
or
return
this throw true try
while
```
