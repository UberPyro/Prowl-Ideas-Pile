# Learn Fast
```
/* This is a comment */
/* Comments are
   multiline */
/*/* Comments can be nested */*/

/* Files are modules, which are sequences of definitions and specifications. 
   Some definitions bind expressions to identifiers. */

/* Prowl is a stack language - if you don't know what that is, then take a 
   moment to look it up. All literals are functions that push a value onto 
   the stack. */

/* Value definitions bind the contents of a built-up stack on the RHS to 
   the provided names on the LHS. The number of elements in the stack 
   must match the number of patterns. */

val x = 5  /* x = 5 */
val x y = 5 6  /* x = 5, y = 6 */
val x = 5 6  /* type error */
val x y = 5  /* type error */

/* Prowl has infix operators. Concatenation is faster than nearly all infix. */

val x = 2 + 3  /* x = 5 */
val x = 1 + 2 * 3  /* x = 7 */
val x = 1 2 + 1  /* type error, `1 2` is not an integer. */

/* Prowl has sectioning, like in Haskell. Sectioned operators are evaluated in 
   RPN.  */
val x = 2 3 (+)  /* x = 5 */
val x = 7 3 (5 -) (+)  /* x = 9 */
val x = 7 3 (- 5) (+)  /* x = 5 */

/* functions can be defined with fun. The name of the function is the 
   last identifier before the => */
fun succ => (+ 1)
fun x y norm-sqr => x^2 + y^2
fun x y norm-sqr => x^2 y^2 (+)  /* exp is faster than concat */

val x = 5 succ succ  /* x = 7 */
val x = 3 4 norm-sqr  /* x = 25 */

/* Most definitions can be lifted to expressions. They must terminate with `;`
   to be delimited from expressions. */

fun x y norm-sqr => 
  fun sqr x => x * x;  /* shadowing is allowed */
  val x-sqr = x sqr; 
  val y-sqr = y sqr; 
  x-sqr + y-sqr

/* `as` is the anonymous analogue to `val`. `as` pops elements off of the
   stack instead of building one up. */

fun x y norm-sqr => 
  x^2 as x-sqr;
  y^2 as y-sqr;
  x-sqr + y-sqr

fun x y norm-sqr => 
  x^2 y^2 as x-sqr y-sqr; 
  x-sqr + y-sqr

/* The `as x; x * 2` here is a function, just like an identifier. */
val x = 2 + 3 as x; x * 2  /* x = 8 */
```