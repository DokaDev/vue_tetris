<template>
  <div class="next-piece-container">
    <h3>Next Piece</h3>
    <div class="next-piece">
      <div v-if="piece" class="piece">
        <div v-for="(row, rowIndex) in piece.shape" :key="rowIndex" class="row">
          <div 
            v-for="(cell, colIndex) in row" 
            :key="`${rowIndex}-${colIndex}`" 
            class="cell"
            :class="{ filled: cell !== 0 }"
            :style="{ backgroundColor: cell !== 0 ? piece.color : '' }"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    piece: {
      type: Object,
      required: false,
      default: null
    }
  }
};
</script>

<style scoped>
.next-piece-container {
  margin-top: 10px;
  text-align: center;
  width: 100px;
}

h3 {
  margin-bottom: 10px;
}

.next-piece {
  display: inline-block;
  padding: 10px;
  background-color: #222;
  border: 2px solid #333;
  min-height: 70px;
  min-width: 80px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.row {
  display: flex;
}

.cell {
  width: 20px;
  height: 20px;
  border: 1px solid #333;
  box-sizing: border-box;
}

.filled {
  border: 1px solid rgba(255, 255, 255, 0.5);
}
</style> 