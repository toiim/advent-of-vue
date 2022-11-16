<script setup lang="ts">
import { computed, ref } from 'vue'

type Symbol = '◯' | '╳'
type Position = undefined | Symbol
type Row = [Position, Position, Position]
type Grid = [Row, Row, Row]
interface Move {
  row: 0 | 1 | 2
  position: 0 | 1 | 2
  symbol: Symbol
}

const moves = ref<Move[]>([])

const currentTurn = computed<Symbol>(() => {
  if (!moves.value.length) {
    return '╳'
  } else {
    const lastMoved = moves.value[moves.value.length - 1].symbol
    return lastMoved === '╳' ? '◯' : '╳'
  }
})

const grid = computed<Grid>(() => {
  const grid: Grid = [
    [undefined, undefined, undefined],
    [undefined, undefined, undefined],
    [undefined, undefined, undefined]
  ]
  moves.value.forEach(move => {
    if (move) {
      grid[move.row][move.position] = move.symbol
    }
  })
  return grid
})

const attemptMove = (row: 0 | 1 | 2, position: 0 | 1 | 2) => {
  const spaceIsEmpty = grid.value[row][position] === undefined
  if (spaceIsEmpty && !gameOver.value) {
    moves.value.push(
      {
        row,
        position,
        symbol: currentTurn.value
      }
    )
    undoneMove.value = undefined
  }
}

const undoneMove = ref<undefined | Move>(undefined);

const undoMove = () => {
  if (moves.value.length) {
    undoneMove.value = moves.value.pop()
  }
}
const redoMove = () => {
  if (undoneMove.value !== undefined) {
    moves.value.push(undoneMove.value as Move)
    undoneMove.value = undefined
  }
}

const gameOver = computed<boolean>(() => {
  if (didWin.value) { return true }
  else if (moves.value.length === 9) { return true }
  else {
    return false
  }
})

const didWin = computed<Symbol | false>(() => {

  const transpose = (matrix: any[][]) => {
    let [row] = matrix
    return row.map((value, column) => matrix.map(row => row[column]))
  }

  const matrixIncludesCharacter = (array: any[][], character: string): boolean => {
    return array.map((row) => row.every((v: any) => v === character)).includes(true)
  }

  const gridAsColumns = transpose(grid.value)
  const horizontalAxes = [[gridAsColumns[0][0], gridAsColumns[1][1], gridAsColumns[2][2]], [gridAsColumns[0][2], gridAsColumns[1][1], gridAsColumns[2][0]]]

  const containsRowOfO = matrixIncludesCharacter(grid.value, '◯')
  const containsRowOfX = matrixIncludesCharacter(grid.value, '╳')

  const containsColumnOfO = matrixIncludesCharacter(gridAsColumns, '◯')
  const containsColumnOfX = matrixIncludesCharacter(gridAsColumns, '╳')

  const containsHorizontalOfO = matrixIncludesCharacter(horizontalAxes, '◯')
  const containsHorizontalOfX = matrixIncludesCharacter(horizontalAxes, '╳')

  if (containsRowOfO || containsColumnOfO || containsHorizontalOfO) return '◯'
  if (containsRowOfX || containsColumnOfX || containsHorizontalOfX) return '╳'
  else return false
})

const clearGameBoard = () => {
  moves.value = []
}

</script>

<template>
  <div class="container">
    <div id="grid" :class="{ 'game-over': gameOver }">
      <div v-for="(row, rowIndex) in grid" :key="rowIndex">
        <div @click="attemptMove(rowIndex, positionIndex)" v-for="(position, positionIndex) in row"
          :key="positionIndex">
          {{ position }}
        </div>
      </div>
    </div>

    <div id="controls">
      <button @click="undoMove">Undo</button>
      <button @click="redoMove">Redo</button>
      <button @click="clearGameBoard">Restart</button>
    </div>
    <div id="game-over-message" v-if="gameOver">
      Game Over
      <br />
      <template v-if="didWin">
        <span>{{ didWin }} Won!</span>
        <br />
      </template>
      <button @click="clearGameBoard">Play Again?</button>
    </div>
  </div>
</template> 

<style>
.container {
  max-width: 25rem;
  border: 1px solid white;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  align-items: center;
}

#grid {
  display: flex;
  flex-direction: column;
  gap: 3px;
  user-select: none;
  color: white;
}

#grid>div {
  display: flex;
  gap: 3px;
}

#grid>div>div {
  width: 5rem;
  height: 5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid red;
}

#grid:not(.game-over)>div>div:hover {
  background-color: rgba(255, 0, 0, 0.2);
  cursor: pointer;
}

#controls {
  display: flex;
  gap: 0.25rem;
}

#controls>button {
  flex:
    1;
}

#game-over-message {
  text-align: center;
}

button {
  border: 1px solid white;
  color: white;
  background-color: transparent;
  padding: 0.25rem 0.75rem;
  cursor: pointer;
}

button:hover {
  background-color: rgba(255, 255, 255, 0.1);
}
</style>