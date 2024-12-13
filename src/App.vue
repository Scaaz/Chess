<template>  
  
  <div class="currentPlayer">   {{ currentPlayerColor }}'s turn</div>
  <div class="container">
    
    <div id="gameboard">     
      <EndgameModal :isOpen="modalIsOpen" :result="textbox" @modalClose="resetGame()"></EndgameModal> 
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
import './assets/style.css'
import { startingChessboard, chessPieceColors, chessPieceIcons, KingMoves, KnightMoves, QueenMoves, bishopMoves, rookMoves, blackPawnMoves, whitePawnMoves  } from '@/data.vue'
import  EndgameModal  from '@/components/EndgameModal.vue'

let chessBoard = structuredClone(startingChessboard);
const currentlySelected = ref({col:Number,row:Number})
watchEffect(currentlySelected)

const currentlyChecked = ref({col:Number,row:Number})
watchEffect(currentlyChecked)

const highlightedArray = ref([]);
watchEffect(highlightedArray)

const castleArray = ref([]);
watchEffect(castleArray)

const pawnArray = ref([]);
watchEffect(pawnArray)

const modalIsOpen = ref(false);
const textbox = ref("string");
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

function resetGame()
{
  currentPlayerColor.value = chessPieceColors[0];
  highlightedArray.value = [];
  castleArray.value = [];
  pawnArray.value = [];
  upgradeColIndex.value = null;
  removeEnPassantFakes(chessPieceColors[0]);
  removeEnPassantFakes(chessPieceColors[1]);
  chessBoard = structuredClone(startingChessboard);
  currentlyChecked.value = {row : null, col : null};
  modalIsOpen.value = false;
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


function GetMoveSquares(colIndex, rowIndex, pieceType, pieceColor, usedChessBoard = chessBoard) {
  return GetSquaresCore(colIndex, rowIndex, pieceType, pieceColor,0, usedChessBoard)
}

function GetAttackSquares(colIndex, rowIndex, pieceType, pieceColor, usedChessBoard = chessBoard) {
  return GetSquaresCore(colIndex, rowIndex, pieceType, pieceColor,1, usedChessBoard)
}

function GetSquaresCore(colIndex, rowIndex, pieceType, pieceColor,action, usedChessBoard = chessBoard) {
    let array = [];    
    let iterations = 1;
    let pieceMoves = [];
    switch(pieceType)
    {
      case 1:
        if(action == 1)
        {
          //attack
          return pawnAttack(rowIndex, colIndex, pieceColor)
        }
        else
        {
          //move
          return movePawn(rowIndex, colIndex, pieceColor)
        }
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
      if (usedChessBoard[newCol][newRow].color === pieceColor) break; // same color piece
      array.push({colIndex: newCol,  rowIndex: newRow});
      if (usedChessBoard[newCol][newRow].type != null) break; // stop if piece found
    }
  });
    return array;
}

function markPawnDoubleMove(rowIndex, colIndex, pieceColor){
  let move = whitePawnMoves;
  if(pieceColor === 1)
  {
    move = blackPawnMoves;
  }
  
  let newRow = rowIndex + move.row
  let newCol = colIndex + move.col 

  if(pieceColor == 0 && rowIndex == 1)
  {
    newRow = newRow+1;
  }
  else if(pieceColor == 1 && rowIndex == 6)
  {
    newRow = newRow-1;
  }

  if (newRow < 0 || newRow > 7 || newCol < 0 || newCol > 7) return []; // outside board
  if (chessBoard[newCol][newRow].color === pieceColor) return []; // same color piece
  if (chessBoard[newCol][newRow].type != null) return []; // stop if piece found  

  return {colIndex: newCol,  rowIndex: newRow} 
}

function movePawn(rowIndex, colIndex, pieceColor) {  
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
  
  let attack = pawnAttack(rowIndex,colIndex, pieceColor)
  array = attack.concat(pawnArray.value)//add attack squares
  array.push({colIndex: newCol,  rowIndex: newRow})
  return array;  
}

