# Factorial
```
fun fac => 
  : as 0; 1
  : as n; n * (n-1) fac
```
```
fun n fac =>
  (n > 0) (1 | n * (n-1) fac)
```
```
fun fac => 
  1 (as n a; (n > 0) (n - 1) (n * a))* nip
```
```
fun fac => (1..) product
```