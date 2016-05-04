---
date: 2016-01-26
title: My kingdom for a build system
---

The DNX based projects I've created all use a simple Powershell script for
building. It is only 44 lines long with a function for each build phase.

The script amounts to a thin wrapper over dnx and dnu commands which applies
them to (potentially) more than one project. The script requires a conventional
project layout.

It's simple.

It works.

I don't like it.


## Powershell is painful

**Return semantics.** Powershell functions return everything as an ordered
collection piped to stdout and smudge the difference between values and
single-value collections.

``` powershell
function InterestingReturnSemantics() {
    1
    "two"
    @{buckle = "My Shoe"}
}
  
# => [1, "two", @{buckle = "My Shoe"}]
```

**Types.** Type annotations are permitted, but operate as locations for
type-coercion rather than type-safety.

**Versions.** Scripts written for v5 may not work on v4 and below, but I can't
test that because parallel installation is not supported.

*Powershell is imprecise and unsafe. I'm uncomfortable using it long term for a
non-throwaway task. Here be dragons and dynamic-typing.*


## The script is not a real build system

IMO the basics of a build system are:
1. transitive dependency between phases
2. determinism and therefore repeatability
3. change detection so phases are not triggered unnecessarily

The script does none of these. Dependency is trivial, but without change
detection it results in phase repetition. In fact, dependency without the
determinism or change detection is simply a bunch of functions calling each
other.


## .NET build systems don't cut the mustard

Bau (C#), Cake (C#), and Fake (F#) have some nice bells and whistles like
automatic import of required NuGet packages and plenty of plugins, but they only
provide #1.

I rewrote the build script with Cake but reverted to Powershell because of a
[Powershell integration problem][p] and because the experience was not pleasant.
Cake does some strange things with global scope and its plugin system is simply
bizarre.

[p]: https://github.com/tflite/TakeFlite.Storage/commit/f5d1b7ed5f82eb2cafab2336b1579ff7e788eb29


## System status

For now I have reverted back to using a Powershell script because it works and
is still small enough to be easily comprehended and fixed as necessary.

WRT the future I have begun work on a csi / scriptcs based tool that will
provide the basics. I'm also investigating how easy it would be to add change
detection to one of the existing tools.

*Dear tools, walk then run.*