function pawnAttack(rowIndex, colIndex, pieceColor) {  
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

function movePiece(targetcol, targetrow){ 
  chessBoard[targetcol] [targetrow] = chessBoard[currentlySelected.value.col][currentlySelected.value.row]
  chessBoard[currentlySelected.value.col][currentlySelected.value.row] = {type: null, color: null}
}

function movePieceToTarget(colIndex, rowIndex, targetcol, targetrow){ 
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
  currentlyChecked.value = {row : null, col : null}
  let isChecked = isKingChecked(chessBoard);
  let canPlayerMove = CanPlayerMove(chessBoard)
  if(isChecked)
  {
    let kingPosition = getKingPosition(currentPlayerColor.value,chessBoard);
    currentlyChecked.value = {row : kingPosition.rowIndex, col : kingPosition.colIndex}
    if(!canPlayerMove)
    {
      textbox.value = "CHECKMATE"
      modalIsOpen.value = true;
    }
  }
else if (!canPlayerMove)
{
  textbox.value = "STALEMATE"
  modalIsOpen.value = true;
}
}

function CanPlayerMove(usedChessBoard)
{
  for (let colIndex = 0; colIndex < usedChessBoard.length; colIndex++) {
    for (let rowIndex = 0; rowIndex < usedChessBoard[colIndex].length; rowIndex++) {
      let piece = usedChessBoard[colIndex][rowIndex];
      // if the player piece
      if(piece.type != null && chessPieceColors[piece.color] === currentPlayerColor.value){  
        let moveSquares = GetMoveSquares(colIndex, rowIndex, piece.type, piece.color, usedChessBoard);
        let indexesToRemove = [];
      for(let [index, currentSquare] of moveSquares.entries())
      {
        //cancel moves that would make u checked
        let fakeChessBoard = structuredClone(chessBoard);//deepclone
        fakeChessBoard[colIndex][rowIndex] = {type: null, color: null}
        fakeChessBoard[currentSquare.colIndex ][currentSquare.rowIndex] = {type: piece.type, color: piece.color}
        let wouldKingBeChecked = isKingChecked(fakeChessBoard);
        if(wouldKingBeChecked)
        {
          indexesToRemove.push(index);   
        }
      }
      let moveSquaresFixed = structuredClone(moveSquares);
  indexesToRemove.sort((a, b) => b - a);
  for(let id of indexesToRemove)
  {
    moveSquaresFixed.splice(id,1);
  }
      if(moveSquaresFixed.length > 0)
      {
        return true;
      }                        
      }
    }
  }
  return false;
}

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
  pawnArray.value = [];
  let moveSquares = GetMoveSquares(colIndex, rowIndex, pieceType, pieceColor)
  if(pieceType == 1){
    let extraSquare = markPawnDoubleMove(rowIndex,colIndex, pieceColor);
    if(extraSquare)
    {
      moveSquares = moveSquares.concat(extraSquare); //todo: creating new vs modifying existing
    }
  }
  let indexesToRemove =[];
  for(let [index, currentSquare] of moveSquares.entries())
  {
    //cancel moves that would make u checked
    let fakeChessBoard = structuredClone(chessBoard);//deepclone
    fakeChessBoard[colIndex][rowIndex] = {type: null, color: null}
    fakeChessBoard[currentSquare.colIndex ][currentSquare.rowIndex] = {type: pieceType, color: pieceColor}
    let wouldKingBeChecked = isKingChecked(fakeChessBoard);
    if(wouldKingBeChecked)
    {
      indexesToRemove.push(index);   
    }
  }
  
  let moveSquaresFixed = structuredClone(moveSquares);
  indexesToRemove.sort((a, b) => b - a);
  for(let id of indexesToRemove)
  {
    moveSquaresFixed.splice(id,1);
  }
  highlightedArray.value = moveSquaresFixed;

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
     pawnArray.value = []
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

function getKingPosition(playerColor, usedChessBoard)
{    
  for(let colIndex in usedChessBoard){
    for(let rowIndex in usedChessBoard){
      let piece = usedChessBoard[colIndex][rowIndex];
      if (piece.type === 6 && chessPieceColors[piece.color] === playerColor) {
        return  {colIndex, rowIndex}               
      }
    }
  } 
}

function isKingChecked(usedChessBoard) 
{  
  let kingPosition = getKingPosition(currentPlayerColor.value, usedChessBoard);
  if (!kingPosition) {
      console.log("No king found for the current player.");
      return true;
  }
  let isKingChecked = isFieldChecked(kingPosition.colIndex, kingPosition.rowIndex,usedChessBoard);
  if(isKingChecked)
  {   
    return true;
  }
  return false;
}

function isFieldChecked(checkedColIndex, checkedRowIndex, usedChessBoard = chessBoard) 
{
  for (let colIndex = 0; colIndex < usedChessBoard.length; colIndex++) {
    for (let rowIndex = 0; rowIndex < usedChessBoard[colIndex].length; rowIndex++) {
      let piece = usedChessBoard[colIndex][rowIndex];
      // if opponent piece
      if(piece.type != null && chessPieceColors[piece.color] !== currentPlayerColor.value){  
        let attackMoves = GetAttackSquares(colIndex, rowIndex, piece.type, piece.color, usedChessBoard);    
        for (let move of attackMoves) {     
          if (move.colIndex == checkedColIndex && move.rowIndex == checkedRowIndex) {                        
            return true;
          }
        }            
      }
    }
  }
  return false;
}
</script>
