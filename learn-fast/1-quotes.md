# Quotes & Combinators
Prowl is a higher-order stack language. This means it has a feature called *quoting* which allows snippets of code to be used as values. Unlike in languages like Factor and Lisp, quotes are *opaque* in Prowl -- this makes them typesafe and have nice properties, similar to first-class functions in functional languages. 

```
val q = {(+ 1) 2}  /* quote creation is with {} */
fun {g} f => g (-)  /* g is extracted via destructuring */
val x = 2 q f      /* x = 1 */
```

While named parameters, constructors and destructuring is enough to do anything, Prowl also includes a number of stack combinators for writing concise code. Here are some of them: 

```
fun  _          zap => id  /* id is the function that does nothing */
fun [f]         call => f
fun  x          mono => [x]
fun [f]         run => f [f]
fun  x          dup => x x
fun [f]         duco => [[f] f]
fun  _   y      nip => y
fun [f] [g]     sap => g f
fun  x  [f]     dip => f x
fun [f] [g]     cat => [f g]
fun [f] [g]     swat => [g f]
fun  x   y      swap => y x
fun  x  [f]     cons => [x f]
fun [f]  x      tack => [f x]
fun  x  [f]     sip => x f x
fun  x   y      peek => x y x
fun  x   y   z  dig => y z x
fun  x   y   z  bury => z x y

fun [f]         i => f
fun [f]         m => [f] f
fun  _  [f]     k => f
fun [f]  _      z => f
fun [f]  x      t => x f
fun  x  [f]     w => x x f
fun  x  [f] [g] s => [x f] x g
```

Some of these are very useful and used very often, others may be more obsure and needed less often. The ones at the bottom come from combinatory logic (you may recognize the s, k, i, combinators). 

todo: types
todo: literal matching