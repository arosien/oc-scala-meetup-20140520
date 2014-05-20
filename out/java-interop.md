# Java interoperability

Wrap `null`-returning code in an `Option`; non-`null` creates a `Some`, `null` creates a `None`:

```scala
scala> val nullIsEvil: String = System.getProperties.getProperty("meh")
nullIsEvil: String = null

scala> val optionIsSooper: Option[String] = Option(nullIsEvil)
optionIsSooper: Option[String] = None
```

Wrap exception-throwing code in a `Try`:

```scala
scala> val exceptionsAreLame: Int =
     |   try {
     |     new java.io.FileInputStream("/tmp/asdfasdfasdf").read
     |   } catch {
     |     case e: java.io.FileNotFoundException => -1
     |   }
exceptionsAreLame: Int = -1

scala> import scala.util.Try
import scala.util.Try

scala> val tryIsSortOfBetter: Int =
     |   Try(new java.io.FileInputStream("/tmp/asdfasdfasdf").read) getOrElse -1
tryIsSortOfBetter: Int = -1
```
