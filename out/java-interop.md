# Java interoperability

```scala
scala> val javaNull: String = System.getProperties.getProperty("meh")
javaNull: String = null

scala> val wrapIt: Option[String] = Option(javaNull)
wrapIt: Option[String] = None
```
