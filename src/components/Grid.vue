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

const width = 20
const height = 20
for (var rows = []; rows.push(new Array(width)) < height;);

function draw (snake, rows) {
  snake.forEach(cell => {
    rows[cell.y][cell.x] = cell
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
    this.snake = [
      { x: 3, y: 5 }, // tail
      { x: 4, y: 5 },
      { x: 5, y: 5 }  // head
    ]
    draw(this.snake, this.rows)
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
