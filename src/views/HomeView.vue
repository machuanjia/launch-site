<template>
  <div v-if="current == 1" class="main"><LanchDown /></div>
  <div v-if="current == 2" class="main"><SeparateDown /></div>
  <div v-if="current == 3" class="main"><Forcase :value="datas"/></div>
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
      datas:{
        noah: {
          lable: 'Noah',
          list: [
            {
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-07 09:42:22',
              end: '2023-07-07 09:48:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            },{
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-07 09:41:00',
              end: '2023-07-07 09:49:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            },{
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-07 09:49:00',
              end: '2023-07-07 09:55:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            },{
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-07 09:50:00',
              end: '2023-07-07 09:59:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            },{
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-07 09:56:00',
              end: '2023-07-07 09:59:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            },{
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-07 10:50:00',
              end: '2023-07-07 10:59:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            },{
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-07 10:50:00',
              end: '2023-07-07 10:59:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            },{
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-07 10:50:00',
              end: '2023-07-07 10:59:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            }
          ]
        },
        as2: {
          lable: 'As-2',
          list: [
            {
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-06 19:39:22',
              end: '2023-07-06 19:47:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            }
          ]
        },
        as3: {
          lable: 'As-3',
          list: [
            {
              antenna: '阿塞拜疆-SX-4501-驭星',
              start: '2023-07-06 19:39:22',
              end: '2023-07-06 19:47:19',
              duration: '300',
              maxElevationAngle: '88.934',
              description: '备注',
            }
          ]
        }
      }
    }
  },
  created(){
    const settings = localStorage.getItem("settings")
    if(settings){
      const { current = 1 } = JSON.parse(settings)
      this.current = current
    }
  },
  mounted() {
    window.onstorage = (e) => {
      if (e.key === 'settings' && e.newValue) {
        console.log(e.newValue)
        const obj = JSON.parse(e.newValue)
        this.current = obj.current
      }
    }
    console.log(this.$refs.items)
  }
}
</script>
  
<style scoped>
.main{
  height: 100vh;
  width: 100wh;
}
</style>
  