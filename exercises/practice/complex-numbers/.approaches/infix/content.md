# Infix arithmetic

```factor
USING: accessors arrays infix kernel locals math math.functions
sequences ;
IN: complex-numbers

TUPLE: cmplx real imaginary ;

: <cmplx> ( real imag -- cmplx ) cmplx boa ;

: >cmplx ( pair -- cmplx ) first2 <cmplx> ;

: cmplx>pair ( cmplx -- pair )
    [ real>> ] [ imaginary>> ] bi 2array ;

: parts ( z -- re im ) [ real>> ] [ imaginary>> ] bi ;

:: c+ ( x y -- z )
    x y [ parts ] bi@ :> ( a b c d )
    [infix a+c infix] [infix b+d infix] <cmplx> ;

:: c- ( x y -- z )
    x y [ parts ] bi@ :> ( a b c d )
    [infix a-c infix] [infix b-d infix] <cmplx> ;

:: c* ( x y -- z )
    x y [ parts ] bi@ :> ( a b c d )
    [infix a*c - b*d infix] [infix a*d + b*c infix] <cmplx> ;

:: c/ ( x y -- z )
    x y [ parts ] bi@ :> ( a b c d )
    [infix (a*c + b*d) / (c*c + d*d) infix]
    [infix (b*c - a*d) / (c*c + d*d) infix] <cmplx> ;

:: c-abs ( z -- |z| )
    z parts :> ( a b )
    [infix sqrt(a*a + b*b) infix] ;

:: c-conj ( z -- z* )
    z parts :> ( a b )
    a [infix -b infix] <cmplx> ;

:: c-exp ( z -- e^z )
    z parts :> ( a b )
    a e^ :> ea
    [infix ea*cos(b) infix] [infix ea*sin(b) infix] <cmplx> ;
```

## Notation as a library

Factor's postfix notation is a poor match for formulas like
`(ac + bd) / (c² + d²)` — precisely the place where stack shuffling hurts
most.
The [`infix`][infix] vocabulary fixes that with a pair of parsing words:
everything between [`[infix`][infix-bracket] and `infix]` is parsed as a
conventional mathematical expression and compiled into the surrounding
word.
Inside an expression you get the usual operators `+ - * / ^` with their
familiar precedence, parentheses, unary minus (`-b` in `c-conj`), and
function-call syntax — `sqrt(a*a + b*b)`, `cos(b)` — which invokes the
Factor word of that name.

The operands are the [`::`][double-colon] locals in scope.
Each operation therefore starts by unpacking both tuples: `parts` turns one
complex number into its two scalar components, `[ parts ] bi@` does it for
both, and `:> ( a b c d )` binds all four values in one
[multiple-binding][bind-local] — `x = a + bi`, `y = c + di`.
After that, every formula reads exactly as the maths textbook writes it,
and the two results feed `<cmplx>` as usual.

## Limits of the notation

Infix expressions work on scalars, not tuples, which is why the unpacking
step exists at all — there is no `x.real` syntax inside `[infix`.
Word names containing operator characters are also out of reach: `e^`
cannot be called inside an expression, so `c-exp` computes `a e^` in
postfix, binds it to `ea`, and only then switches to infix for
`ea*cos(b)` and `ea*sin(b)`.

For words whose inputs are already scalars, the vocabulary also offers
[`INFIX::`][INFIX], which defines an entire word from one infix
expression:

```factor
INFIX:: hypot ( a b -- h ) sqrt(a*a + b*b) ;
```

The tuple-unpacking in this exercise keeps `[infix ... infix]` the better
fit here.

Like every vocabulary in this approach, `infix` ships with Factor and is
available on the Exercism test runner.

[infix]: https://docs.factorcode.org/content/vocab-infix.html
[infix-bracket]: https://docs.factorcode.org/content/word-%5Binfix%2Cinfix.html
[INFIX]: https://docs.factorcode.org/content/word-INFIX__colon____colon__%2Cinfix.html
[double-colon]: https://docs.factorcode.org/content/word-__colon____colon__,locals.html
[bind-local]: https://docs.factorcode.org/content/word-__colon____gt__%2Clocals.html
