# Nested loops with set-at

```factor
USING: assocs hashtables kernel locals sequences strings unicode ;
IN: etl

:: transform ( legacy -- new )
    H{ } clone :> result
    legacy [| score letters |
        letters [| letter |
            score letter >lower result set-at
        ] each
    ] assoc-each
    result ;
```

## The shape of the data

The legacy format maps one score to a sequence of uppercase letters,
`H{ { 1 { "A" "E" } } }`; the new format maps each lowercase letter to its
score, `H{ { "a" 1 } { "e" 1 } }`.
The transformation therefore has to visit every letter of every entry —
a doubly nested loop — and build a new, inverted table along the way.

## Building the table by mutation

The word is defined with [`::`][double-colon] so the input and the
accumulator can be named.
`H{ } clone :> result` binds a fresh hashtable — the [`clone`][clone] matters,
since `H{ }` alone is one shared literal, and mutating it would leak state
between calls.

[`assoc-each`][assoc-each] walks the legacy table one key/value pair at a
time.
The quotation names its two parameters with the `[| score letters | ... ]`
syntax, and the inner [`each`][each] does the same for the individual
letters.
For every letter, [`set-at`][set-at] stores one entry: the score as value,
[`>lower`][>lower] of the letter as key.

After the loops finish, `result` holds the completed table and is the return
value.
The mutation never escapes the word: the hashtable is created, filled and
returned inside one definition, so from the outside `transform` is a pure
function.

[double-colon]: https://docs.factorcode.org/content/word-__colon____colon__,locals.html
[clone]: https://docs.factorcode.org/content/word-clone,kernel.html
[assoc-each]: https://docs.factorcode.org/content/word-assoc-each,assocs.html
[each]: https://docs.factorcode.org/content/word-each,sequences.html
[set-at]: https://docs.factorcode.org/content/word-set-at,assocs.html
[>lower]: https://docs.factorcode.org/content/word-__gt__lower,unicode.html
