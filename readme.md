# Prowl Ideas
Ideas pile for Prowl ([tutorial](./learn-fast)).

Prowl is a purely-functional, statically typed, modular, concatenative relational programming language. 

Main inspirations include: 
- Stack Languages (Kitten, Factor, Joy)
- Functional Languages (OCaml, Haskell)
- Relational Languages (miniKanren)
- Regex

Our look is highly original, though loosely inspired by  SML, Reason, Factor, and Regex. 
```
rel n fac -- 
  (n == 0) 1
  : n * (n - 1) fac
```
Alternatively: 
```
rel fac -- (
  as 0 -> 1; 
  as n if n > 0 -> n * (n - 1) fac
)?!
```

Check out [learn-fast](./learn-fast) for more. 
An [interpreter](https://github.com/UberPyro/prowl) exists for a much older, much less cool version of the language. 
