# A pipeline with assocs.extras

```factor
USING: assocs assocs.extras sequences unicode ;
IN: etl

: transform ( legacy -- new )
    [ [ >lower ] map ] assoc-map assoc-invert expand-keys-set-at ;
```

## Three transformations, three words

Read as data flow, the task is a pipeline: lowercase the letters, swap
keys and values, and flatten the letter groups into individual keys.
Each step has a library word, so `transform` is exactly three of them —
no locals, no mutation, no explicit loops.

[`assoc-map`][assoc-map] rewrites every entry; the quotation receives key
and value, leaves the score alone, and maps [`>lower`][>lower] over the
letter group:

```text
H{ { 1 { "A" "E" } } }  →  H{ { 1 { "a" "e" } } }
```

[`assoc-invert`][assoc-invert], from the
[`assocs.extras`][assocs.extras] vocabulary, swaps every key with its
value — the letter groups become the keys:

```text
H{ { 1 { "a" "e" } } }  →  H{ { { "a" "e" } 1 } }
```

[`expand-keys-set-at`][expand-keys-set-at], also from `assocs.extras`,
gives each element of a sequence key its own entry with the shared value —
precisely the flattening the exercise asks for:

```text
H{ { { "a" "e" } 1 } }  →  H{ { "a" 1 } { "e" 1 } }
```

## Why it reads well

The nested-loop solution interleaves *what* is computed with *how* the
result table is filled in.
Here the *how* is delegated: `expand-keys-set-at` owns the "one entry per
letter" logic and `assoc-invert` the direction flip, leaving `transform`
to state the recipe.
The cost is vocabulary knowledge — `assocs.extras` is a large toolbox, and
finding the right word takes longer than writing the loop the first time,
but pays off every time after that.

The `assocs.extras` vocabulary ships with Factor and is available on the
Exercism test runner.

[assoc-map]: https://docs.factorcode.org/content/word-assoc-map,assocs.html
[>lower]: https://docs.factorcode.org/content/word-__gt__lower,unicode.html
[assocs.extras]: https://docs.factorcode.org/content/vocab-assocs.extras.html
[assoc-invert]: https://docs.factorcode.org/content/word-assoc-invert,assocs.extras.html
[expand-keys-set-at]: https://docs.factorcode.org/content/word-expand-keys-set-at,assocs.extras.html
