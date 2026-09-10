# Introduction

Every solution to Protein Translation has to do the same two things: split the
RNA strand into three-letter codons and map each codon to its protein, and stop
translating at the first STOP codon.
Stopping matters more than it first appears: a strand may carry anything after a
STOP codon — even an incomplete codon — and that data must never be inspected.

Beyond that, the codon-to-protein mapping is a fixed table of string constants,
so a second, more Factor-specific question is how to write that table down with
the least ceremony.

## Approach: while loop

```factor
:: proteins ( strand -- result )
    strand 3 group :> codons
    0 :> i!  V{ } clone :> acc  t :> going!
    [ going i codons length < and ] [
        i codons nth codon>protein
        dup "STOP" = [ drop f going! ] [ acc push i 1 + i! ] if
    ] while
    acc >array ;
```

An explicit scan: walk the codons with an index, push translations onto a
mutable accumulator, and flip a flag at the first STOP codon.
[Read more about the while loop approach][while-loop].

## Approach: terse literals with `qw` and `pair-rocket`

```factor
CONSTANT: codon-table $[ qw{
    AUG Methionine     UUU Phenylalanine  UUC Phenylalanine
    UGG Tryptophan     UAA STOP           UAG STOP
} 2 group ]                                        ! abridged

: proteins ( strand -- proteins )
    3 group dup [ codon>protein "STOP" = ] find drop
    [ head ] when* [ codon>protein ] map ;
```

A declarative pipeline: `find` the first STOP codon, cut the sequence there,
and `map` what remains — paired with the `qw` or `pair-rocket` vocabulary to
write the codon table with far fewer quotes and braces.
[Read more about the terse literals approach][qw-pair-rocket].

## Which approach to use?

The while loop reads like imperative pseudocode, so it is the natural first
solution for anyone arriving from an imperative language, and it makes the
"stop early" requirement completely explicit.

The pipeline version is more idiomatic Factor: shorter, free of mutation and
index bookkeeping, and built from stock combinators.
Its `qw` and `pair-rocket` table literals also showcase how Factor keeps
data-heavy constants terse — both vocabularies ship with Factor and are
available on the Exercism test runner.

[while-loop]: https://exercism.org/tracks/factor/exercises/protein-translation/approaches/while-loop
[qw-pair-rocket]: https://exercism.org/tracks/factor/exercises/protein-translation/approaches/qw-pair-rocket
