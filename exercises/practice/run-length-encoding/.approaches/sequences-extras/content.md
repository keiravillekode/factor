# Run at a time with sequences.extras

```factor
USING: grouping kernel locals math math.parser sequences
sequences.extras splitting.monotonic strings unicode ;
IN: run-length-encoding

: encode ( str -- encoded )
    dup empty? [ ] [
        [ = ] monotonic-split [
            dup length dup 1 > [ number>string ] [ drop "" ] if
            swap first 1string append
        ] map concat
    ] if ;

:: next-run ( str -- rest run )
    str [ digit? not ] cut-when :> ( digits more )
    more unclip-slice :> ( rest ch )
    rest digits >string string>number 1 or ch <string> ;

: decode ( str -- decoded )
    [ dup empty? not ] [ next-run ] produce nip "" concat-as ;
```

## Encoding

Encoding is shared with the [digit accumulator approach][digit-accumulator]:
[`monotonic-split`][monotonic-split] cuts the string into runs of equal
characters, and each run renders as its length (omitted when one) plus its
character.

## Decoding, one run at a time

The encoded string is a sequence of runs — *optional digits, then one
character* — and this decoder consumes exactly one run per step instead of
one character.

`next-run` slices a run off the front.
[`cut-when`][cut-when], from the [`sequences.extras`][sequences.extras]
vocabulary, splits the string at the first element satisfying the
predicate: `[ digit? not ]` puts the leading digits (possibly none) in one
slice and everything from the run's character onward in the other.
[`unclip-slice`][unclip-slice] then peels that character off, leaving the
rest of the input for the next round.
The count is `string>number` of the digit slice, with
`1 or` supplying the default when there were no digits — `string>number`
returns `f` for an empty string — and [`<string>`][make-string] expands
count and character into the decoded run.

`decode` drives it with [`produce`][produce]: *while the remaining input is
non-empty, call `next-run` and collect its output*.
The threading of the shrinking input happens on the stack, the collecting
of results inside `produce` — no mutable locals, no manual state resets.
A final [`concat-as`][concat-as] joins the collected runs, with the string
exemplar making the empty case come out as `""` rather than `{ }`.

Everything here is slices over the original string; nothing is copied until
the final concatenation.

The `sequences.extras` vocabulary ships with Factor and is available on the
Exercism test runner.

[digit-accumulator]: https://exercism.org/tracks/factor/exercises/run-length-encoding/approaches/digit-accumulator
[monotonic-split]: https://docs.factorcode.org/content/word-monotonic-split,splitting.monotonic.html
[sequences.extras]: https://docs.factorcode.org/content/vocab-sequences.extras.html
[cut-when]: https://docs.factorcode.org/content/word-cut-when,sequences.extras.html
[unclip-slice]: https://docs.factorcode.org/content/word-unclip-slice,sequences.html
[make-string]: https://docs.factorcode.org/content/word-__lt__string__gt__%2Cstrings.html
[produce]: https://docs.factorcode.org/content/word-produce,sequences.html
[concat-as]: https://docs.factorcode.org/content/word-concat-as,sequences.html
