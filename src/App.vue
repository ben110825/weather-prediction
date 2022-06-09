<template>
  <div>
    <div class="header">
      天氣預報 demo
      <div class="clock">
        <div>現在時間:</div>
        <div>{{ nowTime.date }}</div>
        <div>{{ nowTime.time }}</div>
      </div>
    </div>
    <router-view></router-view>
    <div class="footer">builder: ben</div>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      nowTime: {}, // * 現在時間
    };
  },
  methods: {
    // * 格式化時間
    timeFormate(timeStamp) {
      let newTime = new Date(timeStamp);
      let year = newTime.getFullYear();
      let month =
        newTime.getMonth() + 1 < 10
          ? "0" + (newTime.getMonth() + 1)
          : newTime.getMonth() + 1;
      let date =
        newTime.getDate() < 10 ? "0" + newTime.getDate() : newTime.getDate();
      let hh =
        newTime.getHours() < 10 ? "0" + newTime.getHours() : newTime.getHours();
      let mm =
        newTime.getMinutes() < 10
          ? "0" + newTime.getMinutes()
          : newTime.getMinutes();
      let ss =
        newTime.getSeconds() < 10
          ? "0" + newTime.getSeconds()
          : newTime.getSeconds();
      this.nowTime = {
        date: year + "-" + month + "-" + date,
        time: hh + ":" + mm + ":" + ss,
      };
    },
    // * 計時器
    nowTimer() {
      let self = this;
      self.timeFormate(new Date());
      setInterval(function () {
        self.timeFormate(new Date());
      }, 1000);
    },
  },
  mounted() {
    // * 啟動計時器
    this.nowTimer();
  },
};
</script>

<style >
body {
  background-color: #2b2d42;
  background-image: url("./assets/taiwan.jpg");
  background-size: cover;
  background-repeat: no-repeat;
  background-blend-mode: lighten;
  margin: 0px;
  -ms-overflow-style: none;
  height: 100vh;
}
#app {
  text-align: center;
  height: 100vh;
}
.header {
  color: white;
  display: flex;
  justify-content: space-between;
  font-size: 35px;
  margin: 0px 10px;
  font-weight: bold;
}
.footer {
  background: rgba(0, 0, 0, 0.5);
  color: White;
  width: 100%;
  height: 100%;
  font-size: 25px;
}
</style>
