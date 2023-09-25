# Costacks in Prowl
Costacks are one of my favorite ideas and something I enjoy talking about in language design spaces. They are very interesting to consider in the context of concatenative programming (languages like Forth and Factor) as they offer powerful interpretations in terms of error handling, case analysis, and control flow. They are also a nice example of a datastructure other than stacks on which concatenative programming can be done. However, due to the neglect of sumtypes in traditional programming languages, costacks are often a very hard concept for me to communicate and for others to understand. In fact, they might be the most troublesome concept in Prowl, a deeply nontrivial language. Often it's Haskellers that have the easiest time understanding them out of the gate due to the support from a theoretical background. 

As costacks are dual to stacks, let's discuss stacks first to gain some intuition. In a stack language, we might represent a stack as a list of values, for example: 
```
2 4 6 8
```
This is a concise syntax, but what does it really mean? Stacks can be thought of as a chain of pairs: 
```
((((bottom, 2), 4), 6), 8)
```
Stacks can be built up and built down. Building a stack up means taking the previous stack and pairing it with a value. Building it down means extracting that value and pulling out the rest of the stack. You can think of stacks a lot like linked lists, but since they are linear no pointers are needed. In ADT syntax this is like: 
```
data Stack = 
    Push (Stack, Value)
  | Bottom
```
Or as an algebraic specification: 
```
push   : (Stack, Value) -> Stack
bottom : Stack
```
Again, stacks rely on *inductively chaining pairs*. The "inductive" part provides a ladder which is convenient for concatenative programming and the "pairs" part makes this a giant product, which is useful for juggling the inputs and outputs of functions. Stacks are some chain like *This AND this AND this AND nothing else*. 

What if instead of chained *pairs*, we chained *possibilities*, one of which is true. If you are familiar with Rust you may know of a result type, or if you are familiar with Haskell an either type. A type in which (only) one of two possibilities are true. 
```
data Result b a = 
    Ok a
  | Err b
```
What if we chained results together into an inductive structure? 
```
data Costack = 
    Real Elem
  | Fake Costack
```
Or as an algebraic specification: 
```
real : Elem -> Costack
fake : Costack -> Costack
```
A costack is a chain saying *This OR this OR this* is true. Exactly one thing. The interpretation in terms of error handling is that there is one success state and any number of failure stats underneath. But you can interpret this as some number of cases in any context. 

Where a stack keeps a collection of values in some order, a costack only ever has a *single value*. Exactly like result types, costacks *as a type* syntactically holds *both* types a term can hold, but *as a term* is only one of those types, with a tag. In fact, you can mathematically simplify the costack definition down to just an integer datting some term: 
```
data Costack = (Int, Elem)
```
Because a costack is only some element with some number of `Fake`s wrapped around in. This makes costacks very interesting -- they can be quite busy at the type level, but at the end of the day can boil down to a single integer when executed efficiently. For a concatenative costack language, that's *one tag* for all costack-encoded sums in the *entire program*. The integer ends up representing the state for all possible cases in the program while still being kept relatively small: it's helped by the enforced stack order and by how its interpretation is fluid with respect to the execution of any costack functions -- I may provide more info as to what exactly is going on here later. 
## A Costack Calculus
Prowl is a costack-of-stacks concatenative language. This means costacks hold stacks and stacks hold values. Prowl is higher-order like Factor, so values can include quotes which hold a pair of costacks. At the type level, costack variables are `+` terminated like `c+` and stack variables are `*` terminated like `s*`. Quotes are represented with `[]` and `--` represents a function. Costacks have stack types pushed on with `|` and stacks have values pushed on with space/juxtaposition. Since Prowl is a stack language, all words (identifiers) are functions, functions must have a costack as input and output, and quotes contain functions. 

Some fundamental costack combinators are the following: 
```
gen : c+ | r* -- c+ | s* | r*
fab : c+ | r* -- c+ | r* | s*
elim : c+ | r* | r* -- c+ | r*
exch : c+ | r* | s* -- c+ | s* | r*
```
Again, the `|`s represent the stacks on top of the costacks. Costacks are manipulating stack variables since they hold stacks in this language. 
- `gen` is a function which does nothing at the value level, but propagates the costack realness up a level to represent a positive case. If the top of the costack is fake, then it stays fake. 
- `fab` wraps a value with `Fake` and grows the costack one level. 
- `elim` says that if the two possibilities on top of a costack have the same type, then they can be merged to the same (because matching a stack out of either case yields the same type of thing). 
- `exch` is a costack swap. 
## Polymorphism
Stacks are fundamentally useful as an abstraction because they possess a kind of *invariance*, something that does not change. For starters, consider these stack functions: 
```
(+) : r* int int -- r* int
succ : r* int -- r* int
```
This notation should be similar to the stack effect notation of Forth and Factor, but an important different is that *stack variables* are made explicit. Stack variables can be elided in first-order stack languages like Forth, but become much more important for the reasoning of higher-order stack languages (having quotes like Factor and Joy). 

