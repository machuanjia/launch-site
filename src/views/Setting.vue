<template>
  <div>
   页面: <el-radio-group v-model="current" @change="handleCurrent">
      <el-radio :label="1">发射倒计时</el-radio>
      <el-radio :label="2">分离倒计时</el-radio>
      <el-radio :label="3">过站预报</el-radio>
      <el-radio :label="4">发射成功</el-radio>
    </el-radio-group>
    <br />
    <br />
   发射开始时间: <el-date-picker v-model="t0" type="datetime" placeholder="Select date and time"  @change="handleT0"/>
    <br />
    <br/>
    发射开始时间和星箭分离间隔: <el-input-number v-model="separate" :min="1"  @change="handleChange" />
    <br/>
    <br/>
    过站预报显示个数: <el-input-number v-model="size" :min="1"  @change="handleSizeChange" />
    <br/>
    <br/>
    过站预报距离当前时间多少秒不在显示: <el-input-number v-model="ignore" :min="1"  @change="handleShowDurationChange" />
    <br/>
    <br/>
    <el-input
      v-model="forcast"
      :rows="20"
      type="textarea"
      placeholder="Please input"
      @change="handleJSON"
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      current: 1,
      t0: this.$moment().add(2,'hours'),
      separate: 807 * 1000,
      size:5,
      ignore: 10 * 60 * 1000,
      forcast: ''
    }
  },
  created() {
    const settings = localStorage.getItem('settings')
    if (settings) {
      const { current = 1, datas = null,t0 = this.$moment().add(2,'hours') } = JSON.parse(settings)
      this.current = current
      if (datas) {
        this.forcast = JSON.stringify(datas)
      }
      this.t0 = t0
    }
  },
  mounted() {},
  methods: {
    setLocal(){
      const temp = {
        current: this.current,
        separate:this.separate,
        ignore:this.ignore,
        size:this.size
      }
      if (this.forcast) {
        temp['datas'] = JSON.parse(this.forcast)
      }
      temp['t0'] = this.$moment(this.t0).valueOf()
      temp['t1'] = this.$moment(this.t0).add(this.separate,'milliseconds').valueOf()
      localStorage.setItem('settings', JSON.stringify(temp))
    },
    handleT0(){
      this.setLocal()
    },
    handleSizeChange(){
      this.setLocal()
    },
    handleChange(){
      this.setLocal()
    },
    handleCurrent() {
     this.setLocal()
    },
    handleJSON() {
      this.setLocal()
    },
    handleShowDurationChange(){
      this.setLocal()
    }
  }
}
</script>

<style scoped></style>
