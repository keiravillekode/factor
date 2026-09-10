# Modular arithmetic

```factor
USING: combinators fry kernel math math.order sequences ;
IN: rotational-cipher

: shift-from ( base ch shift -- ch' )
    swap pick - + 26 mod + ;

: shift-char ( ch shift -- ch' )
    {
        { [ over CHAR: a CHAR: z between? ] [ [ CHAR: a ] 2dip shift-from ] }
        { [ over CHAR: A CHAR: Z between? ] [ [ CHAR: A ] 2dip shift-from ] }
        [ drop ]
    } cond ;

: rotate ( text shift -- cipher )
    '[ _ shift-char ] map ;
```

## Rotating one character

A rotational cipher is arithmetic on character codes: subtract the code of
the alphabet's first letter, add the shift, wrap with `26 mod`, and add the
base back.
`shift-from` is exactly that formula; `CHAR: a` is the [`CHAR:`][char]
syntax for a character literal, and the base it denotes makes the same word
serve both cases.

`shift-char` classifies the character with [`cond`][cond]: a code
[`between?`][between] `CHAR: a` and `CHAR: z` is shifted from the lowercase
base, one between `CHAR: A` and `CHAR: Z` from the uppercase base, and
every other character falls through to `[ drop ]`, which discards the shift
and leaves the character untouched — numbers, punctuation and spaces pass
straight through.

## Rotating the string

Strings are sequences of character codes, so `rotate` is a plain
[`map`][map].
The [fry][fry] quotation `'[ _ shift-char ]` slots the shift into the
quotation once, giving each character a `( ch -- ch' )` transformation, and
`map` over a string yields a string.

[char]: https://docs.factorcode.org/content/word-CHAR__colon__,syntax.html
[cond]: https://docs.factorcode.org/content/word-cond,combinators.html
[between]: https://docs.factorcode.org/content/word-between__que__,math.order.html
[map]: https://docs.factorcode.org/content/word-map,sequences.html
[fry]: https://docs.factorcode.org/content/vocab-fry.html
