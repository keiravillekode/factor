# Introduction

A rotational cipher replaces each letter with the letter a fixed number of
places further down the alphabet, wrapping around at `z` — lowercase and
uppercase independently — while every other character passes through
unchanged.
Both approaches map over the string once; they differ in how a letter finds
its replacement.

## Approach: modular arithmetic

```factor
: shift-from ( base ch shift -- ch' )
    swap pick - + 26 mod + ;
```

Work on character codes: subtract the base letter, add the shift, wrap with
`26 mod`, add the base back, with a `cond` picking the lowercase base, the
uppercase base, or no change.
[Read more about the modular arithmetic approach][modular-arithmetic].

## Approach: circular alphabet

```factor
:: rotated-pairs ( alphabet shift -- pairs )
    alphabet <circular> :> circ
    shift circ change-circular-start
    alphabet circ zip ;
```

Put the alphabet on a ring instead: `<circular>` makes a view whose indices
wrap around, turning the ring by `shift` lines each letter up with its
replacement, and `zip` captures that as a substitution table for
`substitute`.
[Read more about the circular alphabet approach][circular].

## Which approach to use?

Modular arithmetic is the classic solution — compact, allocation-free, and
a direct transcription of how the cipher is usually described.

The circular approach trades a little table-building for a wholly different
mental model with no arithmetic at all, and introduces the `circular`
vocabulary — a reusable tool whenever a computation is naturally cyclic.

[modular-arithmetic]: https://exercism.org/tracks/factor/exercises/rotational-cipher/approaches/modular-arithmetic
[circular]: https://exercism.org/tracks/factor/exercises/rotational-cipher/approaches/circular
