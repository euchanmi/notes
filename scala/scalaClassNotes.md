# scalaClassNotes

## Stream:
+ Stream are similar to List but their tails are evaluated only on demand.
   + (1 to 1000).toStream => [tail = 1 | ? (tail pointer has not be evaulated) ] 
+ Use Stream instead of List or Vector if only need to examine partial elements and thus no need to construct
  a entire list. 
+ The operator “::” does not create stream. Instead, use “#::”. 

## Maps:
+ Class Map[Key, Value] also extends from function type Key => Value,
  so maps can be used everywhere functions can.
+ withDefaultValue gives a map to a defailt value.
   ```scala val cap1 = Map().withDefaultValue(“Default”)
    cap(“Unknown key”) ==> “Default”
  ```

## Vector:
+ List is _linear_: access to the first element is much faster than access to the middle or end of a list.
+ Vector has more evenly balanced access patterns than List.
+ Once the size of vertor becomes 32, then the vector becomes an vector of pointers to a array of 32 elements. 
If 32 * 32 also becomes maxed out, it becomes a vector of pointers of pointers of array of 32 elements.
+ Then why use List at all? to taking Head and Tail, list works better as accessing the head element and tail elements are constant in List.
Thus, recursive functions that uses head and tail work better with List.
+ If higher order functions such as map, filter, and etc are used then Vector is more preferred. 
+ Vector does not have “::”. Insted, it uses “:+” or “+:”.
   + Remember => “:” points to where the collection is.

## Ranges:
+ Has three operators:
   + “to”(inclusive)
   + “until”(exclusive)
   + “by”(to determine step value)
+ 1 to 10 by 3 ==> 1, 4, 7, 10
+ 6 to 1 by -2 ==> 6, 4, 2

## For-Expression Example:
```scala
   case clas Person(name: String, age: Int)

   for( p ← persons if p.age > 20 ) yield p.name
   
   ==> is equivalent to

   persons fileter (p => p.age > 20) map (p => p.name)
```

## Set:
+ The differences between sets and sequences are:
   + Sets are unordered; the elements of a set do not have a predefined order in whichthey apprear in the set.
   + Sets do not have duplicate elements.
   + The fundamental operation on sets is contains.

## Generalization of for:
+ the translation of for is not limited to lists or sequences, or even collections;
  It is based soley on the presence of the methods map, flatMap and withFilter.
  This lets you use the for syntax for your own types as well - you must only define 
  map, flatMap, and withFilter for these types. There are many types for which this is
  usefilt: arrays, iterators, databases, XML data,, optional values, parsers, etc.

## Collections:
+ Operators ending in “:” associate to the right and are seen as method calls of the
  right-hand operand.
   + 1 :: 2 :: 3 :: 4 :: Nil ==> Nil.::(4).::(3).::(2).::(1)
+ “++” concatenates two lists.
   + xs ++ ys => all elements in xs followed by all elements in ys.

## Type Bounds:
+ “<:” is an uppper bound. “S <: T” means S is a subtype of T
+ “>:” is a lower bound. “S >: T” means S is a supertype of T, or T is subtype of S.
+ “S >: NonEmpty <: IntSet” is a mixed bound. 
   + “S >: NonEmpty <: IntSet” means S is a super type of NonEmpty but subtype of IntSet.

## Functions as Objects:
+ function values are treated as object
+ The function type A => B is just an abbreviation for the class scala.Function1[A, B].
+ An anonymous function such as 
```scala
   (x: Int) => x * x

   is expanded to

   { 
      class AnonFun extends Function1[Int, Int] { def apply(x: Int) = x * x }
      new AnonFun
   }

   or shorter, using anonymous class syntax:

   new Function1[Int, Int] { def apply(x: Int) = x * x }
```
+ However, methods in a class or object that are marked as “def” is not itset a function value.
  If it were a function value, then the Function 1 class has an apply method which is a Function1 
  class which also has an apply method.. -> it causes an infinite expension.

## Object Oriented Perspective:
+ All is an object in scala such as Int or Boolean.
+ But for reasons of efficiency, the Scala compiler represents the values of type scala.Int
  by 32-bit integers, and the values of type scala.Boolean by Java’s Booleans, etc. 

## Type Inference:
+ The scala compiler can usually deduce the correct tupe parameters from the value arguments of
  a function call.
+ So, in most cases, type parameters can be left out.

## Types and Evaluation:
+ Type parameters do not affect evaluation in scala. We can assume that all type parameters and
  and type arguments are removed before evaluating the program.
+ This is because type parameters and arguments are only important to compiler as to check whether everything is correct.
  But it is not important to execute the program. So type information is not stored during the 
  run time. 
+ This is called _type erasure_.
+ Languages that use type erasure: 
   + Scala, Java, ML, Haskell, ML, OCaml,
+ Languages that do keep the type parameters around at runtime:
   + C++, C#, F#

## Evaluation Strategy:
+ call-by-value
   + it has the advantage that it evaluates every funciton argument only once.
+ call-by-name
   + it has the advantage that a function argument is not evaluated if the corresponding parameter is unused in the 
     evaluation of the function body.
+ Scala uses call-by-value by default since in real life, call-by-value is much more efficient.
+ But Scala also allows to use call-by-name too if user wants it too by putting an arrow between the variable name and 
  the type.
