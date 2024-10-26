<template>
  <div class="currentPlayer">   Current player: {{ currentPlayerColor }}</div>

  <div class="container">
    <div id="gameboard">
      <div v-for="(col, colIndex) in chessBoard" :key="colIndex">
        <div
          @click="tileClicked(colIndex, rowIndex, piece.type, piece.color)"
          v-for="(piece, rowIndex) in col"
          v-bind:class="rowIndex % 2 === 0 + (colIndex % 2) ? 'light' : 'dark'"
          class="square"
          :key="rowIndex"
        >
          <div
            v-bind:class="
              isHighlighted(colIndex,rowIndex)
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


const currentlySelected = ref({col:Number,row:Number})
watch(currentlySelected)

const currentPlayerColor = ref('white')

function isHighlighted(colIndex,rowIndex) {
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

function movePawn(rowIndex, colIndex, move, pieceColor, iterator = 1) {
  let newRow = rowIndex + move.row * iterator
  let newCol = colIndex + move.col * iterator

  if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) return true // outside board
  if (chessBoard[newCol][newRow].color === pieceColor) return true // same color piece

  if (chessBoard[newCol][newRow].type != null) return true // stop if piece found
  highlightSet.value.add(newCol + ',' + newRow)
  
}

function pawnAttack(rowIndex, colIndex, move, pieceColor) {
  let newRow = rowIndex + move.row 

  let newCol = colIndex + move.col+1 
  if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) return true // outside board
  if (chessBoard[newCol][newRow].type != null && pieceColor != chessBoard[newCol][newRow].color) 
  {
    highlightSet.value.add(newCol + ',' + newRow)
  }

  newCol = colIndex + move.col-1 
  if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) return true // outside board
  if (chessBoard[newCol][newRow].type != null && pieceColor != chessBoard[newCol][newRow].color) 
  {
    highlightSet.value.add(newCol + ',' + newRow)
  }
    
  if (chessBoard[newCol][newRow].type != null) return true // stop if piece found
}

function movePieceToHighlightedSquare(colIndex, rowIndex)
{
  if(isHighlighted(colIndex, rowIndex))
  {
    chessBoard[colIndex] [rowIndex] = chessBoard[currentlySelected.value.colIndex][currentlySelected.value.rowIndex]
    chessBoard[currentlySelected.value.colIndex][currentlySelected.value.rowIndex] = {type: null, color: null}
    currentPlayerColor.value = currentPlayerColor.value === chessPieceColors[0] 
    ? chessPieceColors[1] 
    : chessPieceColors[0];
  }
  highlightSet.value.clear()
}

const whitePawnMoves = { row: 1, col: 0 }
const blackPawnMoves = { row: -1, col: 0 }
const rookMoves = [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
      ]
const KnightMoves = [
        { row: -2, col: -1 },
        { row: -2, col: 1 }, // Two up, one left/right
        { row: -1, col: -2 },
        { row: -1, col: 2 }, // One up, two left/right
        { row: 1, col: -2 },
        { row: 1, col: 2 }, // One down, two left/right
        { row: 2, col: -1 },
        { row: 2, col: 1 }, // Two down, one left/right
      ]
const bishopMoves = [
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]
const QueenMoves = [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]
const KingMoves = [
        { row: 1, col: 0 }, // down
        { row: -1, col: 0 }, // up
        { row: 0, col: 1 }, // right
        { row: 0, col: -1 }, // left
        { row: 1, col: 1 }, // bottom-right diagonal
        { row: 1, col: -1 }, // bottom-left diagonal
        { row: -1, col: 1 }, // top-right diagonal
        { row: -1, col: -1 }, // top-left diagonal
      ]

function highlightMoveSquares(colIndex, rowIndex, pieceType, pieceColor){
  highlightSet.value.clear()
  switch (pieceType) {
    case null:
      break
    case 1:
      //pawn
      { let move = whitePawnMoves;
      if(pieceColor == 1)
      {
        move = blackPawnMoves;
      }
   
      let pawnRange = 1;
      if(pieceColor == 0 && rowIndex == 1 || pieceColor == 1 && rowIndex == 6)
      {
        pawnRange = 2
      }
      for (let i = 1; i <= pawnRange; i++) {
        movePawn(rowIndex,colIndex,move,pieceColor,i)
      }
      pawnAttack(rowIndex,colIndex,move,pieceColor)
    
      break }

    case 2:
      //rook
      for (let move of rookMoves) {
        for (let i = 1; i < 8; i++) {
          if (moveFigure(rowIndex, colIndex, move, pieceColor, i)) break
        }
      }
      break

    case 3:
      // knight
      for (let move of KnightMoves) {
        moveFigure(rowIndex, colIndex, move, pieceColor)
      }
      break

    case 4:
      //bishop
      for (let move of bishopMoves) {
        for (let i = 1; i < 8; i++) {
          if (moveFigure(rowIndex, colIndex, move, pieceColor, i)) break
        }
      }

      break

    case 5:
      // queen
      for (let move of QueenMoves) {
        for (let i = 1; i < 8; i++) {
          if (moveFigure(rowIndex, colIndex, move, pieceColor, i)) break
        }
      }

      break

    case 6:
      // king
      for (let move of KingMoves) {
        moveFigure(rowIndex, colIndex, move, pieceColor)
      }
      break
  }
}

function tileClicked(colIndex, rowIndex, pieceType, pieceColor) {
   
  if( chessPieceColors[pieceColor] == currentPlayerColor.value)
  {
    highlightMoveSquares(colIndex, rowIndex, pieceType, pieceColor)
    currentlySelected.value = {colIndex, rowIndex}
  }
  else
  {
    movePieceToHighlightedSquare(colIndex, rowIndex);
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
  height: 80%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.currentPlayer
{
  color:white;
  background-color: red;
  height: 20%;
  align-items: center;
  justify-content: center;
}
</style>
