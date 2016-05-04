---
date: 2016-05-04 17:01:00+12:00
title: Xunit deps
---

Don't do this. The next post is better.

## Class with dependency

```csharp
public sealed class CookieJar
{
    private int count;
    private Logger logger;
    
    public CookieJar(int cookies, Logger logger)
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


## Dependency

``` csharp
using System.IO;

public class Logger
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
        var writer = new StringWriter();
        var logger = new Logger(writer);
        var jar = new CookieJar(10, logger);
        var name = "Bob";
        
        // Act
        jar.Eat(name);
        writer.Flush();
        
        // Assert
        Assert.True(writer.GetStringBuilder().ToString().Contains(name));
    }
}
```
