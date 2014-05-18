# Java interoperability

```scala
val javaNull: String = System.getProperties.getProperty("meh")

val wrapIt: Option[String] = Option(javaNull)
```
