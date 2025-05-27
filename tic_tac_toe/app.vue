<template>
  <div class="tictactoe-container">
    <div class="status-bar">
      <!-- Status bar: show game state (turn/winner/draw) -->
      <span v-if="winner">
        Winner: <span :class="winner === 'X' ? 'x-color' : 'o-color'">{{ winner }}</span>
      </span>
      <span v-else-if="isDraw">Draw!</span>
      <span v-else>
        Turn: <span :class="currentPlayer === 'X' ? 'x-color' : 'o-color'">{{ currentPlayer }}</span>
      </span>
    </div>
    <div class="board">
      <div
        class="cell"
        v-for="(cell, idx) in board"
        :key="idx"
        @click="handleCellClick(idx)"
        :class="{
          'cell-x': cell === 'X',
          'cell-o': cell === 'O',
          clickable: !winner && !cell && !isDraw
        }"
      >
        <span v-if="cell" :class="cell === 'X' ? 'x-color' : 'o-color'">{{ cell }}</span>
      </div>
    </div>
    <button class="restart-btn" @click="restartGame">Restart</button>
  </div>
</template>

<script setup lang="ts">
// PUBLIC_INTERFACE
import { ref, computed } from 'vue'

/**
 * State for the 3x3 TicTacToe board
 */
const board = ref<(string | null)[]>(Array(9).fill(null))
/**
 * 'X' starts first
 */
const currentPlayer = ref<'X' | 'O'>('X')

/**
 * Winner state. Null when no winner yet, otherwise 'X' or 'O'.
 */
const winner = ref<string | null>(null)
/**
 * Flag indicating draw status
 */
const isDraw = computed(() => !winner.value && board.value.every(cell => cell))

/**
 * PUBLIC_INTERFACE
 * Handles click events on a cell. Only allows marking if the cell is empty and the game is not over.
 * @param idx Index of the clicked cell
 */
function handleCellClick(idx: number) {
  // Don't allow cell click if already occupied or game finished
  if (board.value[idx] || winner.value || isDraw.value) return
  // Mark cell
  board.value[idx] = currentPlayer.value
  // Check for win after move
  if (checkWinner()) {
    winner.value = currentPlayer.value
  }
  // Switch player if game not over
  else if (!isDraw.value) {
    currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
  }
}

/**
 * PUBLIC_INTERFACE
 * Checks all possible winning combinations for a winner.
 * @returns true if a win is detected, false otherwise
 */
function checkWinner(): boolean {
  const lines = [
    [0,1,2],[3,4,5],[6,7,8], // Rows
    [0,3,6],[1,4,7],[2,5,8], // Columns
    [0,4,8],[2,4,6]          // Diagonals
  ]
  for (const [a, b, c] of lines) {
    if (
      board.value[a] &&
      board.value[a] === board.value[b] &&
      board.value[a] === board.value[c]
    ) {
      return true
    }
  }
  return false
}

/**
 * PUBLIC_INTERFACE
 * Resets the board and state for a new game
 */
function restartGame() {
  board.value = Array(9).fill(null)
  winner.value = null
  currentPlayer.value = 'X'
}
</script>

<style scoped>
:root {
  --primary-bg: #ffffff;
  --secondary: #000000;
  --accent: #2196f3;
  --cell-size: 80px;
  --cell-gap: 12px;
}

.tictactoe-container {
  min-height: 100vh;
  background: var(--primary-bg);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.status-bar {
  margin-bottom: 28px;
  font-size: 1.4em;
  font-weight: 600;
  color: var(--secondary);
  text-align: center;
  min-height: 2em;
}

.status-bar .x-color {
  color: var(--accent);
}
.status-bar .o-color {
  color: var(--secondary);
  text-shadow: 0 0 1px var(--accent);
}

.board {
  display: grid;
  grid-template-columns: repeat(3, var(--cell-size));
  grid-template-rows: repeat(3, var(--cell-size));
  gap: var(--cell-gap);
  background: var(--accent);
  padding: var(--cell-gap);
  border-radius: 12px;
  margin-bottom: 32px;
}

.cell {
  width: var(--cell-size);
  height: var(--cell-size);
  background: var(--primary-bg);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2.6em;
  font-weight: bold;
  color: var(--secondary);
  cursor: default;
  user-select: none;
  border: 2px solid var(--accent);
  transition: background 0.1s;
}

.cell.clickable:hover {
  background: #e3f1fc;
  cursor: pointer;
}

.cell-x {
  color: var(--accent);
}

.cell-o {
  color: var(--secondary);
  text-shadow: 0 0 2px var(--accent);
}

.restart-btn {
  margin-top: 18px;
  padding: 10px 26px;
  font-size: 1.1em;
  font-weight: 500;
  color: var(--primary-bg);
  background-color: var(--accent);
  border: none;
  border-radius: 6px;
  cursor: pointer;
  outline: none;
  letter-spacing: 0.04em;
  transition: background 0.16s;
}

.restart-btn:hover {
  background-color: #176ab6;
}
</style>
