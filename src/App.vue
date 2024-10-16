<template>
  <div class="container">
    <div id="gameboard">

    <div v-for="(col,colIndex) in chessBoard" class="row" :key="colIndex">
      <div  style="color:red" v-for="(piece,rowIndex) in col" v-bind:class = "(rowIndex%2===0 + colIndex%2)?'light':'dark'" class="square"  :key="rowIndex">   
        <font-awesome-icon :icon="piece.piece" size="2x" :color="piece.color" />                
      </div>
    </div>
    </div>
  </div>
</template>



<script setup>
// let chessPieces = {
//     pawn: 1,
//     rook: 2,
//     knight: 3,
//     bishop: 4,
//     queen: 5,
//     king: 6
// };



let chessBoard = [
    [{piece: 'chess-rook', color: 'white'}, {piece: 'chess-pawn', color: 'white'}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: 'chess-pawn', color: 'black'}, {piece: 'chess-rook', color: 'black'}],
    [{piece: 'chess-knight', color: 'white'}, {piece: 'chess-pawn', color: 'white'}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: 'chess-pawn', color: 'black'}, {piece: 'chess-knight', color: 'black'}],
    [{piece: 'chess-bishop', color: 'white'}, {piece: 'chess-pawn', color: 'white'}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: 'chess-pawn', color: 'black'}, {piece: 'chess-bishop', color: 'black'}],
    [{piece: 'chess-queen', color: 'white'}, {piece: 'chess-pawn', color: 'white'}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: 'chess-pawn', color: 'black'}, {piece: 'chess-king', color: 'black'}],
    [{piece: 'chess-king', color: 'white'}, {piece: 'chess-pawn', color: 'white'}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: 'chess-pawn', color: 'black'}, {piece: 'chess-queen', color: 'black'}],
    [{piece: 'chess-bishop', color: 'white'}, {piece: 'chess-pawn', color: 'white'}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: 'chess-pawn', color: 'black'}, {piece: 'chess-bishop', color: 'black'}],
    [{piece: 'chess-knight', color: 'white'}, {piece: 'chess-pawn', color: 'white'}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: 'chess-pawn', color: 'black'}, {piece: 'chess-knight', color: 'black'}],
    [{piece: 'chess-rook', color: 'white'}, {piece: 'chess-pawn', color: 'white'}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: null, color: null}, {piece: 'chess-pawn', color: 'black'}, {piece: 'chess-rook', color: 'black'}]
];
</script>
<style scoped>
#gameboard{
  width: 320px;
  height: 320px;
  background-color: red;
  display:flex;
  flex-wrap: wrap;
}

.square{
  display: flex;
  align-items: center;
  justify-content: center;
  height: 40px;
  width: 40px;
}

.whitePiece{
  color: white;
}

.blackPiece{
  color:black
}

.light{
  background-color: beige;
}

.dark{
  background-color: gray;
}

.container {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
