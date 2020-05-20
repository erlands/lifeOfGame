<template>
  <div id="app">
    <div>
      <div v-for="i in line" :key="i" class="line">
        <div v-for="j in column" :key="j" class="grid" @click="change(i, j)">
          <div v-if="lifes.indexOf(getNum(i, j)) === -1" class="cell die"></div>
          <div v-if="lifes.indexOf(getNum(i, j)) !== -1" class="cell alive"></div>
        </div>
      </div>
    </div>
    <div class="menu">
      <el-button type="success" @click="start" :disabled="this.status || this.lifes.length === 0">开始</el-button>
      <el-button  type="danger" @click="stop" :disabled="!this.status">暂停</el-button>
      <el-button type="success" @click="clear" :disabled="this.status || this.lifes.length === 0">清除</el-button>
      <p>第 {{bout}}回合</p>
      <p>计算耗时: {{time}}ms</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: function () {
    return {
      line: 50, // 行数
      column: 80, // 列数
      total: 0,
      lifes: [], // 存活的细胞
      status: false, // 状态 是否开始
      interval: null,
      time: 0,
      bout: 0
    }
  },
  methods: {
    clear: function () {
      this.lifes = []
    },
    // 暂停
    stop: function () {
      this.status = false
      if (this.interval != null) {
        window.clearInterval(this.interval)
      }
    },
    // 开始
    start: function () {
      this.bout = 0
      this.status = true
      this.interval = window.setInterval(() => {
        if (this.lifes.length !== 0) {
          this.next()
          this.bout++
        } else {
          this.stop()
        }
      }, 500)
    },
    // 获取细胞的编号
    getNum: function (i, j) {
      return (((i - 1) * this.column) + j)
    },
    // 下一回合
    next: function () {
      let s = new Date().getTime()
      let nl = [] // 下一轮存活的细胞
      for (let i = 1; i <= this.total; i++) {
        if (this.life(i)) {
          nl.push(i)
        }
      }
      this.lifes = nl
      let e = new Date().getTime()
      this.time = e - s
    },
    // 是否存活
    life: function (num) {
      let n = 0 // 此细胞周围存活的细胞数量
      let lifes = this.lifes
      let c = num % this.column // 列
      // 上
      if (num > this.column) { // 不是第一行就有上一行
        let up = num - this.column
        if (lifes.indexOf(up) !== -1) {
          n++
        }
        if (c !== 1) { // 不是第一列就有上一列
          if (lifes.indexOf(up - 1) !== -1) {
            n++
          }
        }
        if (c !== 0) { // 不是最后一列就有下一列
          if (lifes.indexOf(up + 1) !== -1) {
            n++
          }
        }
      }
      // 下
      if (num <= (this.total - this.column)) { // 不是最后一行就有下一行
        let down = num + this.column
        if (lifes.indexOf(down) !== -1) {
          n++
        }
        if (c !== 1) { // 不是第一列就有上一列
          if (lifes.indexOf(down - 1) !== -1) {
            n++
          }
        }
        if (c !== 0) { // 不是最后一列就有下一列
          if (lifes.indexOf(down + 1) !== -1) {
            n++
          }
        }
      }
      // 左
      if (c !== 1) {
        if (lifes.indexOf(num - 1) !== -1) {
          n++
        }
      }
      // 右
      if (c !== 0) {
        if (lifes.indexOf(num + 1) !== -1) {
          n++
        }
      }
      return n === 2 || n === 3
    },
    // 点击事件 改变当前细胞的状态
    change: function (i, j) {
      let num = this.getNum(i, j)
      let index = this.lifes.indexOf(num)
      if (index === -1) {
        this.lifes.push(num)
      } else {
        this.lifes[index] = this.lifes[this.lifes.length - 1]
        this.lifes.pop()
      }
    }
  },
  mounted: function () {
    this.total = this.line * this.column
  }
}
</script>

<style>
.alive {
  background-color: red;
}
.die {
  background-color: yellow;
}
.grid {
  float: left;
  width: 14px;
  height: 14px;
  border: 1px solid white;
}
.line {
  clear: both;
}
.cell {
  height: 100%;
  width: 100%;
}
.menu {
  float: left;
  top: -700px;
  position: relative;
}
</style>
