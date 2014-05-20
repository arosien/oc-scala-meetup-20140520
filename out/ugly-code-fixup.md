# Pattern Matching

Actual [ugly code](https://github.com/twitter/zipkin/blob/master/zipkin-common/src/main/scala/com/twitter/zipkin/common/Span.scala) from [Twitter's Zipkin](https://github.com/twitter/zipkin) with some (minor) simplifying edits:

```scala
scala> import scala.concurrent.duration._
import scala.concurrent.duration._

scala> case class Endpoint(ipv4: Int, port: Short, serviceName: String)
defined class Endpoint

scala> case class Annotation(timestamp: Long, value: String, host: Option[Endpoint], duration: Option[Duration] = None) {
     |   def serviceName = host.map(_.serviceName).getOrElse("Unknown service name")
     | }
defined class Annotation

scala> // defined by me
     | val annotations: List[Annotation] =
     |   Annotation(0L, "v1", Some(Endpoint(127001, 8080, "service1"))) :: Annotation(1L, "v2", Some(Endpoint(127001, 8080, "service1"))) :: Annotation(2L, "v3", None) :: Nil
annotations: List[Annotation] = List(Annotation(0,v1,Some(Endpoint(127001,8080,service1)),None), Annotation(1,v2,Some(Endpoint(127001,8080,service1)),None), Annotation(2,v3,None,None))

scala> def clientSideAnnotations: Seq[Annotation] = {
     |   annotations.filter(a => false) // edited
     | }
clientSideAnnotations: Seq[Annotation]

scala> def serverSideAnnotations: Seq[Annotation] = {
     |   annotations.filter(a => true) // edited
     | }
serverSideAnnotations: Seq[Annotation]

scala> def serviceNames: Set[String] = {
     |   annotations.flatMap(a => a.host.map(h => h.serviceName.toLowerCase)).toSet
     | }
serviceNames: Set[String]

scala> serviceNames
res1: Set[String] = Set(service1)

scala> def serviceName: Option[String] = {
     |   if (annotations.isEmpty) {
     |     None
     |   } else {
     |     val sName = serverSideAnnotations.flatMap(_.host).headOption.map(_.serviceName)
     |     val cName = clientSideAnnotations.flatMap(_.host).headOption.map(_.serviceName)
     |     sName match {
     |       case Some(s) => Some(s)
     |       case None => cName
     |     }
     |   }
     | }
serviceName: Option[String]

scala> serviceName
res2: Option[String] = Some(service1)
```

You don't need braces for single-line functions:

```scala
scala> def clientSideAnnotations: Seq[Annotation] = annotations.filter(a => true)
clientSideAnnotations: Seq[Annotation]

scala> def serverSideAnnotations: Seq[Annotation] = annotations.filter(a => false)
serverSideAnnotations: Seq[Annotation]
```

Wow, look at the ugly:

```scala
scala> def serviceNames: Set[String] = {
     |   annotations.flatMap(a => a.host.map(h => h.serviceName.toLowerCase)).toSet
     | }
serviceNames: Set[String]

scala> serviceNames
res3: Set[String] = Set(service1)
```

for-comprehension!

```scala
scala> def serviceNames: Set[String] =
     |   (for {
     |     a <- annotations
     |     h <- a.host
     |   } yield h.serviceName.toLowerCase).toSet
serviceNames: Set[String]

scala> serviceNames
res4: Set[String] = Set(service1)
```

Or even:

```scala
scala> def serviceNames: Set[String] =
     |   for {
     |     a: Annotation <- annotations.toSet // compiler needs type annotation, boo!
     |     h <- a.host
     |   } yield h.serviceName.toLowerCase
serviceNames: Set[String]

scala> serviceNames
res5: Set[String] = Set(service1)
```
