# Introduction

Complex Numbers is pure arithmetic: a `cmplx` tuple holds a real and an
imaginary part, and every operation unpacks the parts, applies a textbook
formula, and packs the result back into a tuple.
The scaffolding — the tuple, `<cmplx>`, `>cmplx`, `cmplx>pair` — is the same
in every solution.

What varies is how the formulas themselves are written.
Multiplication and division are where it shows: four operands flowing through
a formula like `(a*c + b*d) / (c*c + d*d)` are genuinely awkward to juggle on
the stack, so both approaches below reach for named values — they differ in
the notation the formula is then written in.

## Approach: postfix arithmetic with locals

```factor
:: c* ( a b -- c )
    a real>> b real>> * a imaginary>> b imaginary>> * -
    a real>> b imaginary>> * a imaginary>> b real>> * +
    <cmplx> ;
```

Factor's native notation: name the operands with `::` locals and write each
formula in postfix, operators after their operands.
[Read more about the postfix approach][postfix-locals].

## Approach: infix arithmetic

```factor
:: c* ( x y -- z )
    x y [ parts ] bi@ :> ( a b c d )
    [infix a*c - b*d infix] [infix a*d + b*c infix] <cmplx> ;
```

The `infix` vocabulary embeds ordinary mathematical notation in a Factor
word: everything between `[infix` and `infix]` is parsed as a conventional
expression over the locals in scope.
[Read more about the infix approach][infix].

## Which approach to use?

Postfix with locals is idiomatic Factor and needs no extra vocabulary; once
the operands are named, the RPN formulas are unambiguous, if unfamiliar to
newcomers.

The infix version matches how the mathematics is written on paper — the
division formula reads exactly like the textbook — at the cost of pulling in
a syntax extension and unpacking the tuples into scalars first.
It is also a fine showcase of Factor's parsing words: notation itself is
library code.
The `infix` vocabulary ships with Factor and is available on the Exercism
test runner.

[postfix-locals]: https://exercism.org/tracks/factor/exercises/complex-numbers/approaches/postfix-locals
[infix]: https://exercism.org/tracks/factor/exercises/complex-numbers/approaches/infix
