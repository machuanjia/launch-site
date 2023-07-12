<template>
  <div v-if="current == 1" class="main"><LanchDown :time="time"/></div>
  <div v-if="current == 2" class="main"><SeparateDown :time="separate"/></div>
  <div v-if="current == 3" class="main"><Forcase :size="size" :value="datas" :ignore="ignore" :isMain="isMain"/></div>
  <div v-if="current == 4" class="main"><Success /></div>
</template>
  <script>
import LanchDown from '../components/LanchDown.vue'
import SeparateDown from '../components/SeparateDown.vue'
import Forcase from '../components/Forcase.vue'
import Success from '../components/Success.vue'

export default {
  components: { LanchDown, SeparateDown, Forcase, Success },
  data() {
    return {
      current: 1,
      time: 1 * 24 * 60 * 60 * 1000,
      separate:807*1000,
      ignore:10 * 60 * 1000,
      size:5,
      isMain:false,
      datas:{
        noah:{
          lable: 'Noah',
          list: []
        },
        as2:{
          lable: 'As-2',
          list: []
        },
        as3:{
          lable: 'As-3',
          list: []
        },
      }
    }
  },
  created(){
    const settings = localStorage.getItem("settings")
    if(settings){
      const { current = 1, datas = null,size=5, t0,t1,ignore = 10 * 60 * 1000,isMain=false} = JSON.parse(settings)
      this.current = current
      if(datas){
        this.datas = datas
      }
      this.size = size
      if(t0){
        this.time = this.$moment(t0).diff(this.$moment(),'milliseconds')
      }
      if(t1){
        this.separate = this.$moment(t1).diff(this.$moment(),'milliseconds')
      }
      this.ignore = ignore
      this.isMain = isMain
    }
  },
  mounted() {
    window.onstorage = (e) => {
      if (e.key === 'settings' && e.newValue) {
        const obj = JSON.parse(e.newValue)
        this.current = obj.current
        if(obj.datas){
          this.datas = obj.datas
        }
        if(obj.t0){
          this.time = this.$moment(obj.t0).diff(this.$moment(),'milliseconds')
        }
        if(obj.t1){
          this.separate = this.$moment(obj.t1).diff(this.$moment(),'milliseconds')
        }
        this.size = obj.size || 5
        this.ignore = obj.ignore || 10 * 60 * 1000
        this.isMain=obj.isMain || false
      }
    }
  }
}
</script>
  
<style scoped>
.main{
  height: 100vh;
  width: 100wh;
}
</style>
  