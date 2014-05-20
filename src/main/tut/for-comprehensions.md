# for-comprehensions

## Breakdown of syntactic sugar

A single expression without `yield` translates to `foreach`:

```scala
for {
  x <- Some(12)
} println(x)

Some(12) foreach println
```

A single expression *with* `yield` translates to `map`:

```scala
for {
  x <- Some(12)
} yield x + 1

Some(12) map (_ + 1)
```

Multiple expressions translate to nested `flatMap`s, with the last one translated to `map`:

```scala
for {
  x <- Some(12)
  y <- Some(2)
} yield x * y

Some(12) flatMap (x => Some(2) map (x * _))
```

Conditionals are translated to `filter`:

```scala
for {
  x <- Some(12) if x < 10
} yield x + 1

Some(12) filter (_ < 10) map (_ + 1)
```

Intermediate values are translated to two `map`s with a tuple:

```scala
for {
  x <- Some(12)
  y = x + 1
} yield y

Some(12) map (x => (x, x + 1)) map { case (x, y) => y }
```