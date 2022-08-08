# Factorial
### Traditional
```
fun fac => 
  : as 0; 1
  : as n; n * (n-1) fac
```
### Choice combinator
```
fun n fac =>
  (n > 0) (1 | n * (n-1) fac)
```
### Regex style
```
fun fac => 
  1 (as n a; (n > 0) (n - 1) (n * a))* nip
```
### Refold
```
fun fac => (1..) product
```
