# Terse literals with `qw` and `pair-rocket`

```factor
USING: assocs grouping kernel literals qw sequences ;
IN: protein-translation

ERROR: invalid-codon ;

CONSTANT: codon-table $[ qw{
    AUG Methionine
    UUU Phenylalanine UUC Phenylalanine
    UUA Leucine       UUG Leucine
    UCU Serine        UCC Serine
    UCA Serine        UCG Serine
    UAU Tyrosine      UAC Tyrosine
    UGU Cysteine      UGC Cysteine
    UGG Tryptophan
    UAA STOP          UAG STOP          UGA STOP
} 2 group ]

: codon>protein ( codon -- protein )
    codon-table at [ invalid-codon ] unless* ;

: proteins ( strand -- proteins )
    3 group
    dup [ codon>protein "STOP" = ] find drop
    [ head ] when* [ codon>protein ] map ;
```

## The codon table with `qw`

The codon table is pure string data, and the [`qw`][qw] vocabulary — named
after Perl's quote-word syntax — is built for exactly that: inside
`qw{ ... }` every whitespace-separated token becomes a string, so
`qw{ AUG Methionine }` is `{ "AUG" "Methionine" }` without any quotes.

Writing codon and protein alternately and then applying
[`2 group`][group] turns the flat string sequence into a sequence of
`{ codon protein }` pairs — an association list that `at` searches directly.
The whole expression sits inside [`$[ ... ]`][literal-dollar] from the
`literals` vocabulary, which evaluates it once at parse time so
`CONSTANT:` still receives a plain literal value.
A linear scan over seventeen pairs is nothing; if you prefer constant-time
lookup, append [`>hashtable`][>hashtable] inside the `$[ ]`.

## The codon table with `pair-rocket`

The [`pair-rocket`][pair-rocket] vocabulary attacks the same noise from the
other side: it defines `=>` as syntax that pairs the previous literal with
the next one, so a hashtable literal reads like a table:

```factor
USING: assocs kernel grouping pair-rocket sequences ;

CONSTANT: codon-table H{
    "AUG" => "Methionine"
    "UUU" => "Phenylalanine" "UUC" => "Phenylalanine"
    "UUA" => "Leucine"       "UUG" => "Leucine"
    "UCU" => "Serine"        "UCC" => "Serine"
    "UCA" => "Serine"        "UCG" => "Serine"
    "UAU" => "Tyrosine"      "UAC" => "Tyrosine"
    "UGU" => "Cysteine"      "UGC" => "Cysteine"
    "UGG" => "Tryptophan"
    "UAA" => "STOP"          "UAG" => "STOP"
    "UGA" => "STOP"
}
```

`"AUG" => "Methionine"` parses to exactly `{ "AUG" "Methionine" }`, so this
is the same hashtable as the plain literal — minus one pair of braces per
entry, plus a visible arrow from key to value.
The keys and values are still quoted here, so `qw` saves more characters on
this particular table; `pair-rocket` shines wherever the keys or values are
not all strings.

Both vocabularies ship with Factor and are available on the Exercism test
runner.

## Translating with `find`, `head` and `map`

```factor
: proteins ( strand -- proteins )
    3 group
    dup [ codon>protein "STOP" = ] find drop
    [ head ] when* [ codon>protein ] map ;
```

`3 group` splits the strand into codons, leaving any one- or two-character
fragment as the final group.
[`find`][find] then translates codons one at a time until one yields
`"STOP"`, returning the index and the element; `drop` keeps just the index,
and [`when*`][when-star] truncates the sequence there with
[`head`][head] — or leaves it whole when no STOP codon exists and `find`
returned `f`.
A final `map` over the surviving codons produces the protein names.

The laziness of `find` is what makes this correct, not just tidy: in
`"UUCUUCUAAUGGU"` the trailing fragment `"U"` follows a STOP codon, and
`find` stops at the STOP without ever translating it.
An invalid codon *before* any STOP — `"XYZ"`, or the leftover `"U"` in
`"AUGU"` — is reached by `find` (or by the final `map`) and raises
`invalid-codon`, satisfying the error tests.
Codons before the STOP are translated twice, once while searching and once
in the `map`; with a seventeen-entry table that costs nothing and keeps the
word a pure pipeline — no locals, no mutation, no index arithmetic.

[qw]: https://docs.factorcode.org/content/vocab-qw.html
[group]: https://docs.factorcode.org/content/word-group,grouping.html
[literal-dollar]: https://docs.factorcode.org/content/word-%24%5B%2Cliterals.html
[>hashtable]: https://docs.factorcode.org/content/word-__gt__hashtable,hashtables.html
[pair-rocket]: https://docs.factorcode.org/content/vocab-pair-rocket.html
[find]: https://docs.factorcode.org/content/word-find,sequences.html
[when-star]: https://docs.factorcode.org/content/word-when__star__,kernel.html
[head]: https://docs.factorcode.org/content/word-head,sequences.html
