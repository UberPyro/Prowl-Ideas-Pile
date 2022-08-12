# Relations
All of the computations we have seen thus far have been *deterministic*. Everything is a function, even literals, and everything has been total. Even functions that push to the costack are total, as all their branches have to be checked. However, Prowl is a *relational* programming language: everything is a relation, and total functions are just a subset of that. There are many motivations for relational programming, but Prowl is motivated mainly by clean algebraic properties. 

Relations behave much nicer than functions in general. Converse relations (inverting) always exists and always produces a new relation, whereas function inverses often don't exist. Relations are sets -- they can undergo union and intersection, which functions have no concept of, and form a Kleene algebra between composition and union, which opens the door to explore with regex. 

Additional properties by themselves do not mean much in terms of expressivity if they aren't useful. However, the ability to write code as regular expressions utilizing the Kleene algebra is the main force driving Prowl. Prowl must be concatenative as to be composed of strings of juxtaposed elements; Prowl must be relational so that operations can be added together, just as in regex. The benefits and tradeoffs of this design run deep and are better suited for a different document, but the promise of Prowl is to produce highly composeable, factorable, and expressive code using semantics that's removed from how a computer works but still guided in ways that help performance. And even if things don't quite work out, the language concept is just too cool to not try. 

## Basic Operators
- Composition: ` `
- Union: `(.. ; ..)`
- Intersection: `(.. , ..)`

You can imagine relations as sets of pairs, where the left element is an input and the right element is an output. For example, `succ` would look something roughly like `{..., (1, 2), (2, 3), (3, 4), ...}` in a math-like notation. Union and intersection make sense with respect to those definitions. 

```
rel f -- (1; 2)  /* union of 1 and 2 */
rel g -- (2; 3)

rel u -- (f; g)  /* produces (1; 2; 3) */
rel v -- (f, g)  /* produces 2 */
```

Composition distributes or "foils" terms in cartesian product style. This is similar to the list applicative functor if you know Haskell. Note that this is using sets though. 

```
rel a -- (1; 2) (+ 2)  /* makes (3; 4) */
rel b -- 1 (+ 2; - 2)  /* makes (3; -1) */
rel c -- (1; 2) (+ 2; - 2)  /* makes (3; 4; -1; 0) */
rel d -- (+ 3) (+ 1; - 1)  /* semantically (+ 4; + 2) */
```

Laws
```
/* Distributive */
f (g; h) = (f g; f h)
(f, (g; h)) = (f, g; f, h) ( = ((f, g); (f, h)) )
(f; (g, h)) = ((f; g), (f; h))  /* lattice property */
```

### Additional set operators
- Set Difference: `\`
- Symmetric Difference: `^`

## Nulls
- `id` (identity), the null of composition, has no effect on the stack. It's like the equivalence relation in mathematics. 
- `ab` (absurdity), the null of unions, represents the empty relation: the relation that maps all values to none. 
- `un` (universality), the null of intersections, represents the universal relation: the relation that maps all values to the set of all values in a particular type. 

Laws
```
/* identity */
id f = f id = f
(ab; f) = (f; ab) = f
(un, f) = (f, un) = f

