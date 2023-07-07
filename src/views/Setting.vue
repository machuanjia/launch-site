<template>
  <div>
    <el-radio-group v-model="current" @change="handleCurrent">
      <el-radio :label="1">发射倒计时</el-radio>
      <el-radio :label="2">分离倒计时</el-radio>
      <el-radio :label="3">过站预报</el-radio>
      <el-radio :label="4">发射成功</el-radio>
    </el-radio-group>
  </div>
</template>

<script>
export default {
  data() {
    return {
      current: 1
    }
  },
  mounted() {},
  methods:{
    handleCurrent(){
      localStorage.setItem("settings", JSON.stringify({current:this.current}))
    }
  }
}
</script>

<style scoped></style>
