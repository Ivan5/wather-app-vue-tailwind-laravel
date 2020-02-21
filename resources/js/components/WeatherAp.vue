<template>
  <div class="text-white mb-8">
    <div class="places-input text-gray-800">
      <input type="text" class="w-full" />
    </div>
    <div
      class="weather-container font-sans w-128 max-w-lg rounded-lg overflow-hidden bg-gray-900 shadow-lg mt-4"
    >
      <div class="current-weather flex items-center justify-between px-6 py-8">
        <div class="flex items-center">
          <div>
            <div class="text-6xl font-semibold">{{currentTemperature.actual}}°C</div>
            <div>Feels like {{currentTemperature.feels}}°</div>
          </div>
          <div class="mx-5">
            <div class="font-semibold">{{currentTemperature.summary}}</div>
            <div>{{location.name}}</div>
          </div>
        </div>
        <div>
          <canvas ref="iconCurrent" id="iconCurrent" width="96" height="96"></canvas>
        </div>
      </div>

      <div class="future-weather text-sm bg-gray-800 px-6 py-8 overflow-hidden">
        <div
          class="flex items-center mt-8"
          v-for="(day,index) in daily"
          :key="day.time"
          :class="{'mt-8' : index > 0}"
          v-if="index < 5"
        >
          <div class="w-1/6 text-lg text-gray-200">{{toDayOfWeek(day.time)}}</div>
          <div class="w-4/6 px-4 flex items-center">
            <div>icon</div>
            <div class="ml-3">{{day.summary}}</div>
          </div>
          <div class="w-1/6 text-right">
            <div>{{Math.round(day.temperatureHigh)}}°C</div>
            <div>{{Math.round(day.temperatureLow)}}°C</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    this.fetchData();
  },
  data() {
    return {
      currentTemperature: {
        actual: "",
        feels: "",
        summary: "",
        icon: ""
      },
      daily: [],
      location: {
        name: "Jiutpec, México",
        lat: 18.8813896,
        lng: -99.1777802
      }
    };
  },
  methods: {
    fetchData() {
      var skycons = new Skycons({ color: "white" });

      fetch(`/api/weather?lat=${this.location.lat}&lng=${this.location.lng}`)
        .then(response => response.json())
        .then(data => {
          this.currentTemperature.actual = Math.round(
            data.currently.temperature
          );
          this.currentTemperature.feels = Math.round(
            data.currently.apparentTemperature
          );
          this.currentTemperature.summary = data.currently.summary;
          this.currentTemperature.icon = this.toKebabCase(data.currently.icon);

          this.daily = data.daily.data;

          skycons.add("iconCurrent", this.currentTemperature.icon);
          skycons.play();
        });
    },
    toKebabCase(stringToConvert) {
      return stringToConvert.split(" ").join("-");
    },
    toDayOfWeek(timestamp) {
      const newDate = new Date(timestamp * 1000);
      const days = ["SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"];

      return days[newDate.getDay()];
    }
  }
};
</script>
