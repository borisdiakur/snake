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

let direction = ''
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
    Vue.set(rows[cell.y], cell.x, true)
  })
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
    // draw the initial snake
    draw(snake, this.rows)

    // make it move
    const intervalId = setInterval(() => {
      if (!direction) return
      const head = snake[snake.length - 1]
      let nextCell
      switch (direction) {
        case 'l':
          nextCell = {x: head.x - 1, y: head.y}
          break
        case 'r':
          nextCell = {x: head.x + 1, y: head.y}
          break
        case 'u':
          nextCell = {x: head.x, y: head.y - 1}
          break
        case 'd':
          nextCell = {x: head.x, y: head.y + 1}
          break
      }

      // game over if the snake bites itself or moves out of grid
      if (
        snake.find(cell => cell.x === nextCell.x && cell.y === nextCell.y) ||
        nextCell.x < 0 || nextCell.x >= width || nextCell.y < 0 || nextCell.y >= height
      ) {
        clearInterval(intervalId)
        alert('Game over')
        return
      }

      snake.push(nextCell)
      const tail = snake.shift()
      draw(snake, this.rows)
      setTimeout(() => { Vue.set(this.rows[tail.y], tail.x, undefined) }, interval / 2)
    }, interval)

    // set direction on keyboard input
    document.onkeydown = event => {
      event = event || window.event
      const charCode = (typeof event.which === 'number') ? event.which : event.keyCode
      switch (charCode) {
        case 37: // arrow left
          if (direction === 'r' || !direction) return
          direction = 'l'
          break
        case 39: // arrow right
          if (direction === 'l') return
          direction = 'r'
          break
        case 38: // arrow up
          if (direction === 'd') return
          direction = 'u'
          break
        case 40: // arrow down
          if (direction === 'u') return
          direction = 'd'
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
