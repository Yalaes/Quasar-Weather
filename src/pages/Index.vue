<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
        bottom-slots
        v-model="search"
        @keyup.enter="getWeatherByCity"
        placeholder="city, country code "
        dark
        borderless
      >
        <template v-slot:before>
          <q-icon name="my_location" @click="getLocation" />
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="search" @click="getWeatherByCity" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData" >
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">{{ weatherData.name }}</div>
        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main }}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          {{ Math.round(weatherData.main.temp) }}
          <span class="text-h4 relative-position deg">&deg;C</span>
        </div>
      </div>

      <div class="col text-center">
        <img
          :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
          alt=""
        />
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">Quasar<br />Weather</div>
        <q-btn class="col" flat @click="getLocation">
          <q-icon left size="3em" name="my_location" />
          <div>FInd my location</div>
        </q-btn>
      </div>
    </template>
    <footer></footer>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "PageIndex",
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      long: null,
      unit: "metric",
      key: "8006a2da8b9cda16e5ca41aa433521ce",
      apiUrl: "https://api.openweathermap.org/data/2.5/weather",
    };
  },
  methods: {
    getLocation() {
      navigator.geolocation.getCurrentPosition((position) => {
        this.lat = position.coords.latitude;
        this.long = position.coords.longitude;
        this.getWeatherByCoord();
      });
    },
    getWeatherByCoord() {
      this.$q.loading.show()
      this.$axios(
        `${this.apiUrl}?lat=${this.lat}&lon=${this.long}&units=${this.unit}&appid=${this.key}`
      ).then((response) => {
        this.weatherData = response.data;
        this.$q.loading.hide()
      }).catch( err => {
        this.$q.loading.hide()
        
      })
    },
    getWeatherByCity() {
      this.$q.loading.show()
      this.$axios(
        `${this.apiUrl}?q=${this.search}&units=${this.unit}&appid=${this.key}`
      ).then((response) => {
        this.weatherData = response.data;
        this.$q.loading.hide()
      }).catch(err => {
        this.$q.loading.hide()
      })
    },
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith("n")) return "bg-Night";
        else return "bg-Day";
      }
    },
  },
});
</script>

<style lang="scss">
.q-page {
  background: #575b77;
  background: -webkit-linear-gradient(top, #575b77, #343543);
  background: -moz-linear-gradient(top, #575b77, #343543);
  background: linear-gradient(to bottom, #575b77, #343543);
  &.bg-Night {
    background: #2b4353;
    background: -webkit-linear-gradient(top, #2b4353, #15172c);
    background: -moz-linear-gradient(top, #2b4353, #15172c);
    background: linear-gradient(to bottom, #2b4353, #15172c);
  }
  &.bg-Day {
    background: #417495;
    background: -webkit-linear-gradient(top, #417495, #1f2772);
    background: -moz-linear-gradient(top, #417495, #1f2772);
    background: linear-gradient(to bottom, #417495, #1f2772);
  }
}

.deg {
  top: -2.9rem;
  left: -1.3rem;
}

footer {
  height: 5vh;
}
.q-icon {
  cursor: pointer;
}
</style>
