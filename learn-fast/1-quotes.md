# Quotes & Combinators
## Quotes
Prowl is a higher-order stack language. This means it has a feature called *quoting* which allows snippets of code to be used as values. Unlike in languages like Factor and Lisp, quotes are *opaque* in Prowl -- this makes them typesafe and have nice properties, similar to first-class functions in functional languages. 

```
val q = {(+ 1) 2}  /* quote creation is with {} */
rel {g} f -- g (-)  /* g is extracted via destructuring */
val x = 2 q f      /* x = 1 */
```

## Combinators
While named parameters, constructors and destructuring is enough to do anything, Prowl also includes a number of stack combinators for writing concise code. Here are some of them: 

```
rel  _          zap  -- id  /* id is the relation that does nothing */
rel {f}         call -- f
rel  x          mono -- {x}
rel {f}         run  -- f {f}
rel  x          dup  -- x x
rel {f}         duco -- {{f} f}
rel  _   y      nip  -- y
rel {f} {g}     sap  -- g f
rel  x  {f}     dip  -- f x
rel {f} {g}     cat  -- {f g}
rel {f} {g}     swat -- {g f}
rel  x   y      swap -- y x
rel  x  {f}     cons -- {x f}
rel {f}  x      tack -- {f x}
rel  x  {f}     sip  -- x f x
rel  x   y      peek -- x y x
rel  x   y   z  dig  -- y z x
rel  x   y   z  bury -- z x y

rel {f}         i -- f
rel {f}         m -- {f} f
rel  _  {f}     k -- f
rel {f}  _      z -- f
rel {f}  x      t -- x f
rel  x  {f}     w -- x x f
rel  x  {f} {g} s -- {x f} x g
```

Some of these are very useful and used very often, others may be more obsure and needed less often. The ones at the bottom come from combinatory logic (you may recognize the s, k, i, combinators). 

## Typestacks
In order to type concatenative programming languages, it becomes necessary to deal with something called *stack polymorphism*. Stack polymorphism describes the property that a function (or a relation) only cares about the top of the stack, and the rest of the stack can be anything. 
```
spec (+) : int int -- int
```
Hopefully the above specification looks familiar. All specifications in Prowl are stack polymorphic *by default*. In other words, the above is sugar for the following: 
```
spec (+) : 0 int int -- 0 int  /* 0 is a typestack */
```
Typestacks are numbered to contrast with type names (lowercase) and generics (caps). The `0` can be thought of as "the rest of the stack" -- it's simply encoding that the stack before and after the addition function can be anything, but is unchanged by the call. 

For example: 
```
3 4 (+)      /* 0 is `.` (the empty stack) */
5 2 7 (+)    /* 0 is `. 5` (a stack containing only 5) */
8 2 4 4 (+)  /* 0 is `. 8 2` */
```

Typestacks become important for expressing certain types when the stack on both sides of `--` isn't the same (which is the default behavior). Typestacks are almost always leftmost, e.g. `0 int` as opposed to `int 0` or `0 1`. Nearly all functions can be specified with leftmost typestacks, and when typestacks are not leftmost, type inference and type checking become significantly more challenging (however I have plans that will allow specific kinds of non-leftmost typestacks). 

Here are the types of some of the above combinators: 
```
spec zap  : A --
spec call : 0 {0 -- 1} -- 1
spec mono : A -- {. -- A}       /* . is the empty stack */
spec dup  : A -- A A
spec nip  : B A -- A
spec dip  : 0 A {0 -- 1} -- 1 A
spec cat  : {0 -- 1} {1 -- 2} -- {0 -- 2}
spec cons : A {0 A -- 1} -- {0 -- 1}
```
