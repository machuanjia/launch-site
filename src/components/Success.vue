<template>
  <div class="wrap">
    <div class="mask">
        <div class="container">
          <div class="main">
            <div class="header">
              <div class="logo">
                <img class="logo" src="@/assets/logo.pic.png" width="100" /> <span>银河航天</span>
              </div>
              <div class="slogen2">Make The Earth Better</div>
               <div class="slogen2">让地球更美好</div>
            </div>
            <div class="slogen">
              “诺亚”、“四象” 发射成功
            </div>
            <div class="footer">{{ currentDate() }} (北京)</div>
          </div>
        </div>
    </div>
  </div>
</template>

<script lang="ts">
import VueCountdown from '@chenfengyuan/vue-countdown'
export default {
  components: { VueCountdown },
  props:{
    time:{
      type: Number,
      readonly: true,
      default: 1 * 24 * 60 * 60 * 1000
    }
  },
  data() {
  },
  methods: {
    currentDate() {
      return this.$moment().format('YYYY-MM-DD HH:mm:ss')
    }
  },
  computed: {}
}
</script>
<style scoped>
.wrap {
  background: url('../assets/4.jpeg') no-repeat center bottom;
  background-size: 100% auto
}
.mask {
  height: 100vh;
  width: 100wh;
  background: rgba(0, 0, 0, 0.7);
}

.container {
  height: 100vh;
  width: 100wh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.main {
  max-width: 1120px;
}
.header {
  padding-bottom: 120px;
}
.logo {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 60px;
  color: #ffffff;
  margin-bottom: 10px;
  /* text-shadow: 0 0 5px #ffffff; */
}
.slogen {
  font-size: 80px;
  font-family: 'Microsoft YaHei';
  color: #ffffff;
  /* text-shadow: 0 0 5px #ffffff; */
  font-weight: bold;
  padding-bottom: 120px;
  text-align: center;
}

.slogen2{
  text-align: center;
  font-size: 20px;
  font-family: 'Microsoft YaHei';
  color: #ffffff;
  /* text-shadow: 0 0 5px #ffffff; */
}

.footer {
  text-align: center;
  font-size: 24px;
  font-weight: bolder;
  font-family: 'Lucida Console', Monaco, monospace;
  margin-top: 30px;
  /* text-shadow: 0 0 5px #ffffff; */
  margin-bottom: 60px;
  color: #ffffff;
}
</style>
