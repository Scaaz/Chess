<template>  
  <div class="currentPlayer">   {{ currentPlayerColor }}'s turn</div>
  <div class="container">
    <div id="gameboard">      
      <div v-for="(col, colIndex) in chessBoard" :key="colIndex" style="position: relative">
        <div v-if="colIndex == upgradeColIndex" >
          <div class="pawn-upgrade-overlay">
            <div class="pawnUpgrade square" v-for="(piece, pieceType) in filteredIcons" :key="pieceType">                 
              <font-awesome-icon
                    :icon="piece"
                    size="2x"
                    :color="currentPlayerColor"
                    style="cursor: pointer;"
                    @click="upgradePawn(pieceType, colIndex, currentPlayerColor)"
                  />
            </div>
            <div class="cancelPawnUpgrade" @click="closePawnUpgrade()">
              X
            </div>
          </div>
        </div>
        <div
          v-for="(piece, rowIndex) in col"
          @click="tileClicked(colIndex, rowIndex, piece.type, piece.color)"
          v-bind:class="getTileColor(rowIndex, colIndex)"
          class="square"
          :key="rowIndex"
        >
          <div
            v-bind:class="
              isHighlighted(colIndex,rowIndex) || isCastle(colIndex,rowIndex) || isPawnDoubleMove(colIndex,rowIndex)
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
import { ref, watchEffect } from 'vue'

const chessPieceIcons = {
  1: 'chess-pawn',
  2: 'chess-rook',
  3: 'chess-knight',
  4: 'chess-bishop',
  5: 'chess-queen',
  6: 'chess-king',
}

const chessPieceColors = {
  0: 'white',
  1: 'black',
}

let chessBoard = [
  [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}],
  [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
  [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
  [{type: 5, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 5, color: 0}, {type: null, color: null}, {type: 6, color: 1}],
  [{type: 6, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 5, color: 1}],
  [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
  [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
  [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}]
];

// let chessBoard = [
//   [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 6, color: 1}],
//   [{type: 6, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 5, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
//   [{type: null, color: null}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
//   [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}]
// ];


// let chessBoard = [
//   [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}],
//   [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 5, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 6, color: 1}],
//   [{type: 6, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 4, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 3, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: null, color: null}],
//   [{type: 2, color: 0}, {type: 1, color: 0}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}]]

const currentlySelected = ref({col:Number,row:Number})
watchEffect(currentlySelected)

const currentlyChecked = ref({col:Number,row:Number})
watchEffect(currentlySelected)

const highlightedArray = ref([]);
watchEffect(highlightedArray)

const castleArray = ref([]);
watchEffect(castleArray)

const pawnArray = ref([]);
watchEffect(pawnArray)

const wasBlackKingMoved = ref(false);
const wasBlackShortMoved = ref(false);
const wasBlackLongMoved = ref(false);

const wasWhiteKingMoved = ref(false);
const wasWhiteShortMoved = ref(false);
const wasWhiteLongMoved = ref(false);

const upgradeColIndex = ref(null);

const currentPlayerColor = ref('white')

const filteredIcons = Object.fromEntries(
  Object.entries(chessPieceIcons).filter(([key]) => key >= 2 && key <= 5)
);

function closePawnUpgrade(){
  upgradeColIndex.value = null;
}
function getTileColor(rowIndex, colIndex)
{
  if(rowIndex == currentlyChecked.value.row && colIndex == currentlyChecked.value.col)
  {
    return 'red'
  }
  else
  {
    return rowIndex % 2 === 0 + (colIndex % 2) ? 'light' : 'dark'
  }
}

function isHighlighted(colIndex,rowIndex) {
  if(highlightedArray.value == [])
  {
    return false;
  }
  return  highlightedArray.value.some(
    (position) => position.rowIndex === rowIndex && position.colIndex === colIndex)  
}

function isCastle(colIndex,rowIndex) {
  if(castleArray.value == [])
  {
    return false;
  }
  return castleArray.value.some(
    position => position.rowIndex === rowIndex && position.colIndex === colIndex)
}

function isPawnDoubleMove(colIndex,rowIndex) {
  if(pawnArray.value == [])
  {
    return false;
  }
  return pawnArray.value.some(
    position => position.rowIndex === rowIndex && position.colIndex === colIndex)
}


function GetMoveSquares(colIndex, rowIndex, pieceType, pieceColor) {
    let array = [];    
    let iterations = 1;
    let pieceMoves = [];
    switch(pieceType)
    {
      case 1:
        return movePawn(rowIndex, colIndex, pieceType, pieceColor)
      case 2:
        pieceMoves = rookMoves;
        iterations = 8
        break;
      case 3:
        pieceMoves = KnightMoves;
        break;
      case 4:
        pieceMoves = bishopMoves;
        iterations = 8
        break;
      case 5:
        pieceMoves = QueenMoves;
        iterations = 8
        break;
      case 6:
        pieceMoves = KingMoves;
        break;
    }

    pieceMoves.forEach(move => {        
    for (let i = 1; i <= iterations; i++){
      let newRow = rowIndex + move.row * i
      let newCol = colIndex + move.col * i

      if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) break; // outside board
      if (chessBoard[newCol][newRow].color === pieceColor) break; // same color piece
      array.push({colIndex: newCol,  rowIndex: newRow});
      if (chessBoard[newCol][newRow].type != null) break; // stop if piece found
    }
  });
    return array;
}


function GetAttackSquares(colIndex, rowIndex, pieceType, pieceColor) {
    let array = [];    
    let iterations = 1;
    let pieceMoves = [];
    switch(pieceType)
    {
      case 1:
        return pawnAttack(rowIndex, colIndex, pieceType, pieceColor)
      case 2:
        pieceMoves = rookMoves;
        iterations = 8
        break;
      case 3:
        pieceMoves = KnightMoves;
        break;
      case 4:
        pieceMoves = bishopMoves;
        iterations = 8
        break;
      case 5:
        pieceMoves = QueenMoves;
        iterations = 8
        break;
      case 6:
        pieceMoves = KingMoves;
        break;
    }

    pieceMoves.forEach(move => {        
    for (let i = 1; i <= iterations; i++){
      let newRow = rowIndex + move.row * i
      let newCol = colIndex + move.col * i

      if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) break; // outside board
      if (chessBoard[newCol][newRow].color === pieceColor) break; // same color piece
      array.push({colIndex: newCol,  rowIndex: newRow});
      if (chessBoard[newCol][newRow].type != null) break; // stop if piece found
    }
  });
    return array;
}

