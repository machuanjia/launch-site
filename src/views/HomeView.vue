<template>
  <div v-if="current == 1" class="main"><LanchDown :time="time"/></div>
  <div v-if="current == 2" class="main"><SeparateDown :time="separate"/></div>
  <div v-if="current == 3" class="main"><Forcase :size="size" :value="datas"/></div>
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
      size:5,
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
      const { current = 1, datas = null,time,separate = 807000,size=5} = JSON.parse(settings)
      this.current = current
      if(datas){
        this.datas = datas
      }
      if(time){
        this.time = time  
      }
      this.separate = separate
      this.size = size
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
        if(obj.time){
          this.time = obj.time
        }
        this.separate = obj.separate
        this.size = obj.size || 5
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
  