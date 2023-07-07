<template>
  <div>
    <el-radio-group v-model="current" @change="handleCurrent">
      <el-radio :label="1">发射倒计时</el-radio>
      <el-radio :label="2">分离倒计时</el-radio>
      <el-radio :label="3">过站预报</el-radio>
      <el-radio :label="4">发射成功</el-radio>
    </el-radio-group>
    <br />
    <br />
    <el-date-picker v-model="t0" type="datetime" placeholder="Select date and time"  @change="handleT0"/>
    <br />
    <br/>
    <el-input-number v-model="separate" :min="1"  @change="handleChange" />
    <br/>
    <br/>
    <el-input-number v-model="size" :min="1"  @change="handleSizeChange" />
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
      forcast: ''
    }
  },
  created() {
    const settings = localStorage.getItem('settings')
    if (settings) {
      const { current = 1, datas = null } = JSON.parse(settings)
      this.current = current
      if (datas) {
        this.forcast = JSON.stringify(datas)
      }
    }
  },
  mounted() {},
  methods: {
    setLocal(){
      const temp = {
        current: this.current,
        separate:this.separate,
        size:this.size
      }
      if (this.forcast) {
        temp['datas'] = JSON.parse(this.forcast)
      }
      const du = this.$moment(this.t0).diff(this.$moment(),'seconds')
      temp['time'] = du * 1000
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
    }
  }
}
</script>

<style scoped></style>
