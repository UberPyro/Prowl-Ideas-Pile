# Syntax Quick Guide

Definitions, Specifications, Expression, and Anonymous forms chart
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