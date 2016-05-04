---
date: 2016-05-04
title: Xunit init
---

## Class to test.

``` csharp
public struct PrimOps
{
    public int Sum(int a, int b)
    =>  a + b;
}
```


## Tests

``` csharp
using Xunit;

public static class TestPrimOps
{
```

Use `Fact` for smoke tests.

``` csharp
[Fact]
public static void SumSanityCheck()
=>  Assert.Equal(5, default(PrimOps).Sum(3, 2))
```

Use `Theory` for invariants.

``` csharp
[Theory]
[MemberData("EvenOddArgs")]
public static void SumEvenOddIsOdd(int a, int b)
=>  Assert.True(default(PrimOps).Sum(a, b) % 2 == 1);

public static IEnumerable<int[]> EvenOddArgs()
{
    for (var i = 0; i < 100; i += 1)
    {
        yield return new[] {i, i + 1};
    }
}
```

``` csharp
}
```
