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

let currentDirection = ''
let nextDirection = ''
const width = 20
const height = 20
const interval = 100
for (var rows = []; rows.push(new Array(width)) < height;);
const snake = [
  { x: 3, y: 5 }, // tail
  { x: 4, y: 5 },
  { x: 5, y: 5 }  // head
]

function draw (snake, rows) {
  snake.forEach(cell => {
    Vue.set(rows[cell.y], cell.x, 'snake')
  })
}

function feed (snake, rows) {
  let randomY, randomX
  do {
    randomY = Math.floor(Math.random() * height)
    randomX = Math.floor(Math.random() * width)
  } while (rows[randomY][randomX])
  Vue.set(rows[randomY], randomX, 'mouse')
}

export default {
  name: 'grid',
  data () {
    return {
      rows,
      snake: []
    }
  },
  created () {
    // draw the initial snake and feed it
    draw(snake, this.rows)
    feed(snake, this.rows)

    // make it move
    const intervalId = setInterval(() => {
      if (!nextDirection) return
      currentDirection = nextDirection

      const head = snake[snake.length - 1]
      let nextCell
      switch (currentDirection) {
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

      snake.push(nextCell)
      const tail = snake.shift()

      // eat mouse
      if (rows[nextCell.y][nextCell.x] === 'mouse') {
        snake.unshift({ x: tail.x, y: tail.y })
        draw(snake, this.rows)
        feed(snake, this.rows)
      } else {
        setTimeout(() => { Vue.set(this.rows[tail.y], tail.x, undefined) }, interval / 2)
        draw(snake, this.rows)
      }
    }, interval)

    // set direction on keyboard input
    document.onkeydown = event => {
      event = event || window.event
      const charCode = (typeof event.which === 'number') ? event.which : event.keyCode
      switch (charCode) {
        case 37: // arrow left
          if (currentDirection === 'r' || !currentDirection) return
          nextDirection = 'l'
          break
        case 39: // arrow right
          if (currentDirection === 'l') return
          nextDirection = 'r'
          break
        case 38: // arrow up
          if (currentDirection === 'd') return
          nextDirection = 'u'
          break
        case 40: // arrow down
          if (currentDirection === 'u') return
          nextDirection = 'd'
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
