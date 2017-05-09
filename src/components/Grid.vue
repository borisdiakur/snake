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
    // make it move
    setInterval(() => {
      if (!direction) return
      const head = snake[snake.length - 1]
      switch (direction) {
        case 'l':
          snake.push({x: head.x - 1, y: head.y})
          break
        case 'r':
          snake.push({x: head.x + 1, y: head.y})
          break
        case 'u':
          snake.push({x: head.x, y: head.y - 1})
          break
        case 'd':
          snake.push({x: head.x, y: head.y + 1})
          break
      }
      const tail = snake.shift()
      draw(snake, this.rows)
      setTimeout(() => { Vue.set(this.rows[tail.y], tail.x, undefined) }, interval / 2)
    }, interval)
    draw(snake, this.rows)

    document.onkeydown = event => {
      event = event || window.event
      const charCode = (typeof event.which === 'number') ? event.which : event.keyCode
      switch (charCode) {
        case 38: // arrow up
          direction = 'u'
          break
        case 40: // arrow down
          direction = 'd'
          break
        case 37: // arrow left
          direction = 'l'
          break
        case 39: // arrow right
          direction = 'r'
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
