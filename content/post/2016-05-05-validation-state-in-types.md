---
date: 2016-05-05
title: Store validation state in types
---

*Store validation state in types to avoid use of invalid data and unnecessary
revalidation.*


## Static set of values

*You have a static set of airports.* Don't store and pass them as strings, use
an `Airport` enum. Convert to and from whatever external representation there is
(might be a string) at the boundaries of your application and perform validation
there.

``` csharp
Airport ParseAirport(string s); // or throw

string PrintAirport(Airport a);
```


## Externally managed set of values

*Your set of airports is provided externally on application init.* Use an `Airport` newtype with a validating smart constructor.

``` csharp
public struct Airport
{
    private Airport(string name)
    {   Name = name;
    }

    public string Name { get; }
    
    public Airport Mk(string name)
    {   if (name is invalid airport designation)
        {   throw new Exception($"Invalid Airport {name}");
        }
        return new Airport(name);
    }
}
```


## Bounded set of values

*A bounded integer range.* Use a newtype with a smart constructor that enforces
the bounds.

``` csharp
public struct Int100
{
    private Int100(int value)
    {   Value = value;
    }

    public int Value { get; }
    
    public Int100 Mk(int x)
    {   if (x > 100 || x < -100)
        { throw new Exception($"Out of bounds {x}");
        }
        return new Int100(x);
    }
}
```
