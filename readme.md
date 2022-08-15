# Prowl Floordrobe
Ideas pile for Prowl ([tutorial](./learn-fast)).

Prowl is a purely-functional, statically typed, modular, concatenative relational programming language. 

Main inspirations include: 
- Kitten
- Haskell
- ML Family
- Joy
- Regex

Much of our look is borrowed from SML and Reason, though we have stack + relational + regex semantics. 
```
rel fac -- 1 (as n a -> (n > 0) (n - 1) (n * a))* nip
```
You can write this in a more functional style if you prefer. 
```
rel fac --
  : as 0 -> 1
  : as n -> n * (n-1) fac
```

Check out [learn-fast](./learn-fast) for more. 
An [interpreter](https://github.com/UberPyro/prowl) exists for a much older, much less cool version of the language. 