# for-comprehensions

A single expression without `yield` translates to `foreach`:

```scala
scala> for {
     |   x <- Some(12)
     | } println(x)

scala> Some(12) foreach println
```

A single expression *with* `yield` translates to `map`:

```scala
scala> for {
     |   x <- Some(12)
     | } yield x + 1
res2: Option[Int] = Some(13)

scala> Some(12) map (_ + 1)
res3: Option[Int] = Some(13)
```

Multiple expressions translate to `flatMap`, with the last one translated to `map`:

```scala
scala> for {
     |   x <- Some(12)
     |   y <- Some(2)
     | } yield x * y
res4: Option[Int] = Some(24)

scala> Some(12) flatMap (x => Some(2) map (x * _))
res5: Option[Int] = Some(24)
```

Conditionals are translated to `filter`:

```scala
scala> for {
     |   x <- Some(12) if x < 10
     | } yield x + 1
res6: Option[Int] = None

scala> Some(12) filter (_ < 10) map (_ + 1)
res7: Option[Int] = None
```

Intermediate values are translated to two `map`s with a tuple:

```scala
scala> for {
     |   x <- Some(12)
     |   y = x + 1
     | } yield y
res8: Option[Int] = Some(13)

scala> Some(12) map (x => (x, x + 1)) map { case (x, y) => y }
res9: Option[Int] = Some(13)
```