Regardless of whether they are written or not, they really are there. In these examples, the stack variables are representing the fact that *the rest of the stack does not change* during the execution of the function. Fundamentally, functions over stacks build abstractions because *the rest of the stack* becomes invariant with respect to it's execution, and we can reason merely about the effect on *the top of the stack* in many different contexts. Additionally, the effect of the function in some sense does not depend on the stack remainders. 

To fully appreciate the power of costacks, we have to be able to interpret *costack polymorphism*; what it means for a function to be invariant over "the rest of the costack". Simply put, all costack functions consider some number of cases to some finite depth, and leave the remaining cases untouched. Consequently, if considered variables are all fake, the costack variable *must be real* (as it is the sum of all remaining possibilities), which means *the state of data is untouched* in the "rest of the costack" case. In other words, an intuitive interpretation of costack polymorphism is the propagation of an error state (in this case, a totally unanalyzed state) until the costack is deconstructed enough by functions for the real state to be matched out. 
## Comparison and Control Flow
Costacks as a main feature does add a significant amount of complexity, but not without benefit: they allow an alternative to control flow that feels more concatenative than what is used in traditional stack languages, and they allow for the elimination of booleans in the language, taking advantage of a more expressive approach. 

Predicates in a stack language can split the stack, and propagate realness to a higher or lower level depending on the provided input: 
```
(<) : c+ | r* int int -- c+ | r* | r*
cmp : c+ | r* int int -- c+ | r* | r* | r*
```
`(<)` is a less-than operator which consumes two elements off the stack and then propagates the stack appropriately. `cmp` is three-way comparison, delivering the stack to the greater-than, equal to, or less-than position in top-down order. 

We can also add the costack variables onto our stack functions from before: 
```
(+) : c+ | r* int int -- c+ | r* int
succ : c+ | r* int -- c+ | r* int
```
The costack polymorphic cases of purely stack functions ends up being instrumental. These functions have their effect skipped on fake costacks, which means the costacks generated from comparisons can directly control the effects of these functions. 

A program such as `0 1 (<)` has an effect identical to that of `gen`, and `1 0 (<)` has the effect of `fab`. The program `0 gen succ elim` produces the value `1`, whereas `0 fab succ elim` produces `0` as `succ` is skipped. Recall that `elim` collapses the costack when the two top stack types are the same. Back to the chained result types analogy, you may notice that this is a lot like `map` on a result type -- the function is applied in the positive case and skipped otherwise. 
## A "Real" Program
```
= fib
  dup 1 (>)
    pred dup pred fib swap fib (+)
  elim
```
`pred` is performing `1 (-)`. Prowl has some additional dataflow operators that are more expressive, but I've omitted them here since this is a simple example. The reason for parenthesization around operators is because Prowl supports infix and has Haskell-like sectioning to regain RPN-like behavior. 
## Dataflow
Factor has a number of dataflow combinators to make the language more expressive. These are called the cleaving, spreading, and applying operators. It turns out that these operators are very product specific and closely related to arrows and cartesian categories, which means they can all be dualized to sums. Prowl's dataflow operators work differently from Factor, but the basic idea is the same. Two costack functions can be executed in parallel by stacking one on top of the other, basically a function with a type like `b+ | t* [c+ -- d+] [e+ | r* -- e+ | s*] -- b+ | t* [c+ | r* -- d+ | s*]` (this is related to `+++` in Haskell, spreading in Factor). Additionally, we can `elim` afterwards and get a function like `b+ | t* [c+ -- d+] [e+ | r* -- d+] -- b+ | t* [c+ | r* -- d+]` (`|||` in Haskell, cleave in Factor). These can be generalized a little bit, for technical reasons the types you may actually have/want may be more sophisticated than this. 
## Colambdas
Practical stack programming usually includes a way to bind values to lexically scoped variables. We can do something similar and bind stacks to values, though the way this works is of course a little different. 

Binding in a stack language may look something like this: 
`\x y -> x sqr y sqr (+) sqrt`
Let's represent a colambda with `\~`. Colambdas bind terms on the right. 
- `R R -> R\~` is `elim`, binding two stacks of the same type to `R` off the costack and then pushing `R` to the costack. 
- `R -> S R\~` is `gen`. Remember that variables are bound on the right. 
- `R -> R S\~` is `fab`. 
## Logic Programming
Prowl is nondeterminstic and reversible, which makes it a Curry-esque logic language. The "functions" we have been talking about previously are really binary relations in the context of Prowl. It turns out that in the category of relations, disjoint unions are far more important than cartesian products, as disjoint union functions as both the product and coproduct in this category. `Rel` is a dagger category, meaning that the product and coproduct operator much coincide. In other words, the reverse execution of the `+++`-like dataflow operator is itself, and in a nondeterministic language where there are *sets* of results rather than one result, sets of tagged results can build up product-like data. A set like `{(0, "x"), (1, "y")}` using numbers to represent costack levels ends up looking like a product `("x", "y")`, and the execution of the `+++`-like dataflow operator will perform like a sum or like a product essentially depending on what inputs are provided to it. 
