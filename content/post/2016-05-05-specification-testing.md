---
date: 2016-05-05 08:22:00+12:00
title: Specification testing
---

Xunit has Theories, which enable a single test to be run over a set of data.
Wouldn't it be great of the data could be auto-generated?

Assume a function `Func<A, B>`. If we know how to create values of type `A`, and
one or more invariants exhibited by the `A -> B` relation, we have the
foundations of a specification test.


## A specification for list reverse

- Reverse does not affect list length.
- Reverse does not affect list membership.
- Reversing twice yields the input list.

The specification in functions.

``` csharp
bool ReverseKeepsLength(List<A> list)
=>  list.Length == Reverse(list).Length;

bool ReverseKeepsMembership(List<A> list)
{
    var rev = Reverse(list);
    for (var x in list)
    {
        if (!rev.Contains(x)) return false;
    }
    return true;
}

bool ReverseReverseIsIdentity(List<A> list)
=>  list.SequenceEqual(Reverse(Reverse(list)));
```

Now all we need is a means of generating random, yet deterministic, list values.
Random, so a full spread of lists is checked. Deterministic, so if a test fails
we have a way to repeat it with the same input value.


## Testing the reverse specification with FsCheck and Xunit

FsCheck provides value generators and a means of running a specification test
multiple times with different input values.

``` csharp
using Xunit;
using FsCheck;

public static class ReverseTests
{
    [Fact]
    public void ReverseKeepsLengthTest()
    =>  Prop.For(Arb.Default.List(), ReverseKeepsLength).QuickCheck();
    
    [Fact]
    public void ReverseKeepsMembershipTest()
    =>  Prop.For(Arb.Default.List(), ReverseKeepsMembership).QuickCheck();
    
    [Fact]
    public void ReverseReverseIsIdentityTest()
    =>  Prop.For(Arb.Default.List(), ReverseReverseIsIdentity).QuickCheck();
}
```

FsCheck provides a limited number of generators under `Arb.Default`. The rest we have to write ourselves.
