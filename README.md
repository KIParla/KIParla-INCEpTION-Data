# KIParla-INCEpTION-Data

KIParla conversations in WebAnno TSV 3.3, the format INCEpTION imports, derived from the
transcriptions of the KIParla modules.

```
KIParla-INCEpTION-Data/
├── full-typesystem.xml     layer definitions (custom layers + DKPro types)
├── KIP/                    one <CODE>.tsv per conversation
├── KIPasti/
├── ParlaBO/
├── ParlaBZ/
├── ParlaTO/
├── Stra-ParlaBO/
└── Stra-ParlaTO/
```

One subfolder per module, because the codes `TOD2001`–`TOD2016` exist in two modules and
would overwrite each other in a single folder.

## Authors

- Martina Simonotti
- Ludovica Pannitto

## Layers

Each file declares these layers in its header; they must exist in the INCEpTION project with
exactly these names and features (all defined in `full-typesystem.xml`).

| layer            | type  | feature                              | content                                                                                                                                                                                         |
| ---------------- | ----- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Intonation`     | span  | `Intonation_values`                  | `Falling`, `Rising`, `WeaklyRising` (the `Intonation` feature of `jefferson_feats`, unchanged)                                                                                                  |
| `Jefferson`      | span  | `Jefferson_values`                   | the token as transcribed (`span` column), with `[ ] \| ; *` escaped                                                                                                                             |
| `Speaker`        | span  | `Speaker_values`                     | speaker code, on the first token of each unit only                                                                                                                                              |
| `TokenID`        | span  | `TokenID_values`                     | `token_id` of the TSV (e.g. `18-6`)                                                                                                                                                             |
| `Type`           | span  | `Type_values`                        | `linguistic`, `nonverbalbehavior`, `shortpause`, `anonymized`, `unknown`, `error`                                                                                                               |
| `code_variation` | span  | `code_variation_values`              | derived from the `code_variation` column: `none` when it is `_`, else the `Code` value in lower case (`other`, `underspecified`, `unsure`), or `yes` when there is only `ContainsVariation=Yes` |
| `Prolongation`   | span  | `Prolongation_values`                | `prolongations` column (e.g. `0x1`)                                                                                                                                                             |
| `Pace`           | span  | `Pace_values`                        | `pace` column                                                                                                                                                                                   |
| `Guesses`        | span  | `Guesses_values`                     | `guesses` column                                                                                                                                                                                |
| `OverlapsLink`   | chain | `referenceRelation`, `referenceType` | links the tokens of an overlapping group                                                                                                                                                        |

`_` means no value. `Intonation` and `code_variation` values are not defined by any tagset in the
project: if you define them as closed lists in INCEpTION, use the values above.
Note that the layer is called `code_variation` in lower case, as in the TSV, while the others are capitalised.

## Structure of a file

- One INCEpTION sentence per transcription unit (`tu_id`).
- One INCEpTION token per row of the TSV: words, short pauses (`(.)`) and non-verbal
  behaviours (`((ride))`) alike. The `syntactic` rows of multiword tokens are left out.
- Character offsets run through the whole document; the separator between tokens (and between
  units) counts as one character.
- A word that overlaps only in part (`overlaps` of the form `3-7(2)`) is followed by an extra
  token, with id `<sentence>-<token>.<n>`, that holds just the overlapping stretch and carries
  the overlap link. A word that overlaps entirely carries the link itself.
- Overlap links are `*->G-N` / `*[G]`: the token is the N-th element of overlapping group G
  (groups are numbered from 1).

## Importing

1. Create an INCEpTION project and set up the layers of the table above in it (the types are
   in `full-typesystem.xml`).
2. Import the files as *WebAnno TSV 3.3*.

The first point has not been checked against a real project yet: if the import of the layers
fails, create them by hand with the names and features listed above.

## Overlap ranges

In the source tables the `overlaps` column (`3-7(2)`) counts the characters of the transcribed
token once punctuation (`: . , ?`) and brackets (`[ ] ( ) < > °` ) are removed. That count still
includes the variation markers `#`, `$` and `*`, which are not part of the word: `$forsa]` is the
word `forsa` (5 characters) but has the range `0-6`. Here every range is converted to the
characters of the word, so the extra token of a partial overlap holds the right stretch.

A range that reaches the first or last character of the word is extended to the whole word.
One range in the data (`2-3` on `(g~)`, word `g~`, conversation BOA1001) is past the end of its
own word, probably because the word was edited after the overlap was computed; there the link
goes on the whole token.