/* annihilation */
f ab = ab f = ab
(f, ab) = (ab, f) = ab
(f; un) = (un; f) = un
```

## Involutions
"Involution" is a math word that refers to operators that do nothing when applied twice, like negation and finding a transpose. The following are unary, prefix operators. 
- Complementation: `!`
- Converse: `~`

Complementation finds the set difference between all of the inhabitants in its operand's type (the universe) and it's operand (which is itself a set). `!(5; 6)` are all integers that are not `5` and `6`. Similarly, `!(succ; prec)` would be the relation `int -- int` that maps all integers to the integers that are not one greater than or less than themselves. Complementation is often expensive to compute -- sets in Prowl are coalesced in lazy ways, which makes reasoning like this possible, even for infinite sets. However, applying a forcing operator at a bad time will cause problems. 

Converse finds the converse relation, which is like an inverse function, except for relations they always exist -- the inputs and outputs are simply switched. Converses of relations are often interesting and surprising, more so than complements. 

Let's introduce a third trusting operation, `part`, to act as a bridge between the language systems: 
```
spec part : 1 | 0 -- 0
rel part -- (ab | id)
```
`part` converts a function that pushes to the costack into a partial function in the relation system. 

- `~((>= 0) part ^ (< 20) part)` would be the range `(0..19)` as a set. 
- `~(part dup)` is the same as `(==)`
- `~swap` is `swap`

In general, `~(a b c...)` is the same as `... ~c ~b ~a`, which can help reasoning (and helps keep things computationally efficient). 

## Arrows based on Relations
Due to connections with arrows and category theory, relations lend themselves to yet another set of sums and products. 
- Addition:  `$`
- Fan In: `|`
- Fan Out: `%`

At this point, you should be no stranger to `|`. `|` is "Fan In", or the `pick` operator, which collapses the costack by providing functions with converging output types. It's analogous to `|||` if you're familiar with arrows. 
```
/* (|) */ spec pick : [0 -- 2] | [1 -- 2] -- [0 | 1 -- 2]
```

The operator corresponding to addition, `$`, which I call `ponder`, is similar to the above but rather "bundles" two functions together, like `+++` with arrows. 
```
/* ($) */ spec ponder : [0 -- 1] | [2 -- 3] -- [0 | 2 -- 1 | 3]
```
Logically, this says: "If I have two functions, which accept different arguments, and produce different arguments, I can make a new function that accepts either argument and produces either argument". Makes sense. 

Thanks to some strangeness in category theory, the "bundling" function for products is exactly the same! This makes sense once you understand fan out: 
```
/* (%) */ spec stock : [0 -- 1] [0 -- 2] -- [0 -- 1 | 2]
```
Products fundamentally rely on unions rather than on a product type. This function says, "If I have two functions with the same input type but different output types, I can make a new function with the same input type and produces **both** output types". It can produce *both* output types by summing a set of lefts and rights together (e.g. have multiple items sitting on top and at the second level of the costack). This is important because the multiplicities of the top and second items don't have to match like they do with functions, but the unions save us from having no product at all. 

## Arrows Based on Functions
Prowl also supports arrows that are more similar to the ones in Haskell, that are more function-like rather than relation-like. These are useful for things that are concatenative and function-like but not relation-like, for example, managing IO and state. 
- Product: `@`
- Fan Out: `&`
Arrows are able to share their sum operators with the previous set as they work exactly the same. The new set of operators work based off of product types -- our stack. 
```
/* (@) */ spec pile : [A -- B] [C -- D] -- [A C -- B D]
/* (&) */ spec hoard : [A -- B] [A -- C] -- [A -- B C]
```

## Nondeterministic Quantification
Now that we understand the relation system, we can examine nondeterministic control flow. 

### Ranges
`(n..m)` creates a set from `n` to `m`, inclusive. `..` can be sectioned like any other infix operator. 

## Nondeterministic Iteration
The iteration operator `#` can accept sets of integers. 
- `f#(1..3)` will produce the set `(f; f f; f f f)`. 
- `f#(0; 2)` will produce the set `(id; f f)`. 

### Regex-esque Quantification
Greedy (nondeterministic) regex operators are the same as their deterministic counterparts, but with `..` following. 
- `f?..` is the same as `f#(0; 1)`. 
- `f*..` is a set that composes `f` with itself until it produces only `ab` (which are irrelevant). 
- `f+..` is the same as above, but attempts to compose `f` with itself at least once. 
Note that `?..` and `*..` promote `f` to being total, due to union with `id`, whereas `+..` will leave the domain of `f` unchanged. 
- `|` corresponds to `( ; )` here. 
