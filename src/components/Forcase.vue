<template>
  <section class="wrap">
    <div class="mask">
      <div class="header">
        <div class="logo">
          <img class="logo" src="@/assets/logo.pic.png" width="100" /> <span>银河航天</span>
        </div>
      </div>
      <div class="time">{{ currentDate() }} (北京)</div>
      <section class="card" v-for="(item, key) in datas" :key="key">
        <header class="card-header"><div class="card-header-title">{{ item.lable }}</div> <div class="card-header-desc"></div></header>
        <div class="card-body">
          <div class="row row-header">
            <div class="row-name">地面站</div>
            <div class="row-start">进站时间</div>
            <div class="row-end">出站时间</div>
            <div class="row-duration">时长(s)</div>
            <div class="row-process">进度</div>
            <div class="row-left">剩余(s)</div>
            <div class="row-elevation">最高仰角</div>
            <div class="row-description">备注</div>
          </div>
          <div
            :class="`row ${task.process !== 0 && task.process !== 100 && 'row-running'} ${
              task.process === 100 && 'row-done'
            } ${task.beforeLeft > 0 && task.beforeLeft < 120 && 'row-warning'}`"
            v-for="(task, index) in item.list"
            :key="index"
          >
            <div class="row-name">{{ task.antenna }}</div>
            <div class="row-start">{{ task.startText }}</div>
            <div class="row-end">{{ task.endText }}</div>
            <div class="row-duration">{{ task.duration }}</div>
            <div class="row-process row-process-col">
              <el-progress
                :text-inside="true"
                :show-text="task.process !== 0"
                :stroke-width="18"
                :percentage="task.process"
                :status="task.process !== 100 ? 'success' : ''"
                :striped="task.process < 100"
                :striped-flow="task.process < 100"
              />
              <div v-if="task.processText" class="row-process-text">
                距开始: {{ task.processText }}
              </div>
            </div>
            <div class="row-left">{{ task.left }}</div>
            <div class="row-elevation">{{ task.maxElevationAngle }}</div>
            <div class="row-description">{{ task.description }}</div>
          </div>
        </div>
        <footer class="card-footer">h</footer>
      </section>
    </div>
  </section>
</template>

<script>
import { map } from 'lodash'
export default {
  props: {
    value: {
      required: true,
      default: {
        noah: {
          lable: 'Noah',
          list: []
        },
        as2: {
          lable: 'As-2',
          list: []
        },
        as3: {
          lable: 'As-3',
          list: []
        }
      }
    }
  },
  data() {
    return {
      datas: {
        noah: {
          lable: 'Noah',
          list: []
        },
        as2: {
          lable: 'As-2',
          list: []
        },
        as3: {
          lable: 'As-3',
          list: []
        }
      }
    }
  },
  created() {
    this.formatData(this.value)
  },
  mounted() {
    setInterval(() => {
      this.formatData(this.datas)
    }, 1000)
  },
  watch: {
    value(news, olds) {
      news && this.formatData(news)
    }
  },
  methods: {
    currentDate() {
      return this.$moment().format('YYYY-MM-DD HH:mm:ss')
    },
    formatData(news) {
      const temp = {}
      map(news, ({ lable, list }, k) => {
        list.forEach((n) => {
          const st = this.$moment(n.start)
          const en = this.$moment(n.end)
          const current = this.$moment()
          n.startText = st.format('HH:mm:ss')
          n.endText = en.format('HH:mm:ss')
          n.duration = en.diff(st, 'seconds')
          let process = 0
          let processText = ''
          let left = '-'
          let beforeLeft = 0
          // 结束时间小于当前时间，已经结束
          if (en < current) {
            process = 100
          }
          // 开始时间大于当前时间，还未开始
          if (st > current) {
            process = 0
            const leftSeconds = st.diff(current, 'seconds')
            const seconds = this.$moment.duration(leftSeconds, 'seconds')
            beforeLeft = leftSeconds
            processText = `${seconds.hours()}:${seconds.minutes()}:${seconds.seconds()}`
          }
          // 开始时间小于当前，结束时间大于当前
          if (st <= current && en >= current) {
            const his = current.diff(st, 'seconds')
            left = n.duration - his
            processText = ''
            process = Math.max(parseInt((his / n.duration) * 100), 1)
          }
          n.process = process
          n.processText = processText
          n.left = left
          n.beforeLeft = beforeLeft
        })
        temp[k] = {
          lable,
          list
        }
      })
      this.datas = temp
    }
  }
}
</script>

<style scoped>
.wrap {
  background: url('../assets/3.jpeg') no-repeat center center;
  background-size: 100% 150%;
}
.mask {
  height: 100vh;
  width: 100wh;
  background: rgba(0, 0, 0, 0.6);
}

.header {
  padding-top: 30px;
}
.logo {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 60px;
  color: #fff;
  text-shadow: 0 0 5px #9898eb;
}

.card {
  margin: 20px;
  background: rgba(0, 0, 0, 0.5);
  border-radius: 5px;
  box-shadow: 0 0 2px #000000;
}

.card-header {
  /* background: #f00; */
  color: #fff;
  padding: 4px;
  padding-left: 10px;
  font-weight: bold;
  text-shadow: 0 0 2px #000000;
  font-size: 20px;
  display: flex;
  align-items: center;
}
.card-header-title {
  flex:1;
}
.card-header-desc{

}
.time{
  position: absolute;
  top:20px;
  right: 20px;
  font-size: 14px;
  padding-right: 10px;
  font-size: 40px;
  color: #fff;
  text-shadow: 0 0 2px #000000;
}
.card-body {
  /* background: #f00; */
  border: 1px solid rgba(255, 255, 255, 0.2);
  margin: 0 10px;
}

.card-footer {
  /* background: #f00; */
}

.row {
  display: flex;
  color: #fff;
  padding: 10px 4px;
  background: rgba(0, 0, 0, 0.8);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.row-running {
  background: rgba(136, 244, 109, 0.7);
  box-shadow: 0 0 2px #000000;
}

.row-running .row-name,
.row-running .row-start,
.row-running .row-end,
.row-running .row-duration,
.row-running .row-elevation,
.row-running .row-description,
.row-running .row-left {
  font-weight: bold;
  font-size: 24px;
  text-shadow: 0 0 2px rgba(0, 0, 0, 1);
}
.row-running .row-process {
  padding-top: 10px;
}

.row-warning {
  background: rgba(236, 241, 167, 0.5);
}

.row-warning .row-name,
.row-warning .row-start,
.row-warning .row-end,
.row-warning .row-duration,
.row-warning .row-elevation,
.row-warning .row-description,
.row-warning .row-left {
  /* font-size: 18px; */
  text-shadow: 0 0 2px rgba(0, 0, 0, 1);
}
.row-warning .row-process {
  padding-top: 4px;
}

.row-done {
  background: rgba(113, 113, 113, 0.9);
}

.row-done .row-name,
.row-done .row-start,
.row-done .row-end,
.row-done .row-duration,
.row-done .row-elevation,
.row-done .row-description,
.row-done .row-left {
  font-size: 12px;
  text-shadow: 0 0 2px rgba(0, 0, 0, 1);
}
.row-done .row-process {
  padding-top: 2px;
}

.row-header {
  background: rgba(0, 0, 0, 0.4);
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
}

.row-name {
  width: 280px;
}
.row-start {
  width: 200px;
}
.row-end {
  width: 200px;
}
.row-duration {
  width: 120px;
}
.row-left {
  width: 80px;
}
.row-elevation {
  width: 200px;
  padding-left: 10px;
}
.row-description {
  width: 280px;
  padding-left: 10px;
}
.row-process {
  flex: 1;
  justify-content: center;
  align-items: center;
  position: relative;
  padding-left: 10px;
  padding-right: 10px;
}
.row-process-col {
  padding-top: 4px;
}
.row-process-text {
  position: absolute;
  top: 3px;
  left: 14px;
  font-size: 12px;
  color: #12197e;
  font-weight: bold;
}
</style>
