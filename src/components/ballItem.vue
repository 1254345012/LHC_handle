<template>
  <div class="ballItem" :style="{background: specialStyle(value)}">
    {{value === 0? '': value}}
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: Number
    }
  },
  methods: {
    specialStyle (num) {
      const redCells = [1, 2, 7, 8, 12, 13, 18, 19, 23, 24, 29, 30, 34, 35, 40, 45, 46]
      const buleCells = [3, 4, 9, 10, 14, 15, 20, 25, 26, 31, 36, 37, 41, 42, 47, 48]
      const greenCells = [5, 6, 11, 16, 17, 21, 22, 27, 28, 32, 33, 38, 39, 43, 44, 49]
      if (redCells.includes(num)) {
        return '#b33335'
      } else if (buleCells.includes(num)) {
        return '#476ca3'
      } else if (greenCells.includes(num)) {
        return '#7ac143'
      }
    }
  }
}
</script>

<style scoped>
.ballItem {
  color: white;
  font-weight: bold;
  border-radius: 50%;
  width: 60px;
  height: 60px;
  line-height: 60px;
  font-size: 30px;
  text-align: center;
  border: 1px solid #ccc;
  margin: 1px;
}
</style>
