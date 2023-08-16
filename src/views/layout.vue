<template>
  <div class="container">
    <div class="header">
      <div class="head-title">
        杀号工具
      </div>
      <div class="tool-list">
        <div class="tool-item" @click="nullTool">指南</div>
        <div class="tool-item" @click="nullTool">版本</div>
        <div class="tool-item" @click="submitData">设置数据</div>
        <div class="tool-item" @click="callAuthor">联系作者</div>
      </div>
    </div>
    <div class="main">
      <div class="left">
        <h2>杀号{{killCount}}次</h2>
        <div class="history-item" @click="showHistory=true,currentHistory=index" v-for="(item,index) in history" :key="index">
          <div>
            <h3 v-if="index > 0"> 第{{index}}次杀号筛去:</h3>
            <p v-if="index>0" > {{item.killList.join(',').length > 0 ? item.killList.join(',') : '无'}}</p>
          </div>
        </div>
        <el-button v-if="history.length > 1" @click="rollBack" style="margin-top:10px" type="primary" >回滚一次</el-button>
      </div>
      <div class="right">
        <h1 class="final-result">--最终结果--</h1>
        <div class="table">
          <ball-table :list="list"></ball-table>
        </div>
        <div class="start-and-restart">
          <el-button type="primary" @click="selectForm = true">开始杀号</el-button>
          <el-button @click="reset">重新开始</el-button>
        </div>
      </div>
      <el-dialog
        title="设置数据"
        :visible.sync="dialogVisible"
        width="40%"
      >
        <el-input
          type="textarea"
          :rows="8"
          placeholder="请输入98期号码数据"
          v-model="beforeData">
        </el-input>
        <span slot="footer" class="dialog-footer">
          <el-button @click="beforeData = '' ">重 置</el-button>
          <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
      </el-dialog>
    </div>
    <el-drawer
      title="选择杀号条件"
      :visible.sync="selectForm"
      direction="ltr"
      ref="drawer"
      size="30%"
      >
      <div class="demo-drawer__content">
        <el-form :model="form">
          <el-form-item label="尾号">
            <el-checkbox-group v-model="form.killTail">
              <el-checkbox v-for="(item,index) in 10" :key="index+1" :label="item-1">{{item-1}}</el-checkbox>
            </el-checkbox-group>
            <el-checkbox v-model="form.bigTail" @change="handleCheckBigTail">大尾</el-checkbox>
            <el-checkbox v-model="form.smallTail" @change="handleCheckSmallTail">小尾</el-checkbox>
          </el-form-item>
          <el-form-item label="单双">
            <el-checkbox-group v-model="form.singleOrDouble" :max="1">
              <el-checkbox :label="1">单</el-checkbox>
              <el-checkbox :label="0">双</el-checkbox>
            </el-checkbox-group>
          </el-form-item>
          <el-form-item label="大小">
            <el-checkbox-group v-model="form.bigOrSmall" :max="1">
              <el-checkbox :label="1">小</el-checkbox>
              <el-checkbox :label="2">大</el-checkbox>
            </el-checkbox-group>
          </el-form-item>
          <el-form-item label="合单双">
            <el-checkbox-group v-model="form.addSingleOrDouble" :max="1">
              <el-checkbox :label="1">合单</el-checkbox>
              <el-checkbox :label="0">合双</el-checkbox>
            </el-checkbox-group>
          </el-form-item>
          <el-form-item label="模3">
            <el-checkbox-group v-model="form.mod3">
              <el-checkbox v-for="(item,index) in 3" :key="index+1" :label="item-1">{{item-1}}</el-checkbox>
            </el-checkbox-group>
          </el-form-item>
          <el-form-item label="颜色">
            <el-checkbox-group v-model="form.color">
              <el-checkbox :label="0">红色</el-checkbox>
              <el-checkbox :label="1">蓝色</el-checkbox>
              <el-checkbox :label="2">绿色</el-checkbox>
            </el-checkbox-group>
          </el-form-item>
        </el-form>
        <div class="demo-drawer__footer">
          <el-button @click="clearSelect">重 置</el-button>
          <el-button type="primary" @click="submitSelect">确 定</el-button>
        </div>
      </div>
    </el-drawer>
    <el-dialog
      :visible.sync="showHistory"
      width="60%"
      center>
      <h1 slot="title">第{{currentHistory}}次杀号结果</h1>
      <ball-table :list="history[currentHistory].list"></ball-table>
      <p class="kill-detail">本次杀号筛去:{{history[currentHistory].killList.join(',')}}</p>
      <div>
        <h2>杀号条件为：</h2>
        <p class="kill-if" v-if="history[currentHistory].form.killTail.length > 0">尾号：{{history[currentHistory].form.killTail.join(',')}}</p>
        <p class="kill-if" v-if="history[currentHistory].form.singleOrDouble.length > 0">单双：{{history[currentHistory].form.singleOrDouble[0] === 0 ? '双' : '单'}}</p>
        <p class="kill-if" v-if="history[currentHistory].form.bigOrSmall.length > 0">大小：{{history[currentHistory].form.bigOrSmall[0] === 1 ? '小' : '大'}}</p>
        <p class="kill-if" v-if="history[currentHistory].form.addSingleOrDouble.length > 0">合单双：{{history[currentHistory].form.addSingleOrDouble[0] === 0 ? '合双' : '合单'}}</p>
        <p class="kill-if" v-if="history[currentHistory].form.mod3.length > 0">模3余：{{history[currentHistory].form.mod3.join(',')}}</p>
        <p class="kill-if" v-if="history[currentHistory].form.color.length > 0">颜色：{{history[currentHistory].form.color.join(',')}}</p>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="showHistory = false">取 消</el-button>
        <el-button type="primary" @click="showHistory = false">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import BallTable from '@/components/ballTable.vue'
