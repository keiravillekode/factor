# Introduction

ETL inverts an association: the legacy format maps a score to a group of
uppercase letters, the new format maps each lowercase letter to its score.
Every solution must visit each letter of each group and build the flipped
table; the approaches differ in whether that table is filled in by hand or
produced by assoc combinators.

## Approach: nested loops with set-at

```factor
:: transform ( legacy -- new )
    H{ } clone :> result
    legacy [| score letters |
        letters [| letter | score letter >lower result set-at ] each
    ] assoc-each
    result ;
```

Loop over the entries, loop over the letters, and `set-at` each lowercase
letter into a freshly cloned hashtable.
[Read more about the nested loops approach][nested-each].

## Approach: a pipeline with `assocs.extras`

```factor
: transform ( legacy -- new )
    [ [ >lower ] map ] assoc-map assoc-invert expand-keys-set-at ;
```

State the transformation as three steps — lowercase the letters, invert
the assoc, expand the grouped keys — each a single word from the standard
library.
[Read more about the assocs.extras approach][assocs-extras].

## Which approach to use?

The nested loops are explicit and beginner-readable, at the price of
mutable state and bookkeeping.

The pipeline is shorter than the problem statement and pure from end to
end, but leans on knowing (or discovering) that `assocs.extras` already
has `assoc-invert` and `expand-keys-set-at` — a vocabulary worth browsing
before hand-rolling any assoc manipulation.

[nested-each]: https://exercism.org/tracks/factor/exercises/etl/approaches/nested-each
[assocs-extras]: https://exercism.org/tracks/factor/exercises/etl/approaches/assocs-extras
