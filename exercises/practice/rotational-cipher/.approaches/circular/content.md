# Circular alphabet

```factor
USING: assocs circular kernel locals sequences ;
IN: rotational-cipher

CONSTANT: lower-alpha "abcdefghijklmnopqrstuvwxyz"
CONSTANT: upper-alpha "ABCDEFGHIJKLMNOPQRSTUVWXYZ"

:: rotated-pairs ( alphabet shift -- pairs )
    alphabet <circular> :> circ
    shift circ change-circular-start
    alphabet circ zip ;

:: rotate ( text shift -- cipher )
    text lower-alpha upper-alpha
    [ shift rotated-pairs ] bi@ append substitute ;
```

## A different mental model

The modular-arithmetic approach thinks in character codes.
This approach thinks in alphabets: write the alphabet on a ring, turn the
ring by the shift, and read off which letter now sits under each original
letter.
No character arithmetic appears anywhere — the wrap-around lives in the
data structure instead of in a `26 mod`.

The [`circular`][circular] vocabulary provides exactly that ring:
[`<circular>`][make-circular] wraps a sequence in a virtual view whose
indices wrap modulo its length, and
[`change-circular-start`][change-circular-start] turns the ring by moving
where index zero points — a shift of 26 comes back around to the start, so
oversized shifts need no special handling.

`rotated-pairs` builds the substitution table for one alphabet:
[`zip`][zip] pairs each plain letter with the letter `shift` places further
along the ring, giving an association list like
`{ { CHAR: a CHAR: d } { CHAR: b CHAR: e } ... }` for shift 3.

## Substituting

`rotate` builds the tables for both alphabets — `[ shift rotated-pairs ]
bi@` applies the same construction to each — appends them into one
52-entry mapping, and hands the text to [`substitute`][substitute], which
replaces every character found in the mapping and passes everything else
(spaces, digits, punctuation) through unchanged.

The per-call table construction is O(52) and buys a `rotate` that is one
line of intent: *make the shifted mapping, apply it*.

The `circular` vocabulary ships with Factor — the track's `circular-buffer`
exercise example is built on it too.

[circular]: https://docs.factorcode.org/content/vocab-circular.html
[make-circular]: https://docs.factorcode.org/content/word-__lt__circular__gt__%2Ccircular.html
[change-circular-start]: https://docs.factorcode.org/content/word-change-circular-start,circular.html
[zip]: https://docs.factorcode.org/content/word-zip,sequences.html
[substitute]: https://docs.factorcode.org/content/word-substitute,assocs.html
