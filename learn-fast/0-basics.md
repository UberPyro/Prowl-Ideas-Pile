# Learn Fast
If you're not familiar with the stack paradigm, you can [check](http://kittenlang.org/tutorial/) [out](http://kittenlang.org/intro/) the intro material for Kitten. 

## Introduction
Prowl at it's core is a *concatenative*, *relational* language. 
- Concatenative Languages are composed of functions of stacks: functions pop arguments off of a stack and then push their results to it. The fact that most functions only care about the top of each stack and are invariant to the rest of them is a powerful property that aids in expressiveness, and concatenative languages permit better pointfree styles than what is offered in applicative languages. In short, concatenative languages permit highly factorable, highly composeable code. 
- Relational Languages are composed of relations rather than functions. Relations are chosen over functions because they are more encompassing yet have much nicer algebraic properties. For example, relations have a *converse*, like an inverse function, except **all** relations have them. They also have lattice properties like union and intersection, and union forms a Kleene algebra with relation composition. 
Prowl is a concatenative, relational language, which means **all expressions are relations between stacks**. By combining these paradigms, we can stack their benefits on top of each other and enjoy writing highly expressive code. Maybe the benefits are greater than the sum of its parts, as juxtaposition on a Kleene algebra allows us to write code as regular expressions using an alphabet of relations. 

## Definition & Expression Languages
```
/* This is a comment */
/* Comments are
   multiline */
/*/* Comments can be nested */*/
```

### Value Definitions
Prowl source files are modules, which are composed of sequences of definitions. Value definitions have patterns left of the `=` and expressions right of the `=`. The number of elements pushed by the expression on the right must equal the number of patterns.
```
val x = 5  /* x = 5 */
val x y = 5 6  /* x = 5, y = 6 */
val x = 5 6  /* type error */
val x y = 5  /* type error */
```

### Infix
Prowl has infix operators. Concatenation binds tighter than nearly all infix. 
```
val x = 2 + 3  /* x = 5 */
val x = 1 + 2 * 3  /* x = 7 */
val x = 1 2 + 1  /* type error, `1 2` is not an integer. */
```

### Sectioniong
Prowl has sectioning, like in Haskell. Sectioned operators are evaluated in RPN fashion. 
```
val x = 2 3 (+)  /* x = 5 */
val x = 7 3 (5 -) (+)  /* x = 9 */
val x = 7 3 (- 5) (+)  /* x = 5 */
```

### Relation Definitions
Relations can be defined with `rel`. They are a lot like functions in other languages. The name of the relation is the final identifier before the `--`. 
```
rel succ -- (+ 1)
rel x y norm-sqr -- x**2 + y**2

val x = 5 succ succ  /* x = 7 */
val x = 3 4 norm-sqr  /* x = 25 */
```

### Binding Expressions
Most definitions can be promoted to expressions. They must terminate with `;` to be delimited from other expressions. 
```
rel x y norm-sqr -- 
  rel x sqr -- x * x;  /* shadowing is allowed */
  val x-sqr = x sqr; 
  val y-sqr = y sqr; 
  x-sqr + y-sqr
```

`as` is the anonymous analogue to `val`. `as` pops elements off of the stack instead of matching a built-up virtual stack. 
```
rel x y norm-sqr --
  x**2 as x-sqr;
  y**2 as y-sqr;
  x-sqr + y-sqr

rel x y norm-sqr --
  x**2 y**2 as x-sqr y-sqr; 
  x-sqr + y-sqr

/* The `as x; x * 2` here is a function, just like an identifier. */
val x = 2 + 3 as x; x * 2  /* x = 8 */
```

## Types & Specifications
Prowl is statically-typed with full type inference. 
| Ex | Type | 
-- | --
| `5` | `int` | 
| `5.0` | `float` | 
| `"hi"` | `str` | 
| `'h'` | `char` | 

```
spec x : int
val x = 5

spec x : int
spec y : float
val x y = 5 6.0

spec x : int
rel x -- 2

spec s : int -- int  /* `--` represents a stack relation.  */
rel x s -- s + 1     /* (known as a stack effect in other stack langauges) */

/* Note that if `--` isn't written, it's implied to be leftmost, e.g. 
   the type `int` is really `-- int` */

spec f : int int
rel f -- 0 0

/* `type` can be used to alias types */
type int2 = int int
spec f : intfn
rel f -- (+ 1)

type intfn = int -- int
spec f : int2
rel f -- 0 0

/* types concatenate just like values do */
/* some types take in parameters, which can be tucked in left of the = */
type opt-pair = A -- A opt A opt pair  /* opt takes 1 parameter, pair takes 2 */
type A opt-pair = A opt A opt pair
```