function movePawn(rowIndex, colIndex, pieceType, pieceColor) {  

  let move = whitePawnMoves;
  if(pieceColor === 1)
  {
    move = blackPawnMoves;
  }

  let array = []; 
  
  let newRow = rowIndex + move.row 
  let newCol = colIndex + move.col 


  if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) return []; // outside board
  if (chessBoard[newCol][newRow].color === pieceColor) return []; // same color piece
  if (chessBoard[newCol][newRow].type != null) return []; // stop if piece found

  
  if(pieceColor == 0 && rowIndex == 1)
  {
    pawnArray.value.push({colIndex: newCol,  rowIndex: newRow+1})
  }
  else if(pieceColor == 1 && rowIndex == 6)
  {
    pawnArray.value.push({colIndex: newCol,  rowIndex: newRow-1})
  }
  array.push({colIndex: newCol,  rowIndex: newRow})
  
  let attack = pawnAttack(rowIndex,colIndex, pieceType, pieceColor)
  array = array.concat(attack)//add attack squares
  return array;  
}

function pawnAttack(rowIndex, colIndex, pieceType, pieceColor) {  
  let array = [];
  let move = whitePawnMoves;
    if(pieceColor == 1)
    {
      move = blackPawnMoves;
    }

  let newRow = rowIndex + move.row 
  let newCol = colIndex + move.col+1 
  if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) return array // outside board
  if (chessBoard[newCol][newRow].type != null && pieceColor != chessBoard[newCol][newRow].color) 
  {
    array.push({colIndex: newCol,  rowIndex: newRow})
  }

  newCol = colIndex + move.col-1 
  if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) return array // outside board
  if (chessBoard[newCol][newRow].type != null && pieceColor != chessBoard[newCol][newRow].color) 
  {
    array.push({colIndex: newCol,  rowIndex: newRow})
  }
    
  if (chessBoard[newCol][newRow].type != null) return array // stop if piece found

  return array;
}

function movePiece(targetcol, targetrow)
{ 
  chessBoard[targetcol] [targetrow] = chessBoard[currentlySelected.value.col][currentlySelected.value.row]
  chessBoard[currentlySelected.value.col][currentlySelected.value.row] = {type: null, color: null}
}

function movePieceToTarget(colIndex, rowIndex, targetcol, targetrow)
{ 
  chessBoard[targetcol] [targetrow] = chessBoard[colIndex][rowIndex];
  chessBoard[colIndex][rowIndex] = {type: null, color: null};

}

