<template>
  <main>
    <article>
      <section class="navigation">
        <div class="navigation-item nav-item--title">
          2048
        </div>

        <div class="navigation-item nav-item--actions">
          <button
            class="btn btn--reset"
            @click="reset"
          >
            Reset
          </button>
        </div>
      </section>

      <section>
        <div
          id="2048"
          class="cell-container"
        >
          <div
            v-for="(currRow, rowKey) in flatBoard"
            :key="rowKey"
            class="cell-container--item"
            :style="`background-color: ${tileColors[currRow] || tileColors.default}`"
          >
            {{ currRow }}
          </div>
        </div>
      </section>

      <section>
        <div class="footer">
          <p class="footer-title">
            HOW TO PLAY
          </p>

          <hr>

          <div class="footer-caption">
            <p><i>For Computers: </i></p> Use your arrow keys to move the tiles.
          </div>

          <div class="footer-caption">
            <p><i>For Mobiles: </i></p> Swipe to move the tiles.
          </div>

          Tiles with the same number merge into one when they touch. Add them up to reach <b>2048</b>!
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
        [null, null, null, null],
        [null, null, null, null],
        [null, null, null, null],
        [null, null, null, null]
      ],

      tileColors: {
        null: '#C8BAAE',
        2: '#EEE5DB',
        4: '#ECE0C9',
        8: '#F2B078',
        16: '#F59462',
        32: '#F67D5E',
        64: '#F65F3A',
        128: '#EDCF72',
        256: '#EDCD60',
        512: '#EDC951',
        1024: '#ECC53E',
        2048: '#EDC32F',
        default: '#3D3A33'
      }
    }
  },

  computed: {
    flatBoard () {
      return _flatten(this.board)
    },

    hasSpace () {
      const flatBoard = this.flatBoard
      for (let i = 0; i < flatBoard.length; i++) {
        const curr = flatBoard[i]

        if (curr === null) {
          return true
        }
      }

      return false
    }
  },

  mounted () {
    document.addEventListener('keydown', this.handleArrowMovement)

    this.initSwiper()

    this.board = this.addRandom(this.board, 2)
  },

  methods: {
    playSlide () {
      const file = require('@/assets/slide.mp3')
      const audio = new Audio(file)
      audio.play()
    },

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
            newRow.push(currNum * 2)
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
            newRow.unshift(currNum * 2)
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

    reset () {
      const file = require('@/assets/reset.mp3')
      const audio = new Audio(file)
      audio.play()

      this.board = this.addRandom([
        [null, null, null, null],
        [null, null, null, null],
        [null, null, null, null],
        [null, null, null, null]
      ], 2)
    },

    addRandom (board, number) {
      const newBoard = []
      const luck = 0.8
      let isLucky = 0

      if (!this.hasSpace) {
        return board
      }

      for (let i = 0; i < board.length; i++) {
        const currRow = board[i]
        const isLuckyRow = Math.random() > luck
        const arr = []

        for (let j = 0; j < currRow.length; j++) {
          const currNum = currRow[j]
          const isLuckyNumber = Math.random() > luck

          if (currNum === null && isLuckyNumber && isLuckyRow && !isLucky) {
            arr.push(number)
            isLucky = 1
          } else {
            arr.push(currNum)
          }
        }

        newBoard.push(arr)
      }

      return isLucky ? newBoard : this.addRandom(board, number)
    },

    initSwiper () {
      const isMobile = navigator.userAgentData.mobile
      if (!isMobile) {
        return
      }

      const x = { start: 0, end: 0 }
      const y = { start: 0, end: 0 }

      const slider = document.getElementById('2048')

      function handleGesture () {
        const xDiff = Math.abs(x.end - x.start)
        const yDiff = Math.abs(y.end - y.start)

        // vertical
        if (xDiff > yDiff) {
          return x.start > x.end
            ? 'left'
            : 'right'
        }

        return y.start > y.end
          ? 'up'
          : 'down'
      }

      slider.addEventListener('touchstart', e => {
        x.start = e.changedTouches[0].screenX
        y.start = e.changedTouches[0].screenY
      })

      slider.addEventListener('touchend', e => {
        x.end = e.changedTouches[0].screenX
        y.end = e.changedTouches[0].screenY
        const direction = handleGesture()

        const methodsByKey = {
          right: this.moveRight,
          left: this.moveLeft,
          down: this.moveDown,
          up: this.moveUp
        }

        const method = methodsByKey[direction]

        if (!method) {
          return
        }

        const board = method(this.board)
        this.board = this.addRandom(board, 2)
        this.playSlide()
      })
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

      const board = method(this.board)
      this.board = this.addRandom(board, 2)
      this.playSlide()
    }
  }
}
</script>

<style lang="scss" scoped>
$app-width: 310px;

main {
  background-color: #FBF9EF;
  height: 100vh;
}

article {
  max-width: $app-width;
  margin-right: auto;
  margin-left: auto;
}

.footer {
  margin-top: 10px;
  font-size: 0.7rem;

  hr {
    margin-top: 10px;
    margin-bottom: 10px;
  }

  .footer-title {
    font-size: 0.8rem;
    font-weight: 900;
    text-align: center;
  }

  .footer-caption {
    font-size: 0.75rem;
    margin-bottom: 10px;

    i {
      font-weight: bold;
    }
  }
}

.navigation {
  max-width: $app-width;
  margin-right: auto;
  margin-left: auto;
  display: flex;
  justify-content: space-between;
  height: 64px;
  align-items: center;

  .nav-item--title {
    font-weight: bold;
    font-size: 3rem;
  }

  .nav-item--actions {
    display: flex;

  align-items: center;
  }
}

.btn {
  padding: 3px 12px;
  font-size: 0.8rem;
  border-radius: 3px;
  text-transform: uppercase;

  &.btn--reset {
    border: 1px solid brown;
    color: brown;
  }
}

.cell-container {
  display: flex;
  flex-wrap: wrap;
  max-width: $app-width;
  margin-right: auto;
  margin-left: auto;
  padding: 5px;
  border-radius: 5px;
  background-color: #BAADA1;
  user-select: none;

  .cell-container--item {
    user-select: none;
    height: 65px;
    width: 20px;
    flex: 1 1 21%;
    border-radius: 5px;
    margin: 5px;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 2rem;
    font-weight: bold;
    background-color: #C8BAAE;
  }
}

</style>
