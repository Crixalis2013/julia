# Compat Package for Julia

[![Build Status](https://travis-ci.org/JuliaLang/Compat.jl.svg?branch=master)](https://travis-ci.org/JuliaLang/Compat.jl)

The **Compat** package is designed to ease interoperability between
older and newer versions of the [Julia
language](http://julialang.org/).  In particular, in cases where it is
impossible to write code that works with both the latest Julia
`master` branch and older Julia versions, or impossible to write code
that doesn't generate a deprecation warning in some Julia version, the
Compat package provides a macro that lets you use the *latest syntax*
in a backwards-compatible way.

This is primarily intended for use by other [Julia
packages](http://docs.julialang.org/en/latest/manual/packages/), where
it is important to maintain cross-version compatibility.

## Usage

To use Compat in your Julia package, add a line `Compat` to the
`REQUIRE` file in your package directory.  Then, in your package,
after a `using Compat` statement to load Compat, simply use:

```
@compat ...Julia master syntax...
```

wherever you want to use syntax that differs in the latest Julia
`master` (the development version of Julia).

## Supported syntax

Currently, the `@compat` macro supports the following syntaxes:

* `@compat Dict(foo => bar, baz => qux)` - type-inferred `Dict` construction.

* `@compat Dict{Foo,Bar}(foo => bar, baz => qux)` - type-declared `Dict` construction.

* `@compat split(str, splitter; keywords...)` - the Julia 0.4-style keyword-based `split` function

* `@compat rsplit(str, splitter; keywords...)` - the Julia 0.4-style keyword-based `rsplit` function

## Type Aliases

* `typealias AbstractString String` - `String` has been renamed to `AbstractString` [#8872](https://github.com/JuliaLang/julia/pull/8872)

* For all unsigned integer types to their equivalents with uppercase `I`. [#8907](https://github.com/JuliaLang/julia/pull/8907)

## Renamed functions

* `itrunc`, `iround`, `iceil`, `ifloor` are now accessed via `trunc(T, x)`, etc. [#9133](https://github.com/JuliaLang/julia/pull/9133)

* `Base.Random.randmtzig_exprnd` is now `randexp` [#9144](https://github.com/JuliaLang/julia/pull/9144)

* `sizehint` is now `sizehint!` [#9278](https://github.com/JuliaLang/julia/pull/9278)

## New macros

* `@inline` and `@noinline` have been added. On 0.3, these are "no-ops," meaning they don't actually do anything.

## Other syntax changes

* `Dict(ks, vs)` is now `Dict(zip(ks, vs))` [#8521](https://github.com/JuliaLang/julia/pull/8521)

## Developer tips

If you're adding additional compatibility code to this package, the following shell script is a useful for extracting the version number from a git commit SHA:

```sh
#! /bin/bash
last_tag=$(git describe --tags --abbrev=0)
git rev-list $1 ^$last_tag | wc -l | sed -e 's/[^[:digit:]]//g'
```
This will print a number `XXXX`, and you can then test whether Julia
is at least this version by `VERSION >= v"0.4.0-dev+XXXX"` (assuming
it is a commit from the 0.4 development cycle).
