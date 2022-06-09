<template>
  <div>
    <div class="container">
      <div class="title_box">
        <div class="controlIndex">
          <img
            src="../assets/arrow_icon.png"
            alt=""
            class="setIndexBtn"
            @click="minusOne()"
          />
        </div>
        <div class="displayCity">
          <p style="margin: 0px 0px; font-size: 50px">
            {{ cityList[chooseIndex] }}
          </p>
        </div>
        <div class="controlIndex">
          <img
            src="../assets/arrow_icon.png"
            alt="arrow"
            style="transform: rotateY(180deg)"
            class="setIndexBtn"
            @click="plusOne()"
          />
        </div>
      </div>
      <div class="weatherContainer" style="display: flex">
        <div class="weatherContent" style="display: flex">
          <div class="weatherCondition">
            <img
              :src="weatherIcon"
              alt="weatherIcon"
              style="max-width: 150px"
            />
            {{ now_area.weather }}
          </div>
          <div class="weatherTemperature">
            <img
              src="../assets/icons8-thermometer-96.png"
              alt="thermometer"
              style="max-width: 150px"
            />
            {{ now_area.low }}°C ~ {{ now_area.high }}°C
          </div>
        </div>
        <div class="weatherDetail">
          <!-- // TODO 詳細資料 -->
          <div
            v-for="(item, index) in detail_now_area"
            :key="index"
            class="weatherDetailBox"
          >
            <div
              style="
                display: flex;
                align-items: center;
                justify-content: center;
              "
            >
              <img
                src="../assets/icons8-alarm-clock-96.png"
                alt="clock"
                style="width: 50px"
              />
              {{ item.startTime.getMonth() + 1 }}/
              {{ item.startTime.getDate() }}({{
                getDay(item.startTime.getDay())
              }}) {{ item.startTime.getHours() }}時 ~
              {{ item.endTime.getMonth() + 1 }}/ {{ item.endTime.getDate() }}({{
                getDay(item.endTime.getDay())
              }}) {{ item.endTime.getHours() }}時
            </div>
            <div
              style="
                display: flex;
                align-items: center;
                justify-content: center;
              "
            >
              <img
                src="../assets/icons8-thermometer-96.png"
                alt="temperature"
                style="width: 50px"
              />
              {{ item.low }}°C ~ {{ item.high }}°C
            </div>
            <div
              style="
                display: flex;
                align-items: center;
                justify-content: center;
              "
            >
              <img :src="weatherIcon" alt="weatherIcon" style="width: 50px" />
              {{ item.weather }}
            </div>
            <div
              style="
                display: flex;
                align-items: center;
                justify-content: center;
              "
            >
              <img
                src="../assets/icons8-rainwater-catchment-96.png"
                alt="PoP"
                style="width: 50px"
              />
              下雨機率:
              {{ item.Pop }} %
            </div>
          </div>
        </div>
      </div>
    </div>
    <div></div>
  </div>
</template>


<script>
let apiUrl =
  "https://opendata.cwb.gov.tw/api/v1/rest/datastore/F-C0032-001?Authorization=CWB-61D92288-280D-4C6B-AF30-6D22117E3857&format=JSON";

