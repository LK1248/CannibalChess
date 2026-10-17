# Cannibal Chess

A chess variant where every piece except the King starts as a pawn, and the only way to get stronger pieces is to eat your own.

Play online at **https://lk1248.github.io/CannibalChess/**.

It is a sibling of [Pawnbroker](https://github.com/LK1248/Pawnbroker): same starting position, but instead of buying upgrades with points, pieces merge.

## Rules

- Both back rows start as pawns, apart from the Kings on e1 and e8.
- **Merging:** a piece may move onto a square held by one of its own pieces, using its normal capturing move (pawns eat diagonally forward, sliders stop at the first piece in their path). The two become one piece on that square:

  | Eater + eaten (either order) | Result |
  |---|---|
  | Pawn + Pawn | Bishop or Knight (player's choice) |
  | Pawn + Bishop, Pawn + Knight | Rook |
  | Pawn + Rook | Rook (no upgrade) |
  | Bishop + Bishop, Bishop + Knight, Knight + Knight | Rook |
  | Bishop + Rook, Knight + Rook | Queen |
  | Rook + Rook | Queen |
  | Queen + anything | Queen (no upgrade) |
  | King + anything | King (no upgrade) |

- The King may eat its own pieces. Nothing may eat its own King. This makes the King harder to smother: a King boxed in by its own pawns can step out by eating one, unless that square is covered.
- Merging and normal pawn promotion are the only ways to get new pieces.
- A merge is an ordinary move: it can't leave your King in check, and it may be used to get out of check.
- A pawn that reaches the far row promotes as usual. If it eats a friend on that row, it promotes first and then merges, so it can always end up a Queen.
- Only pawns on the second row may move two squares. En passant is standard and only applies to enemy pawns.
- Castling needs an unmoved King and an unmoved Rook on the corner. A Rook formed on its own corner square by eating a piece that never moved counts as unmoved (for example a Knight jumping from b3 onto the untouched a1 pawn).
- Checkmate and stalemate are standard. Draws: threefold repetition, the 50-move rule (pawn moves, captures and merges reset the count), King against King, and King plus a single Bishop or Knight against a lone King. Two friendly pieces can always merge into something stronger, so no other material is treated as insufficient.

### Notation

Merges are logged with `&` and the result: `b1&c2=B` is the b1 pawn eating the c2 pawn to make a Bishop, `Nb3&a1=R` a Knight eating the a1 pawn to make a Rook. The `=X` part is left out when the piece doesn't change (`Ke1&d2`).

## Playing

`index.html` is a single self-contained page. Open it in a browser or serve the folder:

```bash
python -m http.server 8741
```

Then visit http://localhost:8741/.

- Click a piece to see its moves. Dots are moves, grey rings are captures, red rings are merges; the badge in the corner shows what the merge will make.
- Two players can share the screen, or pick **Computer plays Black/White** to play the built-in engine (iterative-deepening alpha-beta with a capture search, about 1.5 s a move, run in a Web Worker).
- The game is saved in the browser's local storage. Undo steps back to your own turn when playing the computer.

## Code layout

The rules engine and the computer player live in the `<script id="engine">` block and don't touch the DOM. The UI only calls `newGame`, `legalMoves`, `doMove` and `bestMove`. The same block is loaded into a Web Worker for the computer's search.

## Tests

`tests/tests.html` pulls the engine out of `index.html` and checks the merge table, merge legality, promotion merges, castling with a formed Rook, en passant, draw detection and mate finding, then fuzzes 300 random games and plays 8 short computer-vs-computer games. Serve the repo root as above and open http://localhost:8741/tests/tests.html.