function EndTurn(){
  currentPlayerColor.value = currentPlayerColor.value === chessPieceColors[0] 
  ? chessPieceColors[1] 
  : chessPieceColors[0];
  highlightedArray.value = [];
  castleArray.value = [];
  pawnArray.value = [];
  upgradeColIndex.value = null;
  removeEnPassantFakes(currentPlayerColor.value)
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

function upgradePawn(pieceType,colIndex, color){
let colorId = color=="black" ? 1 : 0;
chessBoard[colIndex][0] = {type: parseInt(pieceType), color: colorId}
if(color === chessPieceColors[0])
{
  chessBoard[colIndex][6] = {type: null, color: null}
}
else
{
  chessBoard[colIndex][1] = {type: null, color: null}
}
EndTurn();
}

function tileClicked(colIndex, rowIndex, pieceType, pieceColor) {
   if( chessPieceColors[pieceColor] == currentPlayerColor.value){
      highlightedArray.value = GetMoveSquares(colIndex, rowIndex, pieceType, pieceColor)

      if(pieceType == 6)
      {      
        if(pieceColor === 1)
        {
          if(canCastleLongSide()) {
            castleArray.value.push({rowIndex : 7, colIndex : 5})
          }
          if(canCastleShortSide()){
            castleArray.value.push({rowIndex : 7, colIndex : 1})
          }
        }
        else {
          if(canCastleLongSide()) {
            castleArray.value.push({colIndex : 2, rowIndex : 0})
          }
          if(canCastleShortSide()) {           
            castleArray.value.push({colIndex : 6, rowIndex : 0})
          }
        }
      }

      currentlySelected.value = {col:colIndex, row:rowIndex}
   }
   else if (isHighlighted(colIndex, rowIndex)) {
    let piece = chessBoard[currentlySelected.value.col][currentlySelected.value.row];
    if(rowIndex === 0 &&  piece.type === 1 && piece.color === 1)
    {
      upgradeColIndex.value = colIndex;
    }
    else if(rowIndex === 7 &&  piece.type === 1 && piece.color === 0)
    {
      upgradeColIndex.value = colIndex;
    }
    else
    {    
    if(pieceType === 7)
   {
    let direction = -1
    if(currentPlayerColor.value === chessPieceColors[1])
      {
        direction = 1;
      }
    chessBoard[colIndex][rowIndex+direction] = {type: null, color: null}
   }
     movePiece(colIndex, rowIndex); 
     EndTurn();

     if(chessBoard[colIndex][rowIndex].type == 6)
     {
        if(chessBoard[colIndex][rowIndex].color === 1)
        {
          wasBlackKingMoved.value = true
        }
        else
        {
          wasWhiteKingMoved.value = true
        }
     }
     
     if(chessBoard[colIndex][rowIndex].type == 2)
     { 
        if(currentlySelected.value.col === 0 && currentlySelected.value.row === 7)
        {
          wasBlackShortMoved.value = true;
        }
        else if(currentlySelected.value.col === 7 && currentlySelected.value.row === 7)
        {
          wasBlackLongMoved.value = true;
        }     
        else if(currentlySelected.value.col === 7 && currentlySelected.value.row === 0)
        {
          wasWhiteShortMoved.value = true;
        }
        else if(currentlySelected.value.col === 0 && currentlySelected.value.row === 0)
        {
          wasWhiteLongMoved.value = true;
        }      
     }
    }
     highlightedArray.value = [];
     isKingChecked()
   }
   else if(isCastle(colIndex,rowIndex))
   {    
    if(rowIndex === 0)
    {
      //white
      wasWhiteKingMoved.value = true;
      if(colIndex === 1)
      {
        //long
        movePieceToTarget(0, 0, 3,0);
        movePieceToTarget(4, 0, 2,0);
        EndTurn()
      }
      else
      {
        //short
        movePieceToTarget(7, 0, 5,0);
        movePieceToTarget(4, 0, 6,0);
        EndTurn()
      }
    }
    else if(rowIndex === 7)
    {
      //black
      wasBlackKingMoved.value = true;
      if(colIndex === 1)
      {
       //short

       movePieceToTarget(3, 7, 1,7);
       movePieceToTarget(0, 7, 2,7);
       EndTurn()
      }
      else
      {
        //long
        movePieceToTarget(3, 7, 6,7);
        movePieceToTarget(7, 7, 5,7);
        EndTurn()
      }
    }
   }
    else if(isPawnDoubleMove(colIndex, rowIndex))
   {
      movePiece(colIndex,rowIndex);
      let direction = -1
      let fakePieceColor = 0;
      if(currentPlayerColor.value === chessPieceColors[1])
      {
        direction = 1;
        fakePieceColor = 1;
      }
      let newRow = rowIndex + direction;
      chessBoard[colIndex][newRow] = {type: 7, color: fakePieceColor}
      EndTurn();
   }
 }

 function removeEnPassantFakes(playerColor)
 {
  for (let colIndex = 0; colIndex < chessBoard.length; colIndex++) {
    for (let rowIndex = 0; rowIndex < chessBoard[colIndex].length; rowIndex++) {
      let piece = chessBoard[colIndex][rowIndex];

      if(piece.type === 7 && chessPieceColors[piece.color] == playerColor)
      {
        chessBoard[colIndex][rowIndex] = {type: null, color: null}
      }
    }
  }
 }


function canCastleLongSide()
{
  if(currentPlayerColor.value === chessPieceColors[0])
  {
    //white
    if(wasWhiteKingMoved.value || wasWhiteLongMoved.value)
    {
      return false
    }

    if(chessBoard[3][0].type !== null || chessBoard[2][0].type !== null || chessBoard[1][0].type !== null)
    {
      //figures on way
      return false;
    }
    if(isFieldChecked(3,0) || isFieldChecked(2,0) || isFieldChecked(1,0))
    {
      //check on way
      return false;
    }
  }
  else if (currentPlayerColor.value === chessPieceColors[1])
  {
    if(wasBlackKingMoved.value || wasBlackLongMoved.value)
    {
      return false
    }

    if(chessBoard[4][7].type !== null || chessBoard[5][7].type !== null || chessBoard[6][7].type !== null)
    {
      //figures on way
      return false;
    }
    if(isFieldChecked(4,7) || isFieldChecked(5,7) || isFieldChecked(6,7))
    {
      //check on way
      return false;
    }
  }
  return true;
}

function canCastleShortSide()
{
  if(currentPlayerColor.value === chessPieceColors[0])
  {
   //White
   if(wasWhiteKingMoved.value || wasWhiteShortMoved.value)
    {
      return false
    }

    //short side
    if(chessBoard[5][0].type !== null || chessBoard[6][0].type !== null)
    {
      //figures on way
      return false;
    }
    if(isFieldChecked(5,0) || isFieldChecked(6,0))
    {
      //check on way
      return false;
    }  
  }
  else if (currentPlayerColor.value === chessPieceColors[1])
  {
    //Black
    if(wasBlackKingMoved.value || wasBlackShortMoved.value)
    {
      return false;
    }

    //short side
    if(chessBoard[2][7].type !== null || chessBoard[1][7].type !== null)
    {
      //figures on way
      return false;
    }
    if(isFieldChecked(2,7) || isFieldChecked(1,7))
    {
      //check on way
      return false;
    }
  }
  return true;
}


function getKingPosition(playerColor)
{    
  for(let colIndex in chessBoard){
    for(let rowIndex in chessBoard){
      let piece = chessBoard[colIndex][rowIndex];
      if (piece.type === 6 && chessPieceColors[piece.color] === playerColor) {
        return  {colIndex, rowIndex}               
      }
    }
  } 
}

function isKingChecked() 
{  
  let kingPosition = getKingPosition(currentPlayerColor.value);
  if (!kingPosition) {
      console.log("No king found for the current player.");
      return false;
  }
  let isKingChecked = isFieldChecked(kingPosition.colIndex, kingPosition.rowIndex)
  if(isKingChecked)
  {
    currentlyChecked.value = {row : kingPosition.rowIndex, col : kingPosition.colIndex}
    return true
  }
}

function isFieldChecked(checkedColIndex, checkedRowIndex) 
{
  for (let colIndex = 0; colIndex < chessBoard.length; colIndex++) {
    for (let rowIndex = 0; rowIndex < chessBoard[colIndex].length; rowIndex++) {
      let piece = chessBoard[colIndex][rowIndex];
      // Jeśli to figura przeciwnika
      if(piece.type != null && chessPieceColors[piece.color] !== currentPlayerColor.value){  
        let attackMoves = GetAttackSquares(colIndex, rowIndex, piece.type, piece.color);    
        for (let move of attackMoves) {     
          if (move.colIndex == checkedColIndex && move.rowIndex == checkedRowIndex) {                        
            return true;
          }
        }            
      }
    }
  }
  return false; // Nie ma szacha
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

.red{
  background-color: #F33E42 !important;
}

.dark {
  background-color: #b16e41;
}

.container {
  width: 100%;
  height: 85%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.currentPlayer
{
  color:white;
  height: 15%;
  font-size: 4rem;
  align-items: center;
  display: flex;
  justify-content: center;
}
.pawnUpgrade {
  background-color: gray;
  box-shadow: 5px 5px 5px black;
}

.pawn-upgrade-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 10;
  display: flex;
  flex-direction: column;
  justify-content: top;
  align-items: center;  
}

.cancelPawnUpgrade
{
  width: 40px;
  height: 20px;
  align-items: center;
  display: flex;
  justify-content: center;
  box-shadow: 5px 5px 5px black;  
  background-color: rgb(114, 114, 114);
  cursor: pointer;
}

</style>
