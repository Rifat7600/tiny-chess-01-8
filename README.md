# Tiny Chess 4K — Check-Extension Variant

[![make.fun](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/Rifat7600/tiny-chess-01-8/rank.svg)](https://make.fun/bounty/tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/Rifat7600/tiny-chess-01-8/reward.svg)](https://make.fun/bounty/tiny-chess-01)

A chess engine in under 4 KB of Python, written for the
[make.fun tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01):
build the strongest chess engine in 4 KB of Python.

## How it plays

The engine is a single `main.py` (Python 3.13, standard library only).
It talks to the trial environment over HTTP, reads the position from the
game state, and answers with a UCI move. There is no clock anywhere in
the search: every move comes from a fixed budget of 45,000 nodes, so a
trial replayed move for move gives the same games.

Under the hood it is a 0x88 board with:

- alpha-beta search with a transposition table and killer-move ordering
- quiescence search over captures and promotions, extended to every
  legal reply when the side to move is in check
- the main search also extends a ply when the side to move is in check,
  so tactics around the king get searched deeper
- a hand-built evaluation: material plus my own piece-square tables
  (pawns advance, knights and bishops centralise, rooks like the
  seventh rank, the king hides in the middlegame and centralises in
  the endgame), with a bonus for the bishop pair

The point of this variant is tactical sharpness: the games that decide
the rating are the ones against the strongest house engines, and those
are usually decided by a tactic, so the check extensions spend the
node budget where it matters most.

## Building

The engine is written readably in `source.py` (kept uncommitted, it is
not part of the entry). The submitted `main.py` is produced from it by
the kit's own compressor, run as its own command after the last edit
to the source:

    node <kit>/cli/makefun.mjs compress source.py --out main.py

That strips comments and whitespace and packs the engine down to
3,550 bytes, under the 4,096-byte cap. The rest of the repository is
just this README, the MIT license, and `.makefun/owner.json`, so the
whole snapshot stays well under the 8,192-byte cap too.
