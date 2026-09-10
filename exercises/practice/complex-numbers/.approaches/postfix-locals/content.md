# Postfix arithmetic with locals

```factor
USING: accessors arrays kernel locals math math.functions
sequences ;
IN: complex-numbers

TUPLE: cmplx real imaginary ;

: <cmplx> ( real imag -- cmplx ) cmplx boa ;

: >cmplx ( pair -- cmplx ) first2 <cmplx> ;

: cmplx>pair ( cmplx -- pair )
    [ real>> ] [ imaginary>> ] bi 2array ;

:: c+ ( a b -- c )
    a real>> b real>> +
    a imaginary>> b imaginary>> +
    <cmplx> ;

:: c- ( a b -- c )
    a real>> b real>> -
    a imaginary>> b imaginary>> -
    <cmplx> ;

:: c* ( a b -- c )
    a real>> b real>> * a imaginary>> b imaginary>> * -
    a real>> b imaginary>> * a imaginary>> b real>> * +
    <cmplx> ;

:: c/ ( a b -- c )
    b real>> sq b imaginary>> sq + :> denom
    a real>> b real>> * a imaginary>> b imaginary>> * + denom /
    a imaginary>> b real>> * a real>> b imaginary>> * - denom /
    <cmplx> ;

: c-abs ( a -- |a| )
    [ real>> sq ] [ imaginary>> sq ] bi + sqrt ;

: c-conj ( a -- a* )
    [ real>> ] [ imaginary>> neg ] bi <cmplx> ;

:: c-exp ( z -- e^z )
    z real>> e^ :> ea
    z imaginary>> :> b
    ea b cos *
    ea b sin *
    <cmplx> ;
```

## Representation

[`TUPLE:`][tuple] declares a class with `real` and `imaginary` slots, and
[`boa`][boa] ("by order of arguments") fills them from the stack.
`>cmplx` and `cmplx>pair` convert between the tuple and the two-element
arrays the tests use.

## Named operands, postfix formulas

The binary operations take two complex numbers — four scalar operands once
unpacked — and that is more than comfortably fits Factor's stack-shuffling
words.
Defining the words with [`::`][double-colon] names the inputs, so each
formula can mention `a real>>`, `b imaginary>>` and so on directly, in any
order, as many times as needed.

Each formula is then ordinary postfix Factor.
Multiplication, `(a + bi)(c + di) = (ac − bd) + (ad + bc)i`, becomes two
lines that each compute one part, leaving both on the stack for `<cmplx>`:

```factor
    a real>> b real>> * a imaginary>> b imaginary>> * -
    a real>> b imaginary>> * a imaginary>> b real>> * +
```

Division needs the shared denominator `c² + d²` twice, so it is computed
once and bound to a local with [`:>`][bind-local] before the two numerator
lines divide by it.

## Where locals are not needed

Unary operations touch only one number, and a [`bi`][bi] over two accessor
quotations handles them without any locals: `c-abs` squares both parts, sums
and takes the [`sqrt`][sqrt]; `c-conj` negates only the imaginary part.

`c-exp` uses Euler's formula `e^(a+bi) = e^a·(cos b + i·sin b)`: `e^` of the
real part is bound once, then multiplied by `cos` and `sin` of the imaginary
part.

[tuple]: https://docs.factorcode.org/content/word-TUPLE__colon__,syntax.html
[boa]: https://docs.factorcode.org/content/word-boa,classes.tuple.html
[double-colon]: https://docs.factorcode.org/content/word-__colon____colon__,locals.html
[bind-local]: https://docs.factorcode.org/content/word-__colon____gt__%2Clocals.html
[bi]: https://docs.factorcode.org/content/word-bi,kernel.html
[sqrt]: https://docs.factorcode.org/content/word-sqrt,math.functions.html
