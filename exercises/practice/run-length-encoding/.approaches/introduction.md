# Introduction

Run-length encoding has an easy half and a fiddly half.
Encoding falls out of one library word: `monotonic-split` cuts the input
into runs of equal characters, and each run prints as its length and
character — both approaches share this encoder.

Decoding is where solutions differ.
The encoded form interleaves optional multi-digit counts with characters,
so the decoder has to carry state — *what number have I read so far?* —
across characters, and the two approaches carry it very differently.

## Approach: digit accumulator

```factor
    str [| ch |
        ch digit? [ n 10 * ch CHAR: 0 - + n! ]
        [ ... acc n ch <array> >string append acc!  0 n! ] if
    ] each
```

Scan character by character, folding digits into a mutable count and
flushing a repeated character whenever a non-digit arrives.
[Read more about the digit accumulator approach][digit-accumulator].

## Approach: run at a time with `sequences.extras`

```factor
:: next-run ( str -- rest run )
    str [ digit? not ] cut-when :> ( digits more )
    more unclip-slice :> ( rest ch )
    rest digits >string string>number 1 or ch <string> ;
```

Treat the input as a sequence of runs instead: `cut-when` slices the digits
off the front, `unclip-slice` takes the character, and `produce` collects
one decoded run per iteration until the input is exhausted.
[Read more about the sequences.extras approach][sequences-extras].

## Which approach to use?

The digit accumulator is self-contained and allocation-light, but every
branch must update the right local at the right time — state bugs hide well
in such code.

The run-at-a-time version matches the grammar of the data: each helper
consumes one syntactic unit, state lives on the stack, and `cut-when` and
`produce` from `sequences.extras` (shipped with Factor, available on the
test runner) replace the hand-rolled scanning.

[digit-accumulator]: https://exercism.org/tracks/factor/exercises/run-length-encoding/approaches/digit-accumulator
[sequences-extras]: https://exercism.org/tracks/factor/exercises/run-length-encoding/approaches/sequences-extras
