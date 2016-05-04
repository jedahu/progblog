---
date: 2016-05-04 17:19:00+12:00
title: Xunit deps redux
---

## Class with dependency

```csharp
public sealed class CookieJar
{
    private int count;
    private ILogger logger;
    
    public CookieJar(int cookies, ILogger logger)
    {
        count = cookies;
        this.logger = logger;
    }
    
    public void EatCookie(string who)
    {
        if (count == 0)
        {
            var msg = "is out of luck!";
            logger.Log(who, msg);
            throw new Exception($"{who} {msg}");
        }
        count -= 1;
        logger.Log(who, "ate a cookie");
    }
}
```


## Dependency interface

``` csharp
public interface ILogger
{
    Log(string who, string msg);
}
```


## Implementation for production

``` csharp
using System.IO;

public class Logger
  : ILogger
{
    private readonly TextWriter writer;
    
    public Logger(TextWriter writer)
    {
        this.writer = writer;
    }
    
    public void Log(string who, string msg)
    =>  writer.Write($"{who} {msg}");
}
```


## Implementation for testing

``` csharp
public class TestLogger
  : ILogger
{
    public List<string> Names { get; } = new List<string>();
    
    public void Log(string who, string msg)
    =>  list.Add(who);
}
```


## Tests

``` csharp
using Xunit;
using System.IO;

public static class CookieJarTests
{
    [Fact]
    public static void CookieEaterIsLogged()
    {
        // Arrange
        var logger = new TestLogger();
        var jar = new CookieJar(10, logger);
        var name = "Bob";
        
        // Act
        jar.Eat(name);
        
        // Assert
        Assert.Equal(name, logger.Names[0]);
    }
}
```

Look Mum, no string operations!
