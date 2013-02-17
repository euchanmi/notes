# Scala Note

## Regular Exception Stack Overflow:

+ Stack overflow can happen when using regular expression with stack trace looking like:
'''scala
	at java.util.regex.Pattern$Branch.match(Pattern.java:4131)
	at java.util.regex.Pattern$GroupHead.match(Pattern.java:4185)
	at java.util.regex.Pattern$Loop.match(Pattern.java:4312)
	at java.util.regex.Pattern$GroupTail.match(Pattern.java:4244)
	at java.util.regex.Pattern$BranchConn.match(Pattern.java:4095)
	at java.util.regex.Pattern$CharProperty.match(Pattern.java:3362)
	at java.util.regex.Pattern$Branch.match(Pattern.java:4131)
	at java.util.regex.Pattern$GroupHead.match(Pattern.java:4185)
	at java.util.regex.Pattern$Loop.match(Pattern.java:4312)
	at java.util.regex.Pattern$GroupTail.match(Pattern.java:4244)
	at java.util.regex.Pattern$BranchConn.match(Pattern.java:4095)
	at java.util.regex.Pattern$CharProperty.match(Pattern.java:3362)
	at java.util.regex.Pattern$Branch.match(Pattern.java:4131)
   ...
'''
+ Solution is to avoid alternations (“|”) since they are inefficient. 
+ For example:
   + “^([a-fA-F]|\d)+$” is inefficient. Instead —> “^([0-9a-fA-F])+$” 

## Parser Combinator:

+ For Basic parsers - 
   + Extend 
      + '''scala JavaTokenParsers, RegexParsers '''
+ Parser class is the root of the parsers. It takes an input and produce parset results:
  '''scala   
     def audienceTagLiteral: Parser[String] = """[0-9a-zA-Z_]\w*""".r // This takes a string of chars and numbers

     def audienceGroup = audienceTagLiteral ^^ {
       case audienceTag:String => AudienceConstraint(audienceTag.toString) 
     }  // ==> This takes a string that represetns audience tag and constructs an AudienceConstraint class with it.

     def and = audienceGroup~"AND"~audienceGroup ^^ {
       case audience1~"AND"~audience2 => audience1 and audience2
     }

     def expr: Parser[TargetingConstraint] = term * (
       "AND" ^^^ {(a: TargetingConstraint, b: TargetingConstraint) => a and b} |
       "OR" ^^^ {(a: TargetingConstraint, b: TargetingConstraint) => a or b}
     )

     def parens: Parser[TargetingConstraint] = "(" ~> expr <~ ")"

     def term = audienceGroup | parens

     def apply(s:String):TargetingConstraint = {
       parseAll(expr, s) match {
         case Success(audienceConstraint, _) => audienceConstraint
         case e: NoSuccess =>
           Console.err.println(e)
           throw new IllegalArgumentException("Bad syntax: "+s)
       }
     }     
   '''
+ The double carat ^^ passes the parsed value to a function. So it is a parser combinator that changes a successful 
  result into the specified value. `p ^^ f' succeeds if `p' succeeds; it returns `f' applied to the result of `p', 
  where f a function that will be applied to this parser's result. In another words, it is a parser that has the same 
  behaviour as the current parser, but whose result is transformed by `f'.
+ The tilda ~ is used for sequencing. p1 ~ p2 means must match p1 followed by p2. The function “and” means that
  “123 AND 987” will be parsed as '''scala And(AudienceConstraint(“123”), AudienceConstraint(“987”)) '''
+ The pipe | is used for alternation. p1 | p2 means must match either p1 or p2 with preference given to p1. The function
  term means an input will be parsed thru either audienceGroup or parens parsers with preference given to auidenceGroup 
  parser.
+ p1.?    // optionality: may match p1 or not
+ p1.*    // repetition: matches any number of repetitions of p1
+ The aterisk * means repear the following parser. 
+ The three carat ^^^ is a parser combinator that changes a successful result into the specified value. 
  `p ^^^ v' succeeds if `p' succeeds; discards its result, and returns `v` instead, where v is the new result for the 
  parser, evaluated at most once (if `p` succeeds), not evaluated at all if `p` fails In another words, it returns 
  a parser that has the same behaviour as the current parser, but whose result is transformed by `f'.
+ The “~>” is a parser combinator for sequential composition which keeps only the right result. `p ~> q' succeeds if 
  `p' succeeds and `q' succeeds on the input left over by `p'. 
+ The “<~” is a parser combinator for sequential composition which keeps only the left result. `p <~ q' succeeds if 
  `p' succeeds and `q' succeeds on the input left over by `p'.

## BeanProperty Annotation:

+ When attached to a field, this annotation adds a setter and a getter method following the Java Bean convention.
+ For example:
  '''scala @BeanProperty var status = “”'''

   adds the following methods to the class

  '''scala def setStatus(s: String) { this.status = s }
   def getStatus: String = this.status '''

## Exception control util:

+ use Exception object for more expcetion controll. Exception.allCatch.opt returns an option if it works. 
  If any exception occurs, None is returned.

   '''scala 
      import scala.util.control.Exception._

      allCatch.opt(17) => Some(17)
      allCatch.opt{ throw new Exception; 17 } => None
   '''

## List operations:

+ _diff_
   + Computes the multiset difference between this list and another sequence.
   + '''scala 
         scala> List(1,2,1,1,1,2,1) diff List(2)
         res13: List[Int] = List(1, 1, 1, 1, 2, 1)
      '''
+ tails VS tail 
   + tail => Selects all elements except the first
   + tails => Iterates over the tails of this list
   + '''scala 
      scala> List(1,2,3).tails
      res16: Iterator[List[Int]] = non-empty iterator
     '''
