# Costacks & Deterministic Flow
Up until this point, Prowl has mostly been like a stack language with some ML sugar on top. Costacks are the first of the conceptually challenging novelties that Prowl has to offer. Even so, with good motivations and examples, I believe them to be accessible to programmers at large. 

One way to imagine stacks are as long chains of pairs. Pairs are simply tuples of size 2. 

In other languages, you might see something that looks like this: 
```
(((unit, c), b), a)
```
Pairs can chain so that the right element represents the "top" and the left element represents the "rest". This gives you the ability to encode something that is similar to a list at the type level (but much more limited). This concept is nearly the same as a stack: 
```
[c b a]
```
The value representation is exactly the same, but there are differences at the type level. (Type)stacks are potentially much more general than tuples at the type level, which makes them like really nice tuples. In particular, stacks can manage stack polymorphism in cases where tuples cannot, and stack polymorphism is in line with our intuitions about scoping and the locality of concepts in programming. 

Now, Prowl tries to be an innovative language. Rather than forming a stack out of nested pairs, what if we used nested result types (or either types, if you're familiar with Haskell)? 

```
/* Rather than */
unit C pair B pair A pair...
/* What if we had */
unit C result B result A result...
```

This is what I call a *costack*, because sums are the categorical dual to products. Costacks are long chains of result types that can *either* be "this value right here" *or* the rest of the costack. Whereas stacks hold 0 to infinity values, costacks are linear: they always hold *one* value. It's simply the case that this value can be a lot of different things, and working your way down the costack can sus out what it is. 

I sometimes use the name "path" instead of "costack" as I think that is a friendlier name, though that may make it harder for some to remember what it is. 

In Prowl, all functions are actually functions of costacks of stacks. This provides a means of error handling that's baked right into the language. Let's see what this looks like: 

todo: costack examples
/ conditional flow?
todo: costack types
todo: costack combinators
todo: costack polymorphism