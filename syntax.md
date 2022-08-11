# Syntax Quick Guide

## Definitions, Specifications, Expression, and Anonymous forms chart
(technicalities like optional annotations are omitted from the grammars)

| Definition | Specification | Binding Expression | Anonymous |
--- | --- | --- | ---
| `val <pat> = <expr>` | `spec : <type>` | `val <pat> = <expr>; <expr>` | `as <pat>; <expr>` |
| `rel <pat> <name> -- <expr>` | `spec : <type>` | `rel <pat> <name> -- <expr>; <expr>` | `by <pat> <name> -- <expr>` |
| `proc <pat> <name> [<block>]` | `spec : <type>` | `proc <pat> <name> [<block>]; <expr>` | `do <pat> [<block>]` |
| `type <params> <name> = <type>` | `kind <name> : <kind>` | | |
| `mod <mod-pat> <mod-name> = <mod-expr>` | | `mod <mod-pat> <mod-name> = <mod-expr>; <expr>` | |
| `sig <mod-name> = <mod-expr>` | | `sig <mod-name> = <mod-expr>; <expr>` | |
| `open <mod-expr>` | | `open <mod-expr>; <expr>` | |
| `mix <mod-expr>` | | | 

# Operators

### Involutions (Prefix)
- `~` Converse
- `!` Complementation

## Polygon Operators
| Op | Top | Qt | Map | Ap | 
--- | --- | --- | --- | ---
| Cat | ` ` | `>>` | `~>` | `<~>` | 
| Dis | `;` | `\/` | `+>` | `<+>` | 
| Con | `,` | `/\` | `*>` | `<*>` | 
| RSum | `$` | `$$` | `$>` | `<$>` | 
| AProd | `@` | `@@` | `@>` | `<@>` | 
| RFanin | `\|` | `\|\|` | `\|>` | `<\|>` | 
| RFanout | `%` | `%%` | `%>` | `<%>` | 
| AFanout | `&` | `&&` | `&>` | `<&>` | 
| Residual | `\` | `\\` | `\>` | `<\>` | 
| SymDiff | `^` | `^^` | `^>` | `<^>` | 

There are also bind, monad composition, comonad, and comonad composition versions following a similar pattern
`>>~`, `>~>`, `~>>`, `~>~` etc
as well as all flipped versions `<~`, `<<~`, `<~<`, ...

### More operators
- `**` Exp
- `++` Append
- `#`, `##` Iteration

# Literals
| Type | Syntax | 
--- | --- 
| `int` | `52` | 
| `float` | `52.0` | 
| `string` | `"fifty-two"` | 
| `char`  | `'f'` | 

### Constructors
```
{1, 2, 3}         list
[5]               quote
[2; 3; 4]         quotes can contain ; (union) and , (intersection)
#[1, 2, 3]        array
%[1 => 2, 3 => 4] map
#{
  the="quick", 
  brown="fox", 
}                 record
```
