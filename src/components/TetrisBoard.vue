<template>
  <div class="tetris-board">
    <div v-for="(row, rowIndex) in displayBoard" :key="rowIndex" class="row">
      <div 
        v-for="(cell, colIndex) in row" 
        :key="`${rowIndex}-${colIndex}`" 
        class="cell"
        :class="{ filled: cell !== 0 }"
        :style="{ backgroundColor: cell !== 0 ? cell : '' }"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    board: {
      type: Array,
      required: true
    },
    currentPiece: {
      type: Object,
      required: false,
      default: null
    }
  },
  computed: {
    displayBoard() {
      // 현재 보드 상태에 현재 조각을 추가하여 표시
      if (!this.currentPiece) {
        return this.board;
      }
      
      // 보드 복사
      const displayBoard = this.board.map(row => [...row]);
      
      // 현재 조각 추가
      const piece = this.currentPiece;
      if (piece) {
        for (let y = 0; y < piece.shape.length; y++) {
          for (let x = 0; x < piece.shape[y].length; x++) {
            if (piece.shape[y][x]) {
              const boardX = piece.x + x;
              const boardY = piece.y + y;
              
              if (boardY >= 0 && boardY < displayBoard.length &&
                  boardX >= 0 && boardX < displayBoard[0].length) {
                displayBoard[boardY][boardX] = piece.color;
              }
            }
          }
        }
      }
      
      return displayBoard;
    }
  }
};
</script>

<style scoped>
.tetris-board {
  border: 2px solid #333;
  background-color: #111;
  display: inline-block;
}

.row {
  display: flex;
}

.cell {
  width: 25px;
  height: 25px;
  border: 1px solid #333;
  box-sizing: border-box;
}

.filled {
  border: 1px solid rgba(255, 255, 255, 0.5);
}
</style> 
