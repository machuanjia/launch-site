<template>
  <section class="wrap">
    <div class="mask">
      <div class="header">
          <div class="logo-wrap">
            <img class="logo" src="@/assets/logo.pic.png" width="60" /> <span>银河航天</span>
          </div>
          <div class="time">{{ currentDate() }} (北京)</div>
      </div>
      <br/><br/><br/><br/>
      <section class="card" v-for="(item, key) in datas" :key="key">
        <header class="card-header">
          <div class="card-header-title">{{ item.lable }}{{ key }}</div>
          <div class="card-header-desc"></div>
        </header>
        <div class="card-body">
          <div class="row row-header">
            <div class="row-name">地面站</div>
            <div class="row-start">进站时间</div>
            <div class="row-end">出站时间</div>
            <div class="row-process">进度</div>
            <div class="row-left">剩(s)</div>
            <div class="row-elevation">最高仰角</div>
            <div class="row-description">备注</div>
          </div>
          <div
            :class="`row ${task.process !== 0 && task.process !== 100 && 'row-running'} ${
              task.process === 100 && 'row-done'
            } ${task.beforeLeft > 0 && task.beforeLeft < 120 && 'row-warning'}  row-orbit-${task.orbitNo}`
            "
            v-for="(task, index) in item.list"
            :key="index"
          >
            <div class="row-name">{{ task.antenna }}</div>
            <div class="row-start">{{ task.startText }}</div>
            <div class="row-end">{{ task.endText }}</div>
            <div class="row-process row-process-col">
              <el-progress
                :key="`${index}-process`"
                class="process"
                :style="{ width: task.durationPercent, marginLeft: task.offset }"
                :text-inside="true"
                :show-text="task.process !== 0"
                :stroke-width="24"
                :percentage="task.process"
                :status="task.process !== 100 ? '' : ''"
                :striped="task.process < 100"
                :striped-flow="task.process < 100"
              />
              <div v-if="task.processText" class="row-process-text" :style="{left:task.offset}">
                距开始:{{ task.processText }}
              </div>
            </div>
            <div class="row-left">{{ task.process !== 0 ? task.left : task.duration }}</div>
            <div class="row-elevation">{{ task.maxElevationAngle }}</div>
            <div class="row-description">{{ task.description }}</div>
          </div>
        </div>
        <!-- <footer class="card-footer"></footer> -->
      </section>

      <br/>
      <br/>
      <br/>
      <br/>
      <div class="slogen2">Make The Earth Better</div>
      <div class="slogen2">让地球更美好</div>
    </div>
  </section>
</template>

<script>
import { groupBy, map, sortBy } from 'lodash'
export default {
  props: {
    size:{
      default: 5
    },
    isMain:{
      default: false
    },
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
    },
    ignore:{
      default: 10 * 60 * 1000
    }
  },
  data() {
    return {
      spliceLength:5,
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
    this.spliceLength = this.size
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
    },
    size(news, olds) {
     this.spliceLength = news
    }
  },
  methods: {
    currentDate() {
      return this.$moment().format('YYYY-MM-DD HH:mm:ss')
    },
    getMax(list){
      const groups = groupBy(list,'orbitNo')
      const maxMap = {}
      map(groups,(list,orbitNo)=>{
        let min = null
        let max = null
        list.forEach((n) => {
          const st = this.$moment(n.start)
          const en = this.$moment(n.end)
          if(en > this.$moment()){
            if (!min) {
              min = st.valueOf()
            } else {
              min = Math.min(st.valueOf(), min)
            }
            if (!min) {
              max = en.valueOf()
            } else {
              max = Math.max(en.valueOf(), max)
            }
          }
        })
        maxMap[orbitNo] = {
          min,
          max
        }
      })
      return maxMap
    },
    formatData(news) {
      const temp = {}
      map(news, ({ label, list }, k) => {
        let ls = list.filter((n)=>{
          let flag = this.$moment(n.end).add(this.ignore,'milliseconds') > this.$moment()
          if(this.isMain && n.is_main === 0){
            flag = false
          }
          return flag
        })
        ls = sortBy(ls, (n) => {
          return this.$moment(n.start).valueOf()
        }).splice(0,this.spliceLength)
        const maxMap = this.getMax(ls)
        ls.forEach((n) => {
          const st = this.$moment(n.start)
          const en = this.$moment(n.end)
          const current = this.$moment()
          n.startText = st.format('HH:mm:ss')
          n.endText = en.format('HH:mm:ss')
          n.duration = en.diff(st, 'seconds')
          let process = 0
          let processText = ''
          let left = 0
          let beforeLeft = 0
          if (en < current) {
            // 结束时间小于当前时间，已经结束
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
        // })
        // ls.forEach((n) => {
          const max = maxMap[n.orbitNo].max
          const min = maxMap[n.orbitNo].min
          const allDuration = this.$moment(max).diff(this.$moment(min), 'seconds')
          if (this.$moment(n.end) < this.$moment()) {
            n.durationPercent = '100%'
            n.offset = '0%'
          }else{
            n.durationPercent = parseInt((n.duration / allDuration) * 100) + '%'
            n.offset =
              parseInt(
                (this.$moment(n.start).diff(this.$moment(min), 'seconds') / allDuration) * 100
              ) + '%'
          }
          n.allDuration = allDuration
          n.st = this.$moment(n.start).valueOf()
          n.min = min
        })
        temp[k] = {
          lable:label,
          list: ls
        }
      })
      this.datas = temp
    }
  }
}
</script>

<style scoped>
.wrap {
  background: url('../assets/4.jpeg') no-repeat center center;
  background-size: 100% 100%;
}
.mask {
  min-height: 100vh;
  width: 100wh;
  background: #161719;
  padding-bottom: 100px;
}

.header {
  padding-top: 30px;
  padding-bottom: 50px;
  position: fixed;
  left: 0;
  right:0;
  display: flex;
  color: #d8d9da;
  padding: 10px;
  background: #161719;
  z-index: 10;
}
.logo-wrap{
  flex: 1;
  display: flex;
  align-items: center;
  font-size: 40px;
}

.card {
  margin: 10px;
  background: #212124;
  border-radius: 5px;
  border:1px solid #141414;
}

.card-header {
  /* background: #f00; */
  color: #d8d9da;
  padding: 4px;
  padding-left: 10px;
  font-weight: bold;
  /* text-shadow: 0 0 2px #000000; */
  font-size: 20px;
  display: flex;
  align-items: center;
  /* border:1px solid #161719; */

  text-align: center;
}
.card-header-title {
  flex: 1;
  font-weight: bold;
}
.card-header-desc {
}
.time {
  font-size: 40px;
  color: #d8d9da;
}
.card-body {
  /* background: #f00; */
  /* border: 1px solid rgba(255, 255, 255, 0.2); */
  /* margin: 0 10px; */
}

.card-footer {
  /* background: #f00; */
}

.row {
  display: flex;
  color: #d8d9da;
  /* padding: 10px 4px; */
  /* background: rgba(0, 0, 0, 0.8); */
  font-size: 24px;
  /* margin-bottom: 4px; */
}


.row .row-name,
.row .row-start,
.row .row-end,
.row .row-duration,
.row .row-elevation,
.row .row-description,
.row .row-left,
.row .row-process {
  /* font-size: 18px; */
  border: 2px solid #161719;
  padding: 10px 4px;
  border-right: 0;
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
  /* font-size: 24px; */
  text-shadow: 0 0 2px rgba(0, 0, 0, 1);
}
.row-running .row-process {
  /* padding-top: 10px; */
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
  /* padding-top: 4px; */
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
  /* font-size: 12px; */
  text-shadow: 0 0 2px rgba(0, 0, 0, 1);
}
.row-done .row-process {
  /* padding-top: 2px; */
}

.row-header {
  background: linear-gradient(135deg,#2f2f32,#262628);
  border-top:1px solid #161719;
  border-bottom:1px solid #161719;
  font-size: 16px;
}

.row-name {
  width: 270px;
}
.row-start {
  width: 120px;
 
}
.row-end {
  width: 120px;
}
.row-duration {
  width: 80px;
  padding-right: 4px;
}
.row-left {
  width: 80px;
  padding-left: 10px;
}
.row-elevation {
  width: 120px;
  padding-left: 10px;
}
.row-description {
  width: 180px;
  padding-left: 10px;
  font-size:14px;
  display: flex;
  align-items: center;
}
.row-process {
  flex: 1;
  /* justify-content: center;
  align-items: center; */
  position: relative;
}
.row-process-col {
  padding-top: 17px !important;
}
.row-process-text {
  position: absolute;
  top: 18px;
  left: 16px;
  font-size: 14px;
  color: #212124;
  /* font-weight: bold; */
  transform: translateX(8px);
}

.slogen2{
  text-align: center;
  font-size: 16px;
  font-family: 'Microsoft YaHei';
  color: #fff;
  margin-bottom: 20px;
  }

  .row-orbit-0{
    background: rgba(138, 138, 138, 0.4);
  }

  .row-orbit-0:not(.card-body :has(+ .row-orbit-0)){
    margin-bottom: 20px;
  }

  .process{
    font-size: 16px;
  }

</style>
