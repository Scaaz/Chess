<template>
  <div class="container">
    <div id="gameboard">
      <div v-for="(col, colIndex) in chessBoard" :key="colIndex">
        <div
          @click="clicked(colIndex, rowIndex, piece.type, piece.color)"
          v-for="(piece, rowIndex) in col"
          v-bind:class="rowIndex % 2 === 0 + (colIndex % 2) ? 'light' : 'dark'"
          class="square"
          :key="rowIndex"
        >
          <div
            v-bind:class="
              isHighlighted(rowIndex, colIndex)
                ? piece.type === null
                  ? 'move'
                  : 'attack'
                : ''
            "
          ></div>
          <font-awesome-icon
            v-if="piece.type != null"
            :icon="chessPieceIcons[piece.type]"
            size="2x"
            :color="chessPieceColors[piece.color]"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

let chessPieceIcons = {
  1: 'chess-pawn',
  2: 'chess-rook',
  3: 'chess-knight',
  4: 'chess-bishop',
  5: 'chess-queen',
  6: 'chess-king',
}

let chessPieceColors = {
  0: 'white',
  1: 'black',
}

let chessBoard = [
  [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}],
  [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
  [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
  [{type: 5, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 6, color: 1}],
  [{type: 6, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 5, color: 1}],
  [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
  [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
  [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}]
];

const highlightSet = ref(new Set())
watch(highlightSet)

function isHighlighted(rowIndex, colIndex) {
  return highlightSet.value.has(`${colIndex},${rowIndex}`)   
}

function moveFigure(rowIndex, colIndex, move, pieceColor, iterator = 1) {
  let newRow = rowIndex + move.row * iterator
  let newCol = colIndex + move.col * iterator

  if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) return true // outside board
  if (chessBoard[newCol][newRow].color === pieceColor) return true // same color piece

  highlightSet.value.add(newCol + ',' + newRow)
  if (chessBoard[newCol][newRow].type != null) return true // stop if piece found
}

function clicked(colIndex, rowIndex, pieceType, pieceColor) {
  highlightSet.value.clear()
  switch (pieceType) {
    case null:
      break
    case 1:
      //pawn
      for (let i = 1; i <= 2; i++) {
        let newRow = pieceColor ? rowIndex - i : rowIndex + i
        if (
          newRow > 7 ||
          newRow < 0 ||
          chessBoard[colIndex][newRow].color === pieceColor
        )
          break
        highlightSet.value.add(colIndex + ',' + newRow)
        if (chessBoard[colIndex][newRow].type != null) {
          break
        }
      }
      break

    case 2:
      //rook

      for (let move of [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
      ]) {
        for (let i = 1; i < 8; i++) {
          if (moveFigure(rowIndex, colIndex, move, pieceColor, i)) break
        }
      }
      break

    case 3:
      // knight
      for (let move of [
        { row: -2, col: -1 },
        { row: -2, col: 1 }, // Two up, one left/right
        { row: -1, col: -2 },
        { row: -1, col: 2 }, // One up, two left/right
        { row: 1, col: -2 },
        { row: 1, col: 2 }, // One down, two left/right
        { row: 2, col: -1 },
        { row: 2, col: 1 }, // Two down, one left/right
      ]) {
        moveFigure(rowIndex, colIndex, move, pieceColor)
      }
      break

    case 4:
      //bishop
      for (let move of [
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]) {
        for (let i = 1; i < 8; i++) {
          if (moveFigure(rowIndex, colIndex, move, pieceColor, i)) break
        }
      }

      break

    case 5:
      // queen
      for (let move of [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]) {
        for (let i = 1; i < 8; i++) {
          if (moveFigure(rowIndex, colIndex, move, pieceColor, i)) break
        }
      }

      break

    case 6:
      // king
      for (let move of [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]) {
        moveFigure(rowIndex, colIndex, move, pieceColor)
      }
      break
  }
}
</script>
<style scoped>
#gameboard {
  width: 320px;
  height: 320px;
  background-color: red;
  display: flex;
  flex-wrap: wrap;
}

.square {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 40px;
  width: 40px;
}

.move {
  background-color: gray;
  border-radius: 50%;
  height: 15px;
  width: 15px;
  opacity: 50%;
  position: absolute;
}

.attack {
  background-color: transparent;
  border: 4px solid gray;
  border-radius: 50%;
  height: 40px;
  width: 40px;
  opacity: 50%;
  position: absolute;
}

.whitePiece {
  color: white;
}

.blackPiece {
  color: black;
}

.light {
  background-color: #ffd599;
}

.dark {
  background-color: #b16e41;
}

.container {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
