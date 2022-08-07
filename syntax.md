# Syntax Quick Guide

## Definitions, Specifications, Expression, and Anonymous forms chart
(technicalities like optional annotations are omitted from the grammars)

| Definition | Specification | Binding Expression | Anonymous |
--- | --- | --- | ---
| `val <pat> = <expr>` | `spec : <type>` | `val <pat> = <expr>; <expr>` | `as <pat>; <expr>` |
| `fun <pat> <name> => <expr>` | `spec : <type>` | `fun <pat> <name> => <expr>; <expr>` | `lam <pat> <name> => <expr>` |
| `proc <pat> <name> {<block>}` | `spec : <type>` | `proc <pat> <name> {<block>}; <expr>` | `do <pat> {<block>}` |
| `type <params> <name> = <type>` | `kind <params> <name>` | | |
| `mod <mod-pat> <mod-name> = <mod-expr>` | | `mod <mod-pat> <mod-name> = <mod-expr>; <expr>` | |
| `sig <mod-name> = <mod-expr>` | | `sig <mod-name> = <mod-expr>; <expr>` | |
| `open <mod-expr>` | | `open <mod-expr>; <expr>` | |
| `mix <mod-expr>` | | | 

# Operators

## Polygon Operators (Relations and Arrows)
| Operation | Top-level | Quote-level | Lifting |
--- | --- | --- | ---
| Concatenation | ` `, `~` | `>>` | `->` |
| Alternation | `(.. ; ..)` | `<>` | `>-` |
| Intersection | `(.. , ..)` | `::` | `:>` |
| Involution (prefix) | `^` | `^^` | | 
| Complementary (prefix) | `!` | `!!` | | 
| Sum | `$` | `$$` | `$>` |
| Sum (fanin) | `\|` | `\|\|` | `\|>` | 
| Arrow Product | `@` | `@@` | `@>` |
| Arrow Product (fanout) | `&` | `&&` | `&>`
| Relational Product (fanout) | `%` | `%%` | `%>` |

(non-fan relational product is the same as sum because category theory is weird)