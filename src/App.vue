<template>
  <main class="min-h-screen">
    <article>
      <section class="mb-10">
        <div class="flex items-center full-width">
          <div class="text-5xl font-medium text-black mx-auto">
            2048
          </div>
        </div>
      </section>

      <section>
        <div class="grid grid-cols-4 gap-1 w-7/12 mx-auto">
          <div
            v-for="(currRow, rowKey) in flatBoard"
            :key="rowKey"
            class="h-20 border flex items-center justify-center text-2xl"
          >
            {{ currRow }}
          </div>
        </div>
      </section>
    </article>
  </main>
</template>

<script>

import _flatten from 'lodash/flatten'

const DIMENSIONS = 4

export default {
  name: 'App',

  data () {
    return {
      board: [
        [2, 2, 3, 3],
        [2, 1, 2, 1],
        [2, 1, 2, 1],
        [4, 3, 4, 4]
      ]
    }
  },

  computed: {
    flatBoard () {
      return _flatten(this.board)
    }
  },

  mounted () {
    document.addEventListener('keydown', this.handleArrowMovement)
  },

  methods: {
    moveRight (board) {
      const newBoard = []

      for (let i = 0; i < board.length; i++) {
        const currRow = board[i]
        const filteredRow = currRow.filter(Boolean)

        const newRow = []
        for (let j = 0; j < filteredRow.length; j++) {
          const prevNum = filteredRow[j - 1]
          const currNum = filteredRow[j]
          const nextNum = filteredRow[j + 1]

          if (currNum === nextNum) {
            newRow.push(currNum + nextNum)
            j++ // increment iteration
            continue
          }

          if (currNum !== nextNum) {
            newRow.push(currNum)
            continue
          }

          if (prevNum === undefined) {
            newRow.push(currNum)
            continue
          }

          if (nextNum === undefined) {
            newRow.push(currNum)
            continue
          }
        }

        const missing = [...new Array(DIMENSIONS - newRow.length)].map(() => null)

        newBoard.push([...missing, ...newRow])
      }

      return newBoard
    },

    moveLeft (board) {
      const newBoard = []

      for (let i = 0; i < board.length; i++) {
        const currRow = board[i]
        const filteredRow = currRow.filter(Boolean).reverse()

        const newRow = []
        for (let j = 0; j < filteredRow.length; j++) {
          const prevNum = filteredRow[j - 1]
          const currNum = filteredRow[j]
          const nextNum = filteredRow[j + 1]

          if (currNum === nextNum) {
            newRow.unshift(currNum + nextNum)
            j++ // increment iteration
            continue
          }

          if (currNum !== nextNum) {
            newRow.unshift(currNum)
            continue
          }

          if (prevNum === undefined) {
            newRow.unshift(currNum)
            continue
          }

          if (nextNum === undefined) {
            newRow.unshift(currNum)
            continue
          }
        }

        const missing = [...new Array(DIMENSIONS - newRow.length)].map(() => null)

        newBoard.push([...newRow, ...missing])
      }

      return newBoard
    },

    moveUp  (board) {
      const newBoard = []
      const tempBoard = []

      for (let i = 0; i < board.length; i++) {
        const newArr = []

        for (let j = 0; j < DIMENSIONS; j++) {
          newArr.push(board[j][i])
        }

        tempBoard.push(newArr)
      }

      const result = this.moveLeft(tempBoard)

      for (let i = 0; i < result.length; i++) {
        const newArr = []

        for (let j = 0; j < DIMENSIONS; j++) {
          newArr.push(result[j][i])
        }

        newBoard.push(newArr)
      }

      return newBoard
    },

    moveDown  (board) {
      const newBoard = []
      const tempBoard = []

      for (let i = 0; i < board.length; i++) {
        const newArr = []

        for (let j = 0; j < DIMENSIONS; j++) {
          newArr.push(board[j][i])
        }

        tempBoard.push(newArr)
      }

      const result = this.moveRight(tempBoard)

      for (let i = 0; i < result.length; i++) {
        const newArr = []

        for (let j = 0; j < DIMENSIONS; j++) {
          newArr.push(result[j][i])
        }

        newBoard.push(newArr)
      }

      return newBoard
    },

    handleArrowMovement (e) {
      const methodsByKey = {
        ArrowRight: this.moveRight,
        ArrowLeft: this.moveLeft,
        ArrowDown: this.moveDown,
        ArrowUp: this.moveUp
      }

      const method = methodsByKey[e.key]

      if (!method) {
        return
      }

      this.board = method(this.board)
    }
  }

}
</script>
