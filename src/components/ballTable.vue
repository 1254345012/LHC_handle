<template>
  <div class="table">
      <div class="thead" v-for="(item,index) in th" :key="index">{{item}}</div>
      <ball-item v-for="(item,index) in list" :key="index+12" :value="item"></ball-item>
  </div>
</template>

<script>
import ballItem from '@/components/ballItem.vue'
export default {
  components: {
    ballItem
  },
  data () {
    return {
      th: ['鼠', '牛', '虎', '兔', '龙', '蛇', '马', '羊', '猴', '鸡', '狗', '猪']
    }
  },
  props: {
    list: {
      type: Array,
      default: () => []
    }
  }
}
</script>

<style scoped>
.table {
  margin: 0 auto;
  display: flex;
  width: 746px;
  flex-wrap: wrap;
  border: 1px solid #ccc;
}
.thead {
  color: black;
  width: 60px;
  height: 50px;
  line-height: 50px;
  font-size: 30px;
  text-align: center;
  margin: 1px;
}
</style>
