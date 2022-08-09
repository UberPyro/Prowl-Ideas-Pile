# Relations
All of the computations we have seen thus far have been *deterministic*. Everything is a function, even literals, and everything has been total. Even functions that push to the costack are total, as all their branches have to be checked. However, Prowl is a *relational* programming language: everything is a relation, and total functions are just a subset of that. There are many motivations for relational programming, but Prowl is motivated mainly by clean algebraic properties. 

Relations behave much nicer than functions in general. Converse relations (inverting) always exists and always produces a new relation, whereas function inverses often don't exist. Relations are sets -- they can undergo union and intersection, which functions have no concept of, and form a Kleene algebra between composition and union, which opens the door to explore with regex. 

Additional properties by themselves do not mean much in terms of expressivity if they aren't useful. However, the ability to write code as regular expressions utilizing the Kleene algebra is the main force driving Prowl. Prowl must be concatenative as to be composed of strings of juxtaposed elements; Prowl must be relational so that operations can be added together, just as in regex. The benefits and tradeoffs of this design run deep and are better suited for a different document, but the promise of Prowl is to produce highly composeable, factorable, and expressive code using semantics that's removed from how a computer works but still guided in ways that help performance. And even if things don't quite work out, the language concept is just too cool to not try. 

## Top-level Operators
- Composition: ` `, `~`
- Union: `(.. ; ..)`
- Intersection: `(.. , ..)`

You can imagine relations as sets of pairs, where the left element is an input and the right element is an output. For example, `succ` would look something roughly like `{..., (1, 2), (2, 3), (3, 4), ...}` in a math-like notation. Union and intersection make sense with respect to those definitions. 

```
fun f = (1; 2)  /* union of 1 and 2 */
fun g = (2; 3)

fun u = (f; g)  /* produces (1; 2; 3) */
fun v = (f, g)  /* produces 2 */
```

Composition distributes or "foils" terms in cartesian product style. This is similar to the list applicative functor if you know Haskell. Note that this is using sets though. 

```
fun a = (1; 2) (+ 2)  /* makes (3; 4) */
fun b = 1 ((+ 2); (- 2))  /* makes (3; -1) */
fun c = (1; 2) ((+ 2); (- 2))  /* makes (3; 4; -1; 0) */
```

