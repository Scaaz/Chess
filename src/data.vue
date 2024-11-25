<script>
export const chessPieceIcons = {
  1: 'chess-pawn',
  2: 'chess-rook',
  3: 'chess-knight',
  4: 'chess-bishop',
  5: 'chess-queen',
  6: 'chess-king',
}

export const chessPieceColors = {
  0: 'white',
  1: 'black',
}

export const startingChessboard = [
  [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}],
  [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
  [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
  [{type: 5, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 5, color: 0}, {type: null, color: null}, {type: 6, color: 1}],
  [{type: 6, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 5, color: 1}],
  [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
  [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
  [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}]
];

// const startingChessboard = [
//   [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 6, color: 1}],
//   [{type: 6, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 5, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
//   [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}]
// ];


// const startingChessboard = [
//   [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}],
//   [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 5, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 6, color: 1}],
//   [{type: 6, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}]]

export const whitePawnMoves = { row: 1, col: 0 }
export const blackPawnMoves = { row: -1, col: 0 }

export const rookMoves = [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
      ]
export const KnightMoves = [
        { row: -2, col: -1 },
        { row: -2, col: 1 }, // Two up, one left/right
        { row: -1, col: -2 },
        { row: -1, col: 2 }, // One up, two left/right
        { row: 1, col: -2 },
        { row: 1, col: 2 }, // One down, two left/right
        { row: 2, col: -1 },
        { row: 2, col: 1 }, // Two down, one left/right
      ]
export const bishopMoves = [
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]
export const QueenMoves = [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]
export const KingMoves = [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]
</script>