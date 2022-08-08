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

## Costacks

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

The expression `(4 = 4)` results in a costack with 2 choices, both of which are empty stacks if you suppose `0` is empty. The first empty stack represents a result, and the second represents an error. This is acting just like a boolean -- if there were items on the first stack, it would be more like an option, and if there were options on the second stack, more like a result type. 

Now, the key to understanding costacks is *costack polymorphism*. Costacks have a top just like stacks have a top. When you have a function that doesn't explicitly deal with the costack (e.g. no `|` in it's type signature), really it's operating on the top of the costack. So, the `"true"` following the call to `f` pushes the value `"true"` to the *first* stack on the costack, *if* that's the one that exists (similar to a mapping on `Either a` in Haskell). The `:` operator represents a form of sumtype elimination. It checks if a costack is its top: if so, it ignores the following code and lowers the costack (eliminating the second stack), otherwise it eliminates the top stack and then executes `"false"` on the remaining costack. 

This should provide us enough material to define a factorial function for the first time: 
```
fun n fac => 
  : (n = 0) 1      /* initial / trailing colons are ignored */
  : n * (n-1) fac
```

Why even bother with such a system? The point of costacks is that (co)stack polymorphism behaves exactly the way you would want it to with either types as it does with product types; it has the properties you want for reasoning about code. The main result floats on top but errors can lurk underneath: this makes it easy tuck under and manage errors in a railway-oriented style. Multiple errors can be managed at a time, being pushed down and up and processed in stack order - this allows you to nest error handling in a way consistent with how scoping works, and gives you extra flexibility in refactoring as anything that's polymorphic over the rest of the costack can be dropped in and push and pop from it without needing to reason about what is underneath. Costacks are incredibly typesafe: `main` is expected to be a monochoiced function, so all pushed to the costack need to be manually handled at some point in time - there is no way an error could escape without explicit suppresion, yet the automatic mapping eases the programmer from having to constantly map or unwrap code. Lastly, costacks give us conciseness and *power*, as we can define costack combinators to catch common schemes and think about error handling more abstractly. 

In addition to using `:` for handling (which is considered syntax and not an operator), there is also a more fundamental `|` operator, which is associative but chains error-first which is not ergonomic in many scenarios. `|` takes two functions as arguments, their inputs can expect different typestacks but their outputs must match. `|` checks the top and second element of the costack: if the top matches, it runs the right function on it, and if the other matches, it runs the left function, either way eliminating a level of the costack. `|` is also *costack polymorphic*: *both* the first and second elements of the costack can fail if there's more to the costack, in which case the `|` still lowers the costack but runs nothing. 
```
fun n fac => (n = 0) (n * (n-1) fac | 1)

val x = (0 = 1) (0 = 1) ("b" | "a") ("d" | "c")  /* x = "d" */
```

## Costack Types
Type costacks are very similar to type stacks. Recall that in Prowl, every function is a costack of stacks. 
```
spec (=) : 0 int int -- 0 | 0  /* removes two integers, then produces 
                                  2 possibilities of the stack with those 
                                  integers removed.  */
spec (int = int) : 0 -- 0 | 0  /* equivalent */
spec (int = int) : !1 | 0 -- !1 | 0 | 0  /* with explicit costack !1 */

           /*  v-- `|` is the operator for sumtype elimination */
spec ((0 -- 1) | (2 -- 1)) : 0 | 2 -- 1
```

`@` can denote a costack bottom, or an empty costack, e.g. `@ | !2 | !1...`

## Costack Combinators
Note that costack combinators are in a sense *linear*. Elements cannot be arbitrarily created or destroyed because it would break the invariant that costacks have exactly 1 match (e.g. purely a sumtype). 
```
spec pump : 0 -- 0 | 0  /* If top matches, it's propagated to the new top */
spec press : 0 -- 0 | 0  /* If top matches, it's propagated to second  */
spec leak : 0 | 0 -- 0  /* If either matches, it's propagated */
spec flip : 1 | 0 -- 0 | 1  /* swap analogue */
spec asc : 2 | 1 | 0 -- 1 | 0 | 2  /* dig analogue */
spec desc : 2 | 1 | 0 -- 0 | 2 | 1  /* bury analogue */
spec pick : 0 | 1 {0 -- 2} {1 -- 2} -- 2  /* `(|)` */
spec try : 0 {0 -- 1 | 0} -- 0  /* postfix ? quantifier, becomes id on 1 */
```
There are definitely many more complex, interesting costack combinators out there, but finding them doesn't appear to be trivial as more complex things do not seem to directly translate over. Getting to the bottom of these will be fun. Ultimately what we will achieve is a full combinator scheme that abstracts over the common patterns in control flow. 

## Quantification
The idea of quantifiers comes from regex, which Prowl borrows from. The conditional quantifiers overlap with the costack quantifiers from before, but include some more general ideas like *recursion schemes*. All quantifiers shown here only operate on endomorphisms: functions whose inputs and outputs are the same type. 

The first quantifier to cover is *iteration*. Iteration repeats a function for the provided number of times. 

```
val x = 2 (+ 1)#4  /* x = 6, `#` has higher precedence than concatenation. */
```
`(+ 1)` is composed with itself `4` times, then concatenated with `2`. 
```
spec dup : X -- X X  /* some reminders */
spec dig : X Y Z -- Y Z X
spec nip : X Y -- Y

val x = 0 1 (dup dig (+))#4 nip  /* x = 5 = fib(6) */
```
Note that every iteration of `dup dig (+)` computes successive Fibonacci numbers. 

Now let's look at conditional quantifiers. These are PEG-style by default in Prowl; the quantifiers are possessive, meaning that they follow a "longest match" rule as opposed to creating a set of all possible matches. This makes them deterministic and a little easier to reason about. 

```
/* functions are expected to have a type like the following: */
type cquantable = 0 -- 1 | 0

/*
  ? 0-1 times
  + 1-inf times
  * 0-inf times
*/

val x = 2 (as n; (n < 5) (n + 1))*  /* x = 5 */
val x = 3 ((0 = 0) (+ 1))?  /* x = 4 */
val x = 3 ((0 = 1) (+ 1))?  /* x = 3 */
val x = 3 ((0 = 1) (+ 1))+  /* x = 4 */
val x = 3 ((0 = 1) (+ 1))*  /* x = 3 */
```

If you wanted to clean up some of those examples, note that `(0 = 0)` is equal to `pump` and `(0 = 1)` is equal to `press` from the combinator list. 

## Basic Patterns

Binding expressions like `as` expressions can include pattern expressions beyond just identifiers in order to destructure the input. Sometimes different structures have many alternate forms, in which case *pattern matching* can be implemented to select the correct case. In Prowl, pattern matching is simply built into the costack system. 

```
0 as 0; ..  /* matches, so true. */
0 as 1; ..  /* doesn't match, so false. */
```

```
fun n fac => 
  : as 0; 1    /* pushes to costack, handled by `:`. */
  : as n; n * (n-1) fac  /* complete, does not push. */
```
