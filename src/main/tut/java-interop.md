# Java interoperability

Wrap `null`-returning code in an `Option`; non-`null` creates a `Some`, `null` creates a `None`:

```scala
val nullIsEvil: String = System.getProperties.getProperty("meh")

val optionIsSooper: Option[String] = Option(nullIsEvil)
```

Wrap exception-throwing code in a `Try`:

```scala
val exceptionsAreLame: Int =
  try {
    new java.io.FileInputStream("/tmp/asdfasdfasdf").read
  } catch {
    case e: java.io.FileNotFoundException => -1
  }

import scala.util.Try

val tryIsSortOfBetter: Int =
  Try(new java.io.FileInputStream("/tmp/asdfasdfasdf").read) getOrElse -1
```
