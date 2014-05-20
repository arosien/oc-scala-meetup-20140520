# Pattern Matching

Actual [ugly code](https://github.com/twitter/zipkin/blob/master/zipkin-common/src/main/scala/com/twitter/zipkin/common/Span.scala) from [Twitter's Zipkin](https://github.com/twitter/zipkin) with some (minor) simplifying edits:

```scala
import scala.concurrent.duration._

case class Endpoint(ipv4: Int, port: Short, serviceName: String)
case class Annotation(timestamp: Long, value: String, host: Option[Endpoint], duration: Option[Duration] = None) {
  def serviceName = host.map(_.serviceName).getOrElse("Unknown service name")
}

// defined by me
val annotations: List[Annotation] =
  Annotation(0L, "v1", Some(Endpoint(127001, 8080, "service1"))) :: Annotation(1L, "v2", Some(Endpoint(127001, 8080, "service1"))) :: Annotation(2L, "v3", None) :: Nil

def clientSideAnnotations: Seq[Annotation] = {
  annotations.filter(a => false) // edited
}

def serverSideAnnotations: Seq[Annotation] = {
  annotations.filter(a => true) // edited
}

def serviceNames: Set[String] = {
  annotations.flatMap(a => a.host.map(h => h.serviceName.toLowerCase)).toSet
}

serviceNames

def serviceName: Option[String] = {
  if (annotations.isEmpty) {
    None
  } else {
    val sName = serverSideAnnotations.flatMap(_.host).headOption.map(_.serviceName)
    val cName = clientSideAnnotations.flatMap(_.host).headOption.map(_.serviceName)
    sName match {
      case Some(s) => Some(s)
      case None => cName
    }
  }
}

serviceName
```

You don't need braces for single-line functions:

```scala
def clientSideAnnotations: Seq[Annotation] = annotations.filter(a => true)
def serverSideAnnotations: Seq[Annotation] = annotations.filter(a => false)
```

Wow, look at the ugly:

```scala
def serviceNames: Set[String] = {
  annotations.flatMap(a => a.host.map(h => h.serviceName.toLowerCase)).toSet
}

serviceNames
```

for-comprehension!

```scala
def serviceNames: Set[String] =
  (for {
    a <- annotations
    h <- a.host
  } yield h.serviceName.toLowerCase).toSet

serviceNames
```

Or even:

```scala
def serviceNames: Set[String] =
  for {
    a: Annotation <- annotations.toSet // compiler needs type annotation, boo!
    h <- a.host
  } yield h.serviceName.toLowerCase

serviceNames
```