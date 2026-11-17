# tiny-chess-01 entry

An original chess engine in a single self-contained `main.py` (4,064 bytes as
committed). The file is an lzma-packed payload with a two-line unpacker, a form
the rules explicitly allow; the unpacked source is plain Python 3, standard
library only, and reads no other file.

How it plays:

- 0x88 board with a piece list, full legal move generation (castling, en
  passant, promotions), incremental Zobrist hashing and incremental
  material + piece-square evaluation (bishop-pair bonus).
- Negamax alpha-beta with iterative deepening under a fixed budget of 100,000
  nodes per move (quiescence nodes included) — never a clock, so replays are
  move-for-move identical.
- Quiescence search over captures and promotions, with all evasions when in
  check; check extensions in the main search.
- Transposition table, MVV-LVA capture ordering, killer moves, and repetition
  scoring that also accounts for the game's earlier positions (seeded from the
  move list the env serves).
- Piece-square tables are stored as compact encoded strings (left-right
  symmetric, expanded at startup); Zobrist keys come from a fixed seed.

It talks to the env at `MAKEFUN_ENV_URL` exactly as the starter does:
`POST /game`, then `POST /move` until the env answers `done`.
