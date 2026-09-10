# Digit accumulator

```factor
USING: arrays grouping kernel locals math math.parser sequences
splitting.monotonic strings unicode ;
IN: run-length-encoding

: encode ( str -- encoded )
    dup empty? [ ] [
        [ = ] monotonic-split [
            dup length dup 1 > [ number>string ] [ drop "" ] if
            swap first 1string append
        ] map concat
    ] if ;

:: decode ( str -- decoded )
    "" 0 :> ( acc! n! )
    str [| ch |
        ch digit? [
            n 10 * ch CHAR: 0 - + n!
        ] [
            n 0 = [ 1 n! ] when
            acc n ch <array> >string append acc!
            0 n!
        ] if
    ] each
    acc ;
```

## Encoding

[`monotonic-split`][monotonic-split] with an `=` predicate cuts
the string wherever two neighbouring characters differ, which is precisely
its runs: `"AABCC"` becomes `{ "AA" "B" "CC" }`.
Each run then renders as its length — as a string when greater than one,
as nothing otherwise — appended to its character, and `concat` reassembles
the pieces.
The empty string is special-cased, since splitting produces no runs to
`concat` back into a string.

## Decoding, one character at a time

`decode` is a hand-rolled scanner.
Two mutable locals (note the `!` suffixes in `:> ( acc! n! )`) carry the
state: `acc` is the output built so far and `n` the numeric value of the
digits seen since the last letter.

For each character: a digit folds into the count as `n 10 * ch CHAR: 0 -
+` — the standard shift-and-add of positional notation, which handles
multi-digit counts like `"24"` for free.
Anything else ends a run: a count of zero means the character had no digits
in front of it and defaults to one, [`<array>`][array] repeats the
character `n` times, and the run is appended to `acc` before the count
resets.

The state threading is explicit and imperative — every branch must remember
to update the right local — which is exactly the bookkeeping the
[declarative approach][sequences-extras] trades away.

[monotonic-split]: https://docs.factorcode.org/content/word-monotonic-split,splitting.monotonic.html
[array]: https://docs.factorcode.org/content/word-__lt__array__gt__%2Carrays.html
[sequences-extras]: https://exercism.org/tracks/factor/exercises/run-length-encoding/approaches/sequences-extras
