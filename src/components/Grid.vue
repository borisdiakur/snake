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
export default {
  name: 'grid',
  data () {
    return {
      rows: [
        [0, 0, 0],
        [0, 1, 1],
        [0, 0, 1]
      ]
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