export default {
  name: "HomeView",
  data() {
    return {
      weatherData: [],
      cityList: [],
      chooseIndex: 0,
      today: {},
    };
  },
  computed: {
    now_area() {
      let data = {};
      let res = this.weatherData.find((obj) => {
        return obj.locationName === this.choose_city;
      });

      if (res) {
        let high = res.weatherElement.find((el) => el.elementName === "MaxT")
          .time[0].parameter.parameterName;
        let low = res.weatherElement.find((el) => el.elementName === "MinT")
          .time[0].parameter.parameterName;
        let weather = res.weatherElement.find((el) => el.elementName === "Wx")
          .time[0].parameter.parameterName;
        data = {
          place: this.choose_city,
          high: high,
          low: low,
          weather: weather,
        };
      }
      return data;
    },
    choose_city() {
      return this.cityList[this.chooseIndex];
    },
    weatherIcon() {
      let weather = this.now_area.weather + "";
      let iconName = "cloudy_icon.png";
      if (weather.indexOf("雲") !== -1)
        iconName = "icons8-partly-cloudy-day-96.png";
      else if (weather.indexOf("雷") !== -1)
        iconName = "icons8-cloud-lightning-96.png";
      else if (weather.indexOf("雨") !== -1)
        iconName = "icons8-cloud-umbrella-96.png";
      else if (weather.indexOf("晴") !== -1)
        iconName = "icons8-partly-cloudy-day-96.png";

      return require("@/assets/" + iconName);
    },
    detail_now_area() {
      let data = {};
      let dateArr = [];
      let res = this.weatherData.find((obj) => {
        return obj.locationName === this.choose_city;
      });
      for (let i = 0; i < 3; i++) {
        if (res) {
          let high = res.weatherElement.find((el) => el.elementName === "MaxT")
            .time[i].parameter.parameterName;
          let low = res.weatherElement.find((el) => el.elementName === "MinT")
            .time[i].parameter.parameterName;
          let weather = res.weatherElement.find((el) => el.elementName === "Wx")
            .time[i].parameter.parameterName;
          let startTime = res.weatherElement.find(
            (el) => el.elementName === "Wx"
          ).time[i].startTime;
          let endTime = res.weatherElement.find((el) => el.elementName === "Wx")
            .time[i].endTime;
          let Pop = res.weatherElement.find((el) => el.elementName === "PoP")
            .time[i].parameter.parameterName;
          let startTime_Date = new Date(startTime);
          let endTime_Date = new Date(endTime);
          data = {
            place: this.choose_city,
            high: high,
            low: low,
            weather: weather,
            startTime: startTime_Date,
            endTime: endTime_Date,
            Pop: Pop,
          };
          dateArr.push(data);
        }
      }

      return dateArr;
    },
  },
  methods: {
    plusOne() {
      if (this.cityList.length - 1 > this.chooseIndex) {
        this.chooseIndex++;
      } else {
        this.chooseIndex = 0;
      }
    },
    minusOne() {
      if (this.chooseIndex > 0) {
        this.chooseIndex--;
      } else {
        this.chooseIndex = this.cityList.length - 1;
      }
    },
    getDay(num) {
      let day_ = "";
      switch (num) {
        case 0:
          day_ += "日";
          break;
        case 1:
          day_ += "一";
          break;
        case 2:
          day_ += "二";
          break;
        case 3:
          day_ += "三";
          break;
        case 4:
          day_ += "四";
          break;
        case 5:
          day_ += "五";
          break;
        case 6:
          day_ += "六";
          break;
      }
      return day_;
    },
  },
  mounted() {
    // * 抓取API
    this.$http
      .get(apiUrl)
      .then((response) => {
        this.weatherData = response.data.records.location;
        for (let i = 0; i < this.weatherData.length; i++) {
          this.cityList.push(this.weatherData[i].locationName);
        }
        console.log(this.weatherData);
      })
      .catch((error) => console.log(error));

    // * 抓取日期
    let date = new Date();
    let year = date.getFullYear();
    let month =
      date.getMonth() + 1 < 10
        ? "0" + (date.getMonth() + 1)
        : date.getMonth() + 1;
    let day = date.getDate() < 10 ? "0" + date.getDate() : date.getDate();
    this.today = {
      year: year,
      month: month,
      day: day,
    };
  },
};
</script>


<style>
.container {
  font-family: "Share Tech Mono";
  font-size: 35px;
  color: black;
  margin: 0px 5vw;
}
.title_box {
  display: flex;
  height: 80px;
  justify-content: center;
  border: 1px black solid;
  border-radius: 15px;
  flex: 1;
  overflow: hidden;
  margin: 5px 0px;
}
.controlIndex {
  display: flex;
  flex: 1;
  align-items: center;
  background: rgba(0, 0, 0, 0.6);
  padding: 10px;
  justify-content: center;
}
.displayCity {
  background: rgba(0, 0, 0, 0.1);
  padding: 5px 30px;
  flex: 8;
  color: rgba(10, 10, 10, 0.67);
  font-weight: 700;
}
.setIndexBtn {
  cursor: pointer;
  width: 50px;
  height: 50px;
}

.weatherContent {
  width: 350px;
  color: white;
  flex-direction: column;
}

.weatherCondition {
  border: 1px black solid;
  border-radius: 15px;
  margin: 15px 0px;
  margin-right: 5px;
  padding: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.6);
  font-size: 35px;
  font-weight: bolder;
  height: 50%;
}
.weatherDetail {
  border: 1px black solid;
  border-radius: 15px;
  margin: 15px 0px;
  margin-left: 5px;
  padding: 10px;
  display: flex;
  background: rgba(0, 0, 0, 0.6);
  flex: 1;
  color: white;
  flex-direction: column;
  font-weight: bold;
}
.weatherTemperature {
  border: 1px black solid;
  border-radius: 15px;

  margin: 15px 0px;
  margin-right: 5px;
  padding: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.6);
  font-size: 35px;

  font-weight: bolder;
  height: 50%;
}

.weatherDetailBox {
  padding: 5px;
  margin-bottom: 15px;
}
.weatherDetailBox:hover {
  background: rgba(0, 0, 0, 0.5);
}

.weatherContent img:hover {
  width: 105%;
}
@media (max-width: 1024px) {
  .weatherContent {
    flex-direction: row;
    width: 100%;
  }
  .weatherCondition,
  .weatherTemperature {
    width: 50%;
    height: auto;
  }
  .weatherContainer {
    flex-direction: column;
  }
}
@media (max-width: 481px) {
  .container,
  .weatherCondition,
  .weatherTemperature {
    font-size: 20px;
  }
}
</style>