export default {
  name: 'layoutIndex',
  components: {
    BallTable
  },
  data () {
    return {
      list: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49],
      killCount: 0,
      beforeData: '',
      showHistory: false,
      currentHistory: 0,
      dialogVisible: false,
      selectForm: false,
      form: {
        killTail: [],
        bigTail: false,
        smallTail: false,
        singleOrDouble: [],
        bigOrSmall: [],
        mod3: [],
        addSingleOrDouble: [],
        color: []
      },
      history: [
        {
          killList: [],
          list: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49],
          form: {
            killTail: [],
            bigTail: false,
            smallTail: false,
            singleOrDouble: [],
            bigOrSmall: [],
            mod3: [],
            addSingleOrDouble: [],
            color: []
          }
        }
      ]
    }
  },
  methods: {
    reset () {
      this.list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49]
      this.killCount = 0
      this.history = [
        {
          killList: [],
          list: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49],
          form: {
            killTail: [],
            bigTail: false,
            smallTail: false,
            singleOrDouble: [],
            bigOrSmall: [],
            mod3: [],
            addSingleOrDouble: [],
            color: []
          }
        }
      ]
      this.clearSelect()
    },
    nullTool () {
      const h = this.$createElement
      this.$notify({
        title: '提示',
        message: h('i', { style: 'color: teal' }, '功能尚未开发，敬请期待'),
        duration: 2000,
        offset: 100
      })
    },
    submitData () {
      this.dialogVisible = true
    },
    callAuthor () {
      const h = this.$createElement
      this.$notify({
        title: '提示',
        message: h('i', { style: 'color: teal' }, '有事微信联系~'),
        duration: 2000,
        offset: 100
      })
    },
    handleCheckBigTail (val) {
      this.form.killTail = this.form.killTail.filter(item => item < 5)
      if (val) {
        this.form.killTail = this.form.killTail.concat([5, 6, 7, 8, 9])
      }
    },
    handleCheckSmallTail (val) {
      this.form.killTail = this.form.killTail.filter(item => item >= 5)
      if (val) {
        this.form.killTail = this.form.killTail.concat([0, 1, 2, 3, 4])
      }
    },
    clearSelect () {
      this.form.killTail = []
      this.form.bigTail = false
      this.form.smallTail = false
      this.form.singleOrDouble = []
      this.form.bigOrSmall = []
      this.form.mod3 = []
      this.form.addSingleOrDouble = []
      this.form.color = []
    },
    handleKill () {
      let killList = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49]
      if (this.form.killTail.length) {
        killList = killList.filter(item => this.form.killTail.includes(item % 10))
      }
      if (this.form.singleOrDouble.length) {
        killList = killList.filter(item => this.form.singleOrDouble.includes(item % 2))
      }
      if (this.form.bigOrSmall.length) {
        if (this.form.bigOrSmall[0] === 1) { // 小
          killList = killList.filter(item => item <= 24)
        } else { // 大
          killList = killList.filter(item => item > 24)
        }
      }
      if (this.form.mod3.length) {
        killList = killList.filter(item => this.form.mod3.includes(item % 3))
      }
      if (this.form.addSingleOrDouble.length) {
        killList = killList.filter(item => {
          const sum = String(item).split('').reduce((a, b) => Number(a) + Number(b))
          return this.form.addSingleOrDouble.includes(sum % 2)
        })
      }
      if (this.form.color.length) {
        let colorList = []
        const redCells = [1, 2, 7, 8, 12, 13, 18, 19, 23, 24, 29, 30, 34, 35, 40, 45, 46]
        const buleCells = [3, 4, 9, 10, 14, 15, 20, 25, 26, 31, 36, 37, 41, 42, 47, 48]
        const greenCells = [5, 6, 11, 16, 17, 21, 22, 27, 28, 32, 33, 38, 39, 43, 44, 49]
        if (this.form.color.includes(0)) {
          colorList = colorList.concat(redCells)
        }
        if (this.form.color.includes(1)) {
          colorList = colorList.concat(buleCells)
        }
        if (this.form.color.includes(2)) {
          colorList = colorList.concat(greenCells)
        }
        killList = killList.filter(item => colorList.includes(item))
        this.form.color = this.form.color.map(item => {
          if (item === 0) {
            return '红色'
          } else if (item === 1) {
            return '蓝色'
          } else {
            return '绿色'
          }
        })
      }
      if (killList.length === 49) {
        killList = []
      }
      killList = this.list.filter(item => killList.includes(item))
      this.list = this.list.map(item => {
        if (killList.includes(item)) {
          return 0
        } else {
          return item
        }
      })
      this.killCount++
      this.history.push({
        killList: killList,
        list: this.list,
        form: JSON.parse(JSON.stringify(this.form))
      })
      this.clearSelect()
      console.log(killList)
    },
    submitSelect () {
      console.log(this.form)
      this.handleKill()
      this.selectForm = false
    },
    rollBack () {
      if (this.killCount > 0) {
        this.killCount--
        this.list = this.history[this.killCount].list
        this.currentHistory = 0
        this.history.pop()
      }
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.container {
  margin: 0 auto;
  width: 1260px;
}
.header {
  display: flex;
  position: fixed;
  top: 0;
  width: 1260px;
  color: #565c63;
  height: 80px;
  background-color: white;
  border-bottom: 1px solid #dddfe5;
}
.head-title {
  padding-left: 50px;
  font-size: 30px;
  line-height: 80px;
  text-align: center;
  font-weight: bold;
}
.tool-list {
  display: flex;
  margin-left: 600px;
}
.tool-item {
  width: 100px;
  height: 80px;
  line-height: 80px;
  text-align: center;
  color: #919398;
  cursor: pointer;
}
.tool-item:hover {
  color: #565c63;
}
.main {
  padding-top: 100px;
  justify-content: space-between;
  display: flex;
}
.left {
  width: 300px;
}
.right {
  margin: 10px auto;
}
.final-result {
  text-align: center;
  font-size: 30px;
  font-weight: bold;
  margin-bottom: 10px;
}
.start-and-restart {
  margin-top: 20px;
  display: flex;
  justify-content: center;
}
.history-item {
  background-color: #f5f5f5;
  width: 300px;
}
.history-item :hover {
  background-color: #ebebeb;
  cursor: pointer;
}
.demo-drawer__content {
  padding: 20px;
}
.history-item h3 {
  text-align: left;
}
.history-item p {
  text-align: left;
  word-wrap: break-word;
}
.kill-detail {
  font-size: 20px;
  margin-top: 10px;
}
.kill-if {
  font-size: 16px;
  margin-left: 30px;
}
</style>
