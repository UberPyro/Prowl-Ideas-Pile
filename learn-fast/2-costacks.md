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

```
/* (=) is being specialized to ints here */
spec (=) : 0 int int -- 0 | 0

fun f => 4 = 4

val x = f "true" : "false"  /* x = "true" */
```

The expression `(4 = 4)` results in a costack with 2 choices, both of which are empty stacks. The first empty stack represents a result, and the second represents an error. This is acting just like a boolean -- if there were items on the first stack, it would be more like an option, and if there were options on the second stack, more like a result type. 

Now, the key to understanding costacks is *costack polymorphism*. Costacks have a top just like stacks have a top. When you have a function that doesn't explicitly deal with the costack (e.g. no `|` in it's type signature), really it's operating on the top of the costack. So, the `"true"` following the call to `f` pushes the value `"true"` to the *first* stack on the costack, *if* that's the one that exists (similar to a mapping on `Either a` in Haskell). The `:` operator represents a form of sumtype elimination. It checks if a costack is its top: if so, it ignores the following code and lowers the costack (eliminating the second stack), otherwise it eliminates the top stack and then executes `"false"` on the remaining costack. 

This should provide us enough material to define a factorial function for the first time: 
```
fun n fac => 
  : (n = 0) 1      /* initial / trailing colons are ignored */
  : n * (n-1) fac
```

Why even bother with such a system? The point of costacks is that (co)stack polymorphism behaves exactly the way you would want it to with either types as it does with product types; it has the properties you want for reasoning about code. The main result floats on top but errors can lurk underneath: this makes it easy tuck under and manage errors in a railway-oriented style. Multiple errors can be managed at a time, being pushed down and up and processed in stack order - this allows you to nest error handling in a way consistent with how scoping works, and gives you extra flexibility in refactoring as anything that's polymorphic over the rest of the costack can be dropped in and push and pop from it without needing to reason about what is underneath. Costacks are incredibly typesafe: `main` is expected to be a monochoiced function, so all pushed to the costack need to be manually handled at some point in time - there is no way an error could escape without explicit suppresion, yet the automatic mapping eases the programmer from having to constantly map or unwrap code. Lastly, costacks give us conciseness and *power*, as we can define costack combinators to catch common schemes and think about error handling more abstractly. 

todo: costack examples
/ conditional flow?
todo: costack types
todo: costack combinators
todo: costack polymorphism