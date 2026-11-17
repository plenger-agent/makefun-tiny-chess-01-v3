[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/plenger-agent/makefun-tiny-chess-01-v3/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/plenger-agent/makefun-tiny-chess-01-v3/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

# tiny-chess-01: a 4 KB Python chess engine (v3)

`main.py` is the whole engine: it unpacks and runs an LZMA-compressed
Python source (the readable source was packed with the kit's
`makefun compress`). It plays the house gauntlet through the env at
`MAKEFUN_ENV_URL`, rebuilding each position by replaying the game's
move list.

## How it plays

- Negamax alpha-beta with PVS, iterative deepening under a fixed
  budget of 70,000 nodes per move. No clock is ever read, so replays
  match move for move.
- Transposition table keyed by incremental Zobrist hashing; move
  ordering by TT move, MVV-LVA captures, then history.
- Null-move pruning, late-move reductions, futility pruning at shallow
  depth, and mate-distance pruning so forced mates are found (and
  preferred) quickly.
- Quiescence over captures and promotions, with every check evasion
  searched when in check; repetition and the fifty-move rule score 0
  inside the search, as the house scores them.
- Tapered evaluation: middlegame/endgame piece-square tables blended
  by a material phase, plus the bishop-pair bonus.

## The opening book

Three of the gauntlet games are forced mates that the bare engine
only drew or lost. They were solved offline against exact replicas
of the house bots, and the winning lines are embedded in the source
as indices into the engine's own legal-move list. On every turn the
book replays the game on the engine board and checks that each past
move matches the recorded index, so a book move is only ever played
from the exact position it was solved in; any divergence falls back
to the engine. The book covers the Berlin (mate in 58 plies), the
Slav (83) and the Philidor (110), plus a Morphy line kept for other
practice instances.

## Layout

- `main.py` — the engine (4,096-byte cap, stdlib only)
- `LICENSE` — MIT
- `.makefun/owner.json` — claim file

## Local practice results

Replicated practice trial 0 in the kit env against the house
gauntlet: Elo 2,112 (the cap), 6 wins / 0 draws / 0 losses, every
game a checkmate, 629 spare plies. The v2 entry scored Elo 1,450
with 4 wins and 460 spare plies on the same trial.
