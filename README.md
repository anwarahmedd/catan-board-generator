# Catan Board Generator

## What it is

A Catan board generator that produces balanced, playable boards. It is a single
self-contained `index.html` — vanilla HTML/CSS/JS with SVG rendering, no build
step, and no dependencies. Open it in a browser and click "Generate new board."

## Why

The board generators available online tend to produce lopsided setups —
clustered resources, one goldmine corner, a whole resource that's overpowered.
This enforces a set of balance constraints so every generated board is actually
fair to play on.

## How the balancing works

Generation uses rejection sampling: shuffle a board, check it against six rules,
and reshuffle until one passes. There is a 5000-attempt cap as a safety net, but
the fallback rate is effectively zero. Each rule catches a different kind of
imbalance:

1. **No adjacent red numbers** — the 6 and 8 (highest-probability rolls) can't
   sit on neighboring tiles. This is the one rule official Catan enforces; most
   online generators skip it.

2. **No adjacent duplicate numbers** — two tiles with the same number can't
   touch, so production doesn't concentrate in one area.

3. **Resource pip-gap limit** — each resource's total roll-probability ("pips")
   is summed, and the gap between the richest and poorest resource is capped, so
   no resource is globally over- or under-powered. Tunable (`MAX_PIP_GAP`).

4. **Mirror-line regional balance** — the board's three symmetry axes each split
   it into two halves; the pip difference between halves is capped on all three,
   preventing high-value tiles from clustering in one region. Adapted from a
   published Catan balance analysis (boardgameanalysis.com) and implemented as a
   generation constraint. Tunable (`MAX_HALF_PIP_DIFF`).

5. **No 13-pip settlement spots** — every settlement corner (where 3 tiles meet)
   is capped at 12 pips, so no single build location is a runaway prize. Corners
   are derived from the tile adjacency map. Tunable (`MAX_SETTLEMENT_PIPS`).

6. **No fully-connected resource blob** — no single resource may have all its
   same-type tiles connected in one clump, preventing "all the brick in one
   corner" boards. Derived from the adjacency map. Tunable (`MAX_RESOURCE_BLOB`).

The rules span two axes — local vs. global, and number vs. resource — so a board
must be fair in adjacency, at build spots, across resources, and across regions
to pass.

## Tuning

The tunable constants live at the top of the file and can be loosened or
tightened to trade strictness for variety.
