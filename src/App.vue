<template>
  <div class="container">
    <div id="gameboard">

    <div v-for="(col,colIndex) in chessBoard" class="row" :key="colIndex">
      <div  style="color:red" v-for="(piece,rowIndex) in col" v-bind:class = "(rowIndex%2===0 + colIndex%2)?'light':'dark'" class="square"  :key="rowIndex">   
        <font-awesome-icon :icon="chessPieceIcons[piece.type]" size="2x" :color="chessPieceColors[piece.color]" />  

      </div>
    </div>
    </div>
  </div>
</template>



<script setup>
let chessPieceIcons = {
  1:'chess-pawn',
  2:'chess-rook',
  3:'chess-knight',
  4:'chess-bishop',
  5:'chess-queen',
  6:'chess-king'
};

let chessPieceColors = {
0: 'white',
1: 'black'
}

let chessBoard = [
    [{type: 2, color: 0}, {type: 1, color: 0}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}],
    [{type: 3, color: 0}, {type: 1, color: 0}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
    [{type: 4, color: 0}, {type: 1, color: 0}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
    [{type: 5, color: 0}, {type: 1, color: 0}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {type: 1, color: 1}, {type: 6, color: 1}],
    [{type: 6, color: 0}, {type: 1, color: 0}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {type: 1, color: 1}, {type: 5, color: 1}],
    [{type: 4, color: 0}, {type: 1, color: 0}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {type: 1, color: 1}, {type: 4, color: 1}],
    [{type: 3, color: 0}, {type: 1, color: 0}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {type: 1, color: 1}, {type: 3, color: 1}],
    [{type: 2, color: 0}, {type: 1, color: 0}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {icon: null, color: null}, {type: 1, color: 1}, {type: 2, color: 1}]
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
