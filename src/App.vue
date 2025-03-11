<script>
import TetrisBoard from './components/TetrisBoard.vue';
import NextPiece from './components/NextPiece.vue';
import HoldPiece from './components/HoldPiece.vue';

export default {
  components: {
    TetrisBoard,
    NextPiece,
    HoldPiece
  },
  data() {
    return {
      score: 0,
      level: 1,
      lines: 0,
      board: [],
      currentPiece: null,
      nextPiece: null,
      holdPiece: null,
      canHold: true,
      isPlaying: false,
      isPaused: false,
      gameInterval: null,
      gameSpeed: 1000, // milliseconds
      boardWidth: 10,
      boardHeight: 20,
      bag: [],
      pieces: [
        // I piece
        {
          shape: [[1, 1, 1, 1]],
          color: 'cyan',
          type: 'I'
        },
        // J piece
        {
          shape: [[1, 0, 0], [1, 1, 1]],
          color: 'blue',
          type: 'J'
        },
        // L piece
        {
          shape: [[0, 0, 1], [1, 1, 1]],
          color: 'orange',
          type: 'L'
        },
        // O piece
        {
          shape: [[1, 1], [1, 1]],
          color: 'yellow',
          type: 'O'
        },
        // S piece
        {
          shape: [[0, 1, 1], [1, 1, 0]],
          color: 'green',
          type: 'S'
        },
        // T piece
        {
          shape: [[0, 1, 0], [1, 1, 1]],
          color: 'purple',
          type: 'T'
        },
        // Z piece
        {
          shape: [[1, 1, 0], [0, 1, 1]],
          color: 'red',
          type: 'Z'
        }
      ]
    };
  },
  methods: {
    initBoard() {
      this.board = Array(this.boardHeight).fill().map(() => Array(this.boardWidth).fill(0));
    },
    startGame() {
      this.initBoard();
      this.score = 0;
      this.level = 1;
      this.lines = 0;
      this.isPlaying = true;
      this.isPaused = false;
      this.bag = [];
      this.holdPiece = null;
      this.canHold = true;
      this.spawnNewPiece();
      this.gameLoop();
    },
    pauseGame() {
      this.isPaused = true;
      clearInterval(this.gameInterval);
    },
    resumeGame() {
      this.isPaused = false;
      this.gameLoop();
    },
    gameLoop() {
      clearInterval(this.gameInterval);
      this.gameInterval = setInterval(() => {
        this.moveDown();
      }, this.gameSpeed);
    },
    getRandomPieceFromBag() {
      // 가방이 비어있으면 새로운 가방 생성
      if (this.bag.length === 0) {
        this.refillBag();
      }
      
      // 가방에서 무작위로 한 조각 선택
      const randomIndex = Math.floor(Math.random() * this.bag.length);
      const piece = this.bag[randomIndex];
      
      // 선택된 조각을 가방에서 제거
      this.bag.splice(randomIndex, 1);
      
      return JSON.parse(JSON.stringify(piece)); // 깊은 복사
    },
    refillBag() {
      // 7개의 모든 조각을 포함하는 새 가방 생성
      this.bag = JSON.parse(JSON.stringify(this.pieces)); // 깊은 복사
    },
    spawnNewPiece() {
      if (!this.nextPiece) {
        this.nextPiece = this.getRandomPieceFromBag();
      }
      
      this.currentPiece = this.nextPiece;
      this.nextPiece = this.getRandomPieceFromBag();
      
      // 현재 피스 시작 위치 설정
      this.currentPiece.x = Math.floor((this.boardWidth - this.currentPiece.shape[0].length) / 2);
      this.currentPiece.y = 0;
      
      // 게임 오버 체크
      if (this.checkCollision()) {
        this.gameOver();
      }
    },
    holdCurrentPiece() {
      if (!this.canHold) return;
      
      const currentPiece = JSON.parse(JSON.stringify(this.currentPiece));
      
      // 타입만 유지하고 위치 정보 초기화
      currentPiece.x = 0;
      currentPiece.y = 0;
      
      if (this.holdPiece === null) {
        // 홀드 슬롯이 비어있는 경우, 현재 조각을 홀드하고 새 조각 생성
        this.holdPiece = currentPiece;
        this.spawnNewPiece();
      } else {
        // 홀드 슬롯에 이미 조각이 있는 경우, 교체
        const temp = this.holdPiece;
        this.holdPiece = currentPiece;
        this.currentPiece = temp;
        
        // 교체된 조각 위치 설정
        this.currentPiece.x = Math.floor((this.boardWidth - this.currentPiece.shape[0].length) / 2);
        this.currentPiece.y = 0;
        
        // 홀드 후 게임 오버 체크
        if (this.checkCollision()) {
          this.gameOver();
        }
      }
      
      // 홀드 후에는 이 조각을 떨어뜨릴 때까지 다시 홀드할 수 없음
      this.canHold = false;
    },
    moveDown() {
      this.currentPiece.y++;
      if (this.checkCollision()) {
        this.currentPiece.y--;
        this.lockPiece();
        this.clearLines();
        this.canHold = true; // 새 조각이 나올 때 홀드 가능하도록 초기화
        this.spawnNewPiece();
      }
    },
    checkCollision() {
      for (let y = 0; y < this.currentPiece.shape.length; y++) {
        for (let x = 0; x < this.currentPiece.shape[y].length; x++) {
          if (this.currentPiece.shape[y][x]) {
            const boardX = this.currentPiece.x + x;
            const boardY = this.currentPiece.y + y;
            
            // 범위를 벗어났는지 확인
            if (
              boardX < 0 || 
              boardX >= this.boardWidth || 
              boardY >= this.boardHeight || 
              (boardY >= 0 && this.board[boardY][boardX])
            ) {
              return true;
            }
          }
        }
      }
      return false;
    },
    lockPiece() {
      for (let y = 0; y < this.currentPiece.shape.length; y++) {
        for (let x = 0; x < this.currentPiece.shape[y].length; x++) {
          if (this.currentPiece.shape[y][x]) {
            const boardX = this.currentPiece.x + x;
            const boardY = this.currentPiece.y + y;
            
            if (boardY >= 0) {
              this.board[boardY][boardX] = this.currentPiece.color;
            }
          }
        }
      }
    },
    clearLines() {
      let linesCleared = 0;
      
      for (let y = 0; y < this.boardHeight; y++) {
        if (this.board[y].every(cell => cell !== 0)) {
          // 줄 제거
          this.board.splice(y, 1);
          // 새 줄 추가
          this.board.unshift(Array(this.boardWidth).fill(0));
          linesCleared++;
        }
      }
      
      if (linesCleared > 0) {
        // 점수 계산
        this.lines += linesCleared;
        switch (linesCleared) {
          case 1: this.score += 100 * this.level; break;
          case 2: this.score += 300 * this.level; break;
          case 3: this.score += 500 * this.level; break;
          case 4: this.score += 800 * this.level; break;
        }
        
        // 레벨 업
        this.level = Math.floor(this.lines / 10) + 1;
        // 게임 속도 업데이트
        this.gameSpeed = Math.max(100, 1000 - (this.level - 1) * 100);
        if (this.isPlaying && !this.isPaused) {
          this.gameLoop();
        }
      }
    },
    gameOver() {
      this.isPlaying = false;
      clearInterval(this.gameInterval);
      alert(`Game Over! 최종 점수: ${this.score}`);
    },
    rotateClockwise() {
      // 회전 전 상태 저장
      const oldShape = this.currentPiece.shape;
      const oldX = this.currentPiece.x;
      
      // 행렬 회전 (시계 방향)
      const rows = this.currentPiece.shape.length;
      const cols = this.currentPiece.shape[0].length;
      
      let newShape = Array(cols).fill().map(() => Array(rows).fill(0));
      
      for (let y = 0; y < rows; y++) {
        for (let x = 0; x < cols; x++) {
          newShape[x][rows - 1 - y] = this.currentPiece.shape[y][x];
        }
      }
      
      this.currentPiece.shape = newShape;
      
      // 회전 후 충돌 체크
      if (this.checkCollision()) {
        // 충돌하면 원상복구
        this.currentPiece.shape = oldShape;
        this.currentPiece.x = oldX;
      }
    },
    rotateCounterClockwise() {
      // 회전 전 상태 저장
      const oldShape = this.currentPiece.shape;
      const oldX = this.currentPiece.x;
      
      // 행렬 회전 (반시계 방향)
      const rows = this.currentPiece.shape.length;
      const cols = this.currentPiece.shape[0].length;
      
      let newShape = Array(cols).fill().map(() => Array(rows).fill(0));
      
      for (let y = 0; y < rows; y++) {
        for (let x = 0; x < cols; x++) {
          newShape[cols - 1 - x][y] = this.currentPiece.shape[y][x];
        }
      }
      
      this.currentPiece.shape = newShape;
      
      // 회전 후 충돌 체크
      if (this.checkCollision()) {
        // 충돌하면 원상복구
        this.currentPiece.shape = oldShape;
        this.currentPiece.x = oldX;
      }
    }
  },
  mounted() {
    this.initBoard();
    
    // 키보드 이벤트 리스너 등록
    window.addEventListener('keydown', (e) => {
      if (!this.isPlaying || this.isPaused) return;
      
      switch (e.key) {
        case 'ArrowLeft':
          this.currentPiece.x--;
          if (this.checkCollision()) {
            this.currentPiece.x++;
          }
          break;
        case 'ArrowRight':
          this.currentPiece.x++;
          if (this.checkCollision()) {
            this.currentPiece.x--;
          }
          break;
        case 'ArrowUp':
          // 회전 - 우측 회전으로 설정
          this.rotateClockwise();
          break;
        case 'ArrowDown':
          this.moveDown();
          break;
        case ' ':
          // 하드 드롭
          while (!this.checkCollision()) {
            this.currentPiece.y++;
          }
          this.currentPiece.y--;
          this.lockPiece();
          this.clearLines();
          this.canHold = true; // 새 조각이 나올 때 홀드 가능하도록 초기화
          this.spawnNewPiece();
          break;
        case 'c':
        case 'C':
          // 홀드 기능
          this.holdCurrentPiece();
          break;
        case 'z':
        case 'Z':
          // 왼쪽(반시계) 회전
          this.rotateCounterClockwise();
          break;
        case 'x':
        case 'X':
          // 오른쪽(시계) 회전
          this.rotateClockwise();
          break;
      }
    });
  }
};
</script>

