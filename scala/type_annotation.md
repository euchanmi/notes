## Lazy evaluation
```scala
def orElse[B >: A](ob: => Option[B]): Option[B] 
```

The ```ob => Option[B]``` type annotation above indicaes the arugment will not be evaluated until it is needed by
the function.
