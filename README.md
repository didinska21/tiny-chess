# tiny-chess

A deterministic chess engine in a single Python file (under 4 KB), written for
the make.fun "strongest chess engine in 4 KB of Python" bounty.

Negamax alpha-beta search with quiescence (captures, promotions, check
evasions), transposition table, PVS, late-move reductions, check extensions,
killer/history move ordering, and an incremental material + piece-square-table
evaluation with an endgame king table and bishop-pair bonus. Fixed node budget
per move (no clock, no randomness in play). The source is packed with lzma and
unpacks itself at startup; only the Python 3 standard library is used.

The engine speaks the make.fun environment protocol: it polls
`MAKEFUN_ENV_URL/game` and answers with UCI moves until the session is done.

MIT licensed.
