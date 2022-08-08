# Prowl Floordrobe
Ideas pile for Prowl. 

Prowl is a purely-functional, statically typed, modular, concatenative relational programming language. 

Main inspirations include: 
- Kitten
- Haskell
- ML Family
- Joy
- Regex

Much of our look is borrowed from SML and Reason, though we have stack + relational + regex semantics. 
```
fun fac => 1 (as n a; (n>1) ? (n-1) (n*a))* nip
```
You can write this in a more functional style if you prefer. 
```
fun fac => 
  : as 0; 1
  : as n; n * (n-1) fac
```

Check out [learn-fast](./learn-fast) for more. 