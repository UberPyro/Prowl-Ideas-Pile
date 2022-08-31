# Why Does Prowl Matter
Prowl is a typed, higher-order [concatenative](http://evincarofautumn.blogspot.com/2012/02/why-concatenative-programming-matters.html) [relational](https://matt.might.net/articles/microkanren/) language. If you are unfamiliar with those paradigms, I would recommend checking them out first: nevertheless, I will do my best to briefly motivate everything from scratch. 

Programs in Prowl are made via the juxtopositions of words, like in stack languages. Furthermore, instructions can be quoted into values, just like in Joy and Factor. However, rather than functions, words correspond to *relations*, and rather than function composition, juxtaposition represents *relation composition*. 

## Relations

Relations are like functions, but they return a set of elements instead of a single element. We are all familiar with the differences between functions and relations from high school math, as relations are ubiquitous in algebra. 

- `y = x^2` is a function because for any `x`, there is only 1 `y`. It passes the vertical line test! 
- `y^2 = x` is not a function with respect to `x`, because there can be a set of results. `y^2 = x` can be rewritten to the disjunction `y = sqrt(x) OR y = -sqrt(x)`, so for `x = 4`, the solution set is `{2, -2}`. 

In Algebra, we are often taught the "vertical line test" to check if something is a function or not, as two ouputs for a given input would both intersect with a vertical line. Similarly, there is a "horizontal line test" to see if the *inverse* is a function - this is because the inverse of a plot is a reflection over the `y = x` line and so a horizontal line in a plot corresponds to a vertical line in the inverse plot. 

What is interesting about those exercises is that we were viewing functions through the lens of relations, e.g. functions are just a subset of relations. While an inverse function may or may not exist, the converse relation *always* exists - you can always mirror the plot over `y = x` - and it is through the more intuitive converse relation that we reason about inverse functions. 

The big idea here is to say: if relations have the nicer algebraic properties, then maybe we should center our analysis around *them* rather than functions. Not only are they closed around the converse operator, but relations are sets - they can be unioned and interested by encoding them as sets of pairs, where each pair is `(input value, output value)`. So if you imagine the domain and codomain each as a bunch of nodes, relations are just the set of edges from input to output. 

## Relation Algebra

Functions and relations both support algebras with *fanning operations*. If you are familiar with arrows in Haskell or combinatory logic, you should be familiar with some of the ideas here. The purpose is not necessarily to write programs in terms of them (though you can certainly do that), but to use them simply as a tool to understand how things work on an algebraic, mathematical level. 

Functions can form both sums and products using the appropriate combinators. The two most important are: 

- `(|||) : (a -> c) -> (b -> c) -> Either a b -> c` is known as a "fan in". It says "If I have a function that turns an `a` into a `c` and another function that turns a `b` into a `c`, I can make a new function that turns either an `a` or a `b` into a `c`". 
- `(&&&) : (a -> b) -> (a -> c) -> a -> Pair b c` is known as a "fan out". It says "If I hav a function that turns an `a` into a `b` and another function that turns an `a` into a `c`, I can make a new function that turns an `a` into both a `b` or a `c`". 
- `(>>>) : (a -> b) -> (b -> c) -> (a -> c)` It's also not a bad idea to remind ourselves of function composition. 

As an example, if I had two functions `show_int` and `show_float` (which cast to strings), I could create a new function `show_int ||| show_float` that takes in the sum type and then produces a string. Alternatively, if I had the functions `parse_int` and `parse_float` producing their respective data types, I could make a function `parse_int &&& parse_float` which accepts a string and tries to generate both. 

A key point here is that we need some kind of product or sum type in the functions that we create, as combining the functions pushes information into data, the inputs and outputs. 

Now, the corresponding sums and products for relations work differently. The representation for sums and products actually coincide due to the self-duality of relations - the fact that they are closed under converse. 

Let's start with what relation composition looks like, which we will call `>>`. `~` is the binder / arrow for relations. 
- `(>>) : (a ~ b) ~ (b ~ c) ~ (a ~ c)`. Relations map an element to a set of elements. Composition relations mean we apply each output of the first relation to the second relation, and then union all of the outputs together - it's a bind over the powerset monad. 

Now let's simply use `$` to represent fan in and `@` to represent fan out. 

- `($) : (a ~ c) ~ (b ~ c) ~ Either a b ~ c`. Fan in says "if I have a relation between `a` and `b`, and another relation between `b` and `c`, I can create a new relation between `a` or `b` and `c`". The input can consist of any number of unique `Left`s and `Right`s - each side must have unique elements with respect to itself, e.g. `Left 5` and `Left 5` union away to just `Left 5`, though `Left 5` and `Right 5` are tagged and therefore different. The output then is attained by applying the left function to all of the lefts and the right function to all of the rights, and then unioning all of the results together. 
- `(@) : (a ~ b) ~ (a ~ c) ~ a ~ Either b c`. Fan out says "if I have a relation between `a` and `b`, and another relation between `a` and `c`, I can produce a new relation between `a` and `b` *or* `c`". Notice that fan out is now producing a sum! This is because products can be represented as unions of sums: rather than having `Pair a b`, we can simply union `Left a` with `Right b`. This function then takes our two input relations, applies both of them to the same output, then tags them and unions the results, and both results are provided in the output. The size of the output set is exactly the sum of the size of the output sets for the left and right functions, as each one is controlling the number of `Left`s and `Right`s produced, and those `Left`s and `Right`s are discriminating their contents from each other. 

This is our first glimpse into the awesome property of self-duality - finding the converse relation finds the categorical dual of that relation - a relation `a ~ b` has a converse computing the opposite `b ~ a`, or in other words, backwards execution is the same as dualization by the Curry-Howard Isomorphism. Running the sum of relations backwards provides their product, which is why product must be able to be expressed only in terms of sums of data. 

## Concatenative Programming
We've taken a long enough time to show how relation algebra doesn't need product types in its type system due to them coinciding with sum types. This was a really long way to motivate the fact that we can make a concatenative language that can represent both sums and products with a single data structure. 

Normally, concatentive programs are *stack-based*. This means that all functions accept and return a stack, and that modifying the stack is how data is manipulated and passed around. The juxtaposition of words signifies function composition, which creates a terse way to describe computation. 

As an example, in reverse polish notation `7 8 9 + 1 - swap -` evaluates to `9`. This is made easier by imagining a stack: `7 8 9` goes on top of the stack, the `+` pops the `8` and `9` and pushes a `17`, the `1 -` turns that into a `16`. The swap instruction then switches the `16` and `7`, and `16 7 -` produces `9`. 

A key idea is that of *stack polymorphism*. Operators like `+`, `-`, `swap`, and even `7` all do something specific while ignoring the rest of the stack underneath what they do. `(+)` has some type like `int int -- int`, but underneath those `int`s are stacks, which can be populated in any way. 

Concatenative programming has a lot of benefits, mostly that it enables a point-free style that is more expressive and more intuitive to reason about than the typical FP combinators (e.g. flip, compose, and its variations in languages such as Haskell and F#). Being able to write more of a program in tacit style is good, as it reduces plumbing due to variable bindings, reduces the number of things that have to be named, reduces the number of ways something can be done, allows functions to be described more directly and with less noise, and lets programming be more abstract by factoring out common patterns. Concatenative programming in general also permits highly factored and factorable code in general due to how easy it is for functions to be split. Writing with greater abstractions in general will help make code less buggy, as with more abstract things, there are less actions available and therefore fewer wrong paths to take. 

Traditional concatenative programming uses a stack. One way to think of stacks is like tuples with nicer properties - they glide right between tuples and heterogenous lists, and are right on the edge of supporting type inference, which means they have just the right amount of power to be an extraordinary data type. The support for stack polymorphic functions specifically is what gives them their advantage over tuples. 

Stacks can be encoded naively in terms of tuples, which is actually a very useful way to understand what they are. `((((), 1), 2), 3)` is one way to represent the stack `1 2 3`. Or in plain english, the stack is `3 AND 2 AND 1 AND nothing else!`

Since stacks represent products, they provide us a way to implement fan out from before, using `--` to represent a *stack effect* like in Factor. 
- `A [A -- B] [A -- C] -- B C`. This is fan out using the stack as a better tuple. It takes in a stack with a value of type `A`, as well as two (quoted) functions that turn an `A` to `B` and an `A` to `C`. The function executes and then leaves the `B` and `C` on the stack. 

We would also like a nicer way to represent fanning in. But stacks are only suitable for expressing products! This isn't that surprising, as programming historically has seemed to rely far more on products. Languages often have special syntax for tuples and records / structs, but tagged unions are not offered the same treatment, and `Either` types aren't commonly seen outside heavy FP languages such as Haskell (and the space of languages similar to it). 

However, fanning in is very useful, its something that a modern, well designed language might want to support. What are our options? One thing we need to explore is the idea of a *costack*, the categorical dual of a stack, that give us a list of options for us to choose from. 

Just as stacks can be thought of in terms of pairs, costacks can be thought of in terms of result or either types. A stack might be `this AND this AND this AND an empty stack`, but a costack looks like `this OR this OR this OR no other possibility`. You can also think of the top element like "success": `success OR error1 OR error2 OR error3...`. Costacks have combinators to reorganize and manipulate them just like stacks do (in fact, every stack operator has a dual costack operator, though the correspondences might be quite unintuitive). Furthermore, costacks support stack polymorphism just as stacks do, which gives them an advantage over either types. We can imagine what a dual stack language would look like - a language where every function is a function between costacks, where the costack is able to track all of the error cases of functions and all of the possibilities for data, using tuples to pass around grouped arguments and return values. Such a language would certainly have very pleasant primitives and abstractions over error handling and sum type manipulation more generally, but we lose the crucial product type manipulation of stack languages. 

Clearly there is a motivation to develop a language with both features. We could have a language that has both costacks and stacks - for example, we could nest stacks inside of costacks, and run around with costacks *of* stacks, but this makes the type system a mess as types become long, beginner-hostile, and short on good syntax. At this point, hopefully it's obvious how the ideas we have covered can connect. 


## Relations of Costacks
We know from ealier that products and sums coincide in relation algebra. So our above problem becomes a non-problem when we move to that paradigm. Now we can have the benefits from both relational programming and concatenative programming, but with maintaining a kind of minimalism - the total is *less* than the sum of its parts - as a cheeky way of putting it. Relations between costacks allows us to use costacks as both ways to represent sums and products - the converse of such a relation always uses them in the opposite way. 

## A Slight Problem (and its solution)
While sums and products coincide, relation products can be unintuitive at first compared to function products. They make different assumptions about their data which might change the way you have to write certain functions. 

For example, consider an addition function like so. `~` is used as the binder for relations, and juxtaposition separates items on the costack. 
- `(+) : int int ~ int`
This function accepts *either* a left `int` *or* a right `int`. The previous relation can output a left in union with a right, but relation composition will apply this relation to each of the relation's outputs individually, and then union the results together. This is no good because you need *both* `int`s at the same time to add them together. The solution is to permit curried relations. 

The true type of the relation is
- `(+) : int ~ int ~ int`
which is a (binary) relation which relates a costack to another binary relation. 

The reason why this works is because the outer function consumes an int and then produces a *set* of relations. That set of relations is then applied to the second operand. We have access to both `int`s now as the second operand is fed through the relation created through the first operand. In the case of multiple top and second-to-top operands, a cartesian product pattern of applications would naturally occur. 

## Regex and Program Combinators
The final critical idea of Prowl is that programs are not merely written in a concatenative style - there is a whole host of *program combinators*, of which concatenation is one, which together with the primitive relations, can be used as the basis to form more complicated programs. This way certain kinds of key functionality that has to be packed away into function composition in a purely concatenative language can be expressed more naturally, and programs can be built intuitively similar to how parser combinators can construct parsers. 

At the start of this document, I mentioned that the original motivation for Prowl was to explore the connection between string concatenation theory (really regex and Kleene Algebras) and concatenative programming. It may seem that it's taken a long time to circle back here, but the truth is that relational programming *is* that detour. 

Concatenation theory tells us that string concatenation and string alternation, where alternation forms unions between sets of strings, forms a semiring. So something like `a (a | b) b | a b b` would form the set of strings that's just `a b b`, since the second set is a singleton subset of the first. The isomorphism with concatenative programming is direct on the syntactic level, but different on the semantic level, as different functions can have values that coincide. In math terms, concatenative programs have a syntactic monoid (string concatenation) and a semantic monoid (composition), with a homomorphism from the concatenative monoid onto the semantic monoid (many different lists of words correspond to functions which do the same thing; `pred succ succ` and `succ` both map onto the same semantic program for instance). 

The ability to treat concatenative programs as strings or as lists gives us an enormous amount of power in terms of metaprogramming, as we can through the entirety of string or list processing at instruction macros. However, to reign in the scope of both this project and this document, let's specifically continue to focus on combinators over the semantic monoid, as this is typeable and the end goal that matters. 

We can import some regex combinators into Prowl as both the syntactic and semantic monoids are Kleene Algebras, and we can lift operators from the former into the latter. 

- ` `, juxtaposition, standing for the concatenation of strings, is used as Prowl's operator for relation composition. 
- `|`, standing for the alternation of strings, is used as Prowl's union operator between relations. 
- `*`, postfix Kleene star, can be used to unfold a function composing with itself, and union all of the intermediates. `f*` encodes to `id | f | f f | f f f | ...` where `id` is the identity relation. 
- `+` is the same as above without the `id` case. 
- `?` desugars `f?` to `id | f`. 

We also deviate from regex combinators in some cases, for convenience. For example: 
`&` represents relation intersection. 
`#` represents iteration, e.g. `f#3` is `f f f`. 

There are many more examples, but the point is that we can exploit some familiar regex notation as relation algebras are Kleene algebras. 

## The whole operator basis
### Kleene Algebra Combinators
- ` ` (juxtaposition) - relation composition
- `|` - relation union, forms a semiring with the above
### Set Combinators
- `|` - relation union
- `&` - relation intersection
- `!` - relation complement. Unary, prefix. Constructs the relation that (only) has all of the edges that the current relation does not have. 
- `~` - relation converse. Unary, prefix. Evaluates a program backwards by swapping the domain and codomain around, with all the same edges. Because relations form a dagger category, programs as relations yield their categorical dual when evaluated backwards. 
### Arrow Combinators
- `$` - Fan in 
- `@` - Fan out
- `%` - connects fanned relations, equivalent to `+++` / `***` in Haskell. 
### Null Relations
- `id` - Identity relation, null of composition. 
- `ab` - Empty relation, always returns the empty set, null of union. 
- `un` - Universal relation, any input will return the set of all possible outputs, null of intersection. 

## Syntax
Our type syntax uses lowercase letters for type aliases, uppercase letters for generic values, and numbers for generic costacks. `~` is used as the relation binder. Relations can be curried. 