<template>
  <div class="tetris-container">
    <h1>Vue Tetris</h1>
    <div class="game-info">
      <div class="score">Score: {{ score }}</div>
      <div class="level">Level: {{ level }}</div>
      <div class="lines">Lines: {{ lines }}</div>
    </div>
    <div class="game-container">
      <HoldPiece :piece="holdPiece" :canHold="canHold" />
      <TetrisBoard :board="board" />
      <NextPiece :piece="nextPiece" />
    </div>
    <div class="controls">
      <button @click="startGame" :disabled="isPlaying">Start Game</button>
      <button @click="pauseGame" :disabled="!isPlaying || isPaused">Pause</button>
      <button @click="resumeGame" :disabled="!isPaused">Resume</button>
    </div>
    <div class="instructions">
      <h3>Controls:</h3>
      <p>← → : Move left/right</p>
      <p>↑ : Rotate clockwise</p>
      <p>Z : Rotate counter-clockwise</p>
      <p>X : Rotate clockwise</p>
      <p>↓ : Soft drop</p>
      <p>Space : Hard drop</p>
      <p>C : Hold piece</p>
    </div>
  </div>
</template>

<style scoped>
.tetris-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
}

h1 {
  margin-bottom: 10px;
  color: #333;
}

.game-info {
  display: flex;
  gap: 20px;
  margin-bottom: 10px;
  font-size: 18px;
}

.game-container {
  display: flex;
  align-items: flex-start;
  gap: 20px;
}

.controls {
  display: flex;
  gap: 10px;
  margin: 10px 0;
}

button {
  padding: 8px 16px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.instructions {
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.instructions h3 {
  margin-bottom: 10px;
}

.instructions p {
  margin: 5px 0;
}
</style>
