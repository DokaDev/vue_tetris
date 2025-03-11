<template>
  <div class="hold-piece-container">
    <h3>Hold (C)</h3>
    <div class="hold-piece" :class="{ 'disabled': !canHold }">
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
      <div v-else class="empty-hold">
        Empty
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
    },
    canHold: {
      type: Boolean,
      required: true,
      default: true
    }
  }
};
</script>

<style scoped>
.hold-piece-container {
  margin-top: 10px;
  text-align: center;
  width: 100px;
}

h3 {
  margin-bottom: 10px;
}

.hold-piece {
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

.disabled {
  opacity: 0.5;
  position: relative;
}

.disabled:after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.3);
}

.empty-hold {
  color: #999;
  font-size: 14px;
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