+ For example:
  ```scala 
  scala> def loop: Int = loop
  loop: Int

  scala> def first(x: Int, y: => Int) = x //call-by-name so loop never gets executed.
  first: (x: Int, y: => Int)Int

  scala> first(1, loop)
  res0: Int = 1 

  scala> def second(x:Int, y:Int) = x // call-by-value so loop gets executed.
  second: (x: Int, y: Int)Int

  scala> second(1,loop) // will cause stack overflow eventually
  ```
+ The _def_ form is “by-name”, its right hand side is evaluated on each use.
   + ```scala def first(x: Int, y: Int) ```
+ The _val_ form is “by-value” as its right hand side is evaluated on its initialization.
   + ```scala val x = 2 ```

## Conditional Expressions:
+ “if-else”
   + It lloks like a _if-else_ in Java, but is used for expressions, not statements which means it returns a value.

## Expressions:
+ An _identifier_ such as x, isGoodEnough
+ A _literal_, like 0, 1.0, “abc”
+ A _function application_, like sqrt(x)
+ An _operator application_, like -x, y + x,
+ A _selection, like math.abs
+ A _conditional expression_, like if(x < 0) -x else x,
+ A _block_, like { val x = math.abs(y) ; x * 2)
+ An _anonymous function_, like x => x + 1

## Definition:
+ A _function definition_, like ```scala def square(x: Int) = x * * ```
+ A _value definition_, like ```scala val y = square(2) ```

## Parameter:
+ A _call-by-value parameter_, like (x: Int)
+ A call-by-name paraeter, like (y: => Double)

## Blocks:
+ Blocks are themselves expressions; a block may appear everywhere an expression can.

## Recursive Function:
+ need an explicit return type in Scala, whereas non recursive functions do not.

## First class function:
+ Functional language treat functions as _first class values_. This means that, like any other value, a function can
  be passed as a parameter and returned as a result.

## Higher order function:
+ Functions that take other functions as parameters or that return functions as results.

## Currying function:
+ Currying process transforms a function of mutiple argument into a function of single argument.
+ ```scala 
  def add(x: Int, y: Int) = x + y   // before currying
  def add(x:Int) = (y: Int) = x + y // after currying
  def add(x: Int)(y: Int) = x + y   // syntatic sugar for currying
  def curriedAdd = Function.curried(add _) // currying an already defined function 
  def uncurriedAdd = Function.uncurried(add _) // inverting currying
  ```

## Data abstraction:
+ Data abstraction is an ability to choose different implementations of the data with out effecting clients.
+ It is a cornerstone of software engineering.

## Require and assert:
+ require is used to enfore a precondition on the caller of a function.
+ assert is used as to check the code of the function itself.
```scala
   def reqTest(num: Int) = require(num > 0, “num should be greater than zero”)
   val x = sqrt(y) ; assert(x >= 0)
```

## Primary constructor:
+ Takes the parameters of the class.
+ And executes all statements in the class body.

## Overloaded Constructor:
+ use _this_ keyword:
```scala
   class Rational(x: Int, y: Int) {
      def this(x: Int) = this(x, 1)
   }
```

## Relaxed Identifiers:
+ Operators can be used as identifiers.
+ Identifiers can be:
   + Alphanumeric: starting with a letter, followed by a sequence of letters of numbers.
   + Symbolic: starting with an operator symbol, followed by other operator symbols.
+ The underscore character ‘_’ counts as a letter
+ Alphanumeric identifiers can also end in an underscore, followed by some operator symbols.
+ Examples: ‘x1’, ‘*’, ‘+?%&’, ‘vector_++’, ‘counter_=’
+ For prefix operators, use ‘unary_’ prefix:
```scala
   def unary_- : Rational = new Rational(-numer, denom)
   
   val that = new Rational(1,2)
   —that // => Rational(-1,2) 
```
+ When defining a prefix operator, put space between the end of the def name and colon(:) otherwise ‘:’ will be counted as the entire function name since ‘:’ is allegible operator symbol.
+ So if operators such as ‘*’ can be user defined, then how about precedence? Is the regulatr operator execution priority still applies?
   + Answer to that is “yes”. Even if it’s user defined operatios, the regular precedence is still applied.

## Abstract class:
+ Abstract classes can contain members which are missing an implementation.
+ Consequently, no instances of an abstract class can be created with the operator new.

## Imports:
+ You can import from either a package or an object
+ The followins are automatically imported:
   + All members of package scala
   + All members of package java.lang
   + All mebers of the singleton object scala.Predef

## Trait:
+ Traits resemble interfaces in Java, but are more powerful because they can contains fields and concrete methods.
+ On the other hand, traits cannot have(value) parameters, only classes can.

## Class Hierarchy:
+ Any is the root class of every class.
   + impement methods ‘==’, ‘!=’, ‘equals’, ‘hashCode’, and ‘toString’
+ AnyVal is the root class of Primitive type classes such as Double, Int, Boolean, Char, and Unit.
+ AnyRef is the root class of all other classes. AnyRef is just am alias java.lang.Object. 
   + Also, any classes that extend from AnyRef also implement trait called ScalaObject.  
+ Nothing is sub-type of all classes in scala so it’s opposite to Any.
   + Nothing is at the botoom of Scala’s type hierarchy. It is a subtype of every other type. There is no value of type Nothing. 
      + Then, why is it useful?
      + To signal abnormal termination.
      + As an element type of empty collections.
  
  ```scala 
    throw new Exception // <== this returns Nothing 
  ```
+ Null is sub-type fo all classes that extend from AnyRef.
+ Every reference class type also has null as a value.
+ The type of null is Null.
+ Null is a subtype fo every class that inherits from Object; it is incompatible with subtypes of AnyVal.
