<template>
  <div class="container">
    <div id="gameboard"></div>
  </div>
</template>



<script setup>


import { onMounted } from 'vue'

onMounted(() => {
  drawBoard();
})


function drawBoard(){
  const square = document.createElement('div');
  square.classList.add(square);
  square.class.add('light')
  document.getElementById("#gameboard");
}

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
  height: 40px;
  width: 40px;
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
