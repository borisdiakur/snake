<template>
  <ul>
    <li class="row" v-for="row in rows">
      <ul>
        <li class="cell" v-for="cell in row" v-bind:class="{active: cell}"></li>
      </ul>
    </li>
  </ul>
</template>

<script>
import Vue from 'vue'

export default {
  name: 'grid',
  data () {
    return {
      rows: [],
      snake: [],
      currentDirection: '',
      nextDirection: '',
    }
  },
  methods: {
    draw () {
      this.snake.forEach(cell => {
        Vue.set(this.rows[cell.y], cell.x, 'snake')
      })
    },
    feed () {
      if (!this.rows[0] || !this.rows[0].length) return
      let randomY, randomX
      do {
        randomY = Math.floor(Math.random() * (this.rows[0]).length)
        randomX = Math.floor(Math.random() * this.rows.length)
      } while (this.rows[randomY][randomX])
      Vue.set(this.rows[randomY], randomX, 'mouse')
    }
  },
  created () {
    const width = 20
    const height = 20
    for (var rows = []; rows.push(new Array(width)) < height;);
    this.rows = rows
    this.snake = [
      { x: 3, y: 5 }, // tail
      { x: 4, y: 5 },
      { x: 5, y: 5 }  // head
    ]

    // draw the initial snake and feed it
    this.draw()
    this.feed()

    // make it move
    const interval = 100
    const intervalId = setInterval(() => {
      if (!this.nextDirection) return
      this.currentDirection = this.nextDirection

      const head = this.snake[this.snake.length - 1]
      let nextCell
      switch (this.currentDirection) {
        case 'l':
          nextCell = { x: head.x - 1, y: head.y }
          break
        case 'r':
          nextCell = { x: head.x + 1, y: head.y }
          break
        case 'u':
          nextCell = { x: head.x, y: head.y - 1 }
          break
        case 'd':
          nextCell = { x: head.x, y: head.y + 1 }
          break
      }

      // game over if the snake moves out of grid or bites itself
      if (
        nextCell.x < 0 || nextCell.x >= width || nextCell.y < 0 || nextCell.y >= height ||
        rows[nextCell.y][nextCell.x] === 'snake'
      ) {
        clearInterval(intervalId)
        alert('Game over')
        return
      }

      this.snake.push(nextCell)
      const tail = this.snake.shift()

      // eat mouse
      if (this.rows[nextCell.y][nextCell.x] === 'mouse') {
        this.snake.unshift({ x: tail.x, y: tail.y })
        this.draw(this.snake, this.rows)
        this.feed(this.snake, this.rows)
      } else {
        setTimeout(() => { Vue.set(this.rows[tail.y], tail.x, undefined) }, interval / 2)
        this.draw(this.snake, this.rows)
      }
    }, interval)

    // set direction on keyboard input
    document.onkeydown = event => {
      event = event || window.event
      const charCode = (typeof event.which === 'number') ? event.which : event.keyCode
      switch (charCode) {
        case 37: // arrow left
          if (this.currentDirection === 'r' || !this.currentDirection) return
          this.nextDirection = 'l'
          break
        case 39: // arrow right
          if (this.currentDirection === 'l') return
          this.nextDirection = 'r'
          break
        case 38: // arrow up
          if (this.currentDirection === 'd') return
          this.nextDirection = 'u'
          break
        case 40: // arrow down
          if (this.currentDirection === 'u') return
          this.nextDirection = 'd'
          break
      }
    }
  }
}
</script>

<style scoped>
  ul {
    list-style: none;
    padding: 0;
  }
  .row {
    height: 22px;
  }
  .cell {
    display: inline-block;
    width: 20px;
    height: 20px;
    border: solid 1px #ddd;
  }
  .active {
    background-color: steelblue;
  }
</style>
