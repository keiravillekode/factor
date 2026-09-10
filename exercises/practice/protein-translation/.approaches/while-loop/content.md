# While loop

```factor
USING: arrays assocs grouping kernel locals math sequences vectors ;
IN: protein-translation

ERROR: invalid-codon ;

CONSTANT: codon-table H{
    { "AUG" "Methionine" }
    { "UUU" "Phenylalanine" } { "UUC" "Phenylalanine" }
    { "UUA" "Leucine" } { "UUG" "Leucine" }
    { "UCU" "Serine" } { "UCC" "Serine" } { "UCA" "Serine" } { "UCG" "Serine" }
    { "UAU" "Tyrosine" } { "UAC" "Tyrosine" }
    { "UGU" "Cysteine" } { "UGC" "Cysteine" }
    { "UGG" "Tryptophan" }
    { "UAA" "STOP" } { "UAG" "STOP" } { "UGA" "STOP" }
}

: codon>protein ( codon -- protein )
    codon-table at [ invalid-codon ] unless* ;

:: proteins ( strand -- result )
    strand 3 group :> codons
    0 :> i!
    V{ } clone :> acc
    t :> going!
    [ going i codons length < and ] [
        i codons nth codon>protein
        dup "STOP" =
        [ drop f going! ]
        [ acc push i 1 + i! ] if
    ] while
    acc >array ;
```

## The codon table

The mapping from codons to proteins is fixed data, so it lives in a
[`CONSTANT:`][constant] holding a hashtable literal.
Each entry is a two-element array `{ key value }`, and the three STOP codons
all map to the sentinel string `"STOP"`, letting the translation loop treat
"is this a STOP codon?" as an ordinary string comparison.

`codon>protein` looks a codon up with [`at`][at], which returns `f` when the
key is missing.
[`unless*`][unless-star] keeps a truthy lookup result and otherwise calls the
quotation, which raises the error that [`ERROR:`][error] defined — `ERROR:
invalid-codon ;` generates both the `invalid-codon` throwing word used here
and the `invalid-codon?` predicate the tests check with `must-fail-with`.

## The scan

`proteins` is defined with [`::`][double-colon] so it can use named locals
instead of stack shuffling.
`strand 3 group` splits the strand into codons; when the length is not a
multiple of three, the final group is a leftover fragment of one or two
characters.

Three locals drive the loop, each declared mutable with a trailing `!`:

- `i` — the index of the next codon to translate,
- `acc` — a vector accumulating protein names,
- `going` — a flag that goes `f` at the first STOP codon.

The [`while`][while] combinator keeps running its body as long as `going` is
true and codons remain.
Each pass translates one codon: a `"STOP"` result flips `going` to end the
loop, anything else is pushed onto `acc`.

Stopping via the flag is what satisfies the trickiest test case: in
`"UUCUUCUAAUGGU"` the trailing fragment `"U"` comes after a STOP codon, so the
loop ends before ever looking it up.
An invalid codon *before* any STOP, as in `"AUGU"`, still reaches
`codon>protein` and raises `invalid-codon`, exactly as the error tests demand.

Finally `>array` converts the accumulator, since the tests compare against
array literals.

[constant]: https://docs.factorcode.org/content/word-CONSTANT__colon__,syntax.html
[at]: https://docs.factorcode.org/content/word-at,assocs.html
[unless-star]: https://docs.factorcode.org/content/word-unless__star__,kernel.html
[error]: https://docs.factorcode.org/content/word-ERROR__colon__,syntax.html
[double-colon]: https://docs.factorcode.org/content/word-__colon____colon__,locals.html
[while]: https://docs.factorcode.org/content/word-while,combinators.html
