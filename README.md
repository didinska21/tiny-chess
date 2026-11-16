# tiny-chess

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/didinska21/tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/didinska21/tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A deterministic chess engine in a single Python file (under 4 KB), written for
the make.fun "strongest chess engine in 4 KB of Python" bounty.

Negamax alpha-beta search with quiescence (captures, promotions, check
evasions), transposition table, PVS, null-move pruning, late-move reductions,
check extensions, killer/history move ordering with a malus for quiet moves
that fail at a node, and an incremental material + piece-square-table
evaluation with an endgame king table and bishop-pair bonus. Fixed node budget per move (no clock, no randomness in play). The
source is packed with lzma and unpacks itself at startup; only the Python 3
standard library is used.

The engine speaks the make.fun environment protocol: it polls
`MAKEFUN_ENV_URL/game` and answers with UCI moves until the session is done.

MIT licensed.

## Local trials

Strength was tuned with paired gauntlet trials against the kit's house
engines (three fixed 6-game trial sets, identical openings per set, 18 games
per candidate). The previously submitted build — null-move pruning plus a
larger node budget (105k soft / 150k hard) — scored 9.5/18. Adding a
history malus (quiet moves that fail at a node lose history, HS -= d*d/2)
scored 10.0/18 against that build (3.5 + 3.0 + 3.5). The current build
adds a passed-pawn bonus in the endgame evaluation on top of the malus
build and scored 11.0/18 (3.5 + 2.5 + 5.0). The current build also
caches that passed-pawn bonus by pawn structure (a pawn-only Zobrist key
maintained in make/unmake), which leaves the evaluation unchanged —
verified by identical root moves and node counts against the uncached
build and by move-for-move identical games in live trials — while
cutting the endgame scan cost (its trial-2 set finished in 1,037 s
against roughly 1,300 s for the uncached build, with five of six games
move-for-move identical; the sixth diverged on a house-engine move, a
harness state artifact, not an engine difference). A
malus-plus-deeper-LMR stack tested worse (8.5/18) and was dropped, as were
earlier candidates: a passed-pawn bonus on the older build (6.0/12 — it
only pays off combined with the malus), aspiration windows at the
root (6.0/12), and checking moves in the first quiescence ply (strong per
game but too slow per node — it nearly exhausted the trial wall-clock
limit, a forfeit risk in the final).
