# Scala Underscores:

## for everything:  

	import com.pacakgeA._

## Give me a variable name but I don't care what is is.

	var count : Int = _

## Don't import from name space  
```scala
  import java.util.{Date => _, _}

  val date = new Date() // Error, Date not found
```

## Unused variables:

```scala
(1 to 5) foreach { (x:Int) => println("x is not used") }

can be

(1 to 5) foreach { _ => println("x is not used") }

in pattern match:

a match {
case x => println("…")
}

can be

a match {
case _ => println("…")
}
```

## Anonymous Paramters
```scala
	(1 to 10) map { x => x + 1 }  can be (1 to 10) map { _ + 1 }

	Also works with several elements
	(1 to 10) foldLeft(0){ (x,y) => x + y } can be (1 to 10) foldLeft(0) { _ + _ }

	But doesn't work below because the compiler gives a new fresh name for each underscore even if it is supposed to refer to the same value:

	scala> (1 to 10) map { _ / 2 + _ }
	<console>:9: error: wrong number of parameters; expected = 1
    	          (1 to 10) map { _ / 2 + _ }
```
## existential type:

```scala
scala> def size(objs: List[T] forSome { type T }) = objs
size: (objs: List[_])List[Any]

scala> def size(objs: List[_]) = objs
size: (objs: List[_])List[Any]
```

Also in patten matching:

```scala
scala> Some("Hell") match {
     | case x: Option[_] => println("option")
     | case x => println("Something Else: " + x)
     | }
option
```

## accessors in tuples:
```scala 
  (1,2)._1 // => 1
```

## _* //varags:
```scala
  scala> "One: %s, Two : %s".format(Seq(1,2):_*)
  res73: String = One: 1, Two : 2
```

## setter name: _=

