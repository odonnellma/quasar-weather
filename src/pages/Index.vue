<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
          <q-input 
            v-model="search" 
            placeholder="Search"
            @keyup.enter="getWeatherBySearch"
            dark
            borderless 
          >
            <template v-slot:before>
              <q-icon  
                @click="getLocation"
                name="my_location"
              />
            </template>

        <template v-slot:append>
          <q-btn 
            @click="getWeatherBySearch"
            round 
            dense 
            flat 
            icon="search" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
         {{ weatherData.name }}
        </div>
       <div class= "text-h6 text-weight-light">
          {{ weatherData.weather[0].main}}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp) }}</span>
          <span class="text-h4 relative-position degree">&deg;F</span>
        </div>
      </div>
      <div class="col text-center">
       <img :src="`https://openweathermap.org/img/wn/${ weatherData.weather[0].icon }@2x.png`">
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar<br>Weather
        </div>
        <q-btn 
          @click="getLocation"
          class="col" 
          flat
        >
          <q-icon left size="3em" 
          name="my_location" />
          <div>Find my location</div>
        </q-btn>
      </div>
    </template>
    <div class="col skyline">
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: '',
      weatherData: null,
      lat: null,
      long: null,
      apiKey: '3da36ec2fff7d63e3ce61debc0d74a81',
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather'
    }
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith('n')){
          return 'bg-night'
        }
        else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation() {
      navigator.geolocation.getCurrentPosition
      (position => {
        console.log('position: ', position)
        this.lat= position.coords.latitude
        this.long= position.coords.longitude
        this.getWeatherByCoords()
      })
    },
    getWeatherByCoords() {
      console.log('lat: ', this.lat)
      this.$axios(`${ this.apiUrl }?lat=${ this.lat }&lon=${ this.long }&appid=${ this.apiKey }&units=imperial`).then(response=> { 
        console.log('response: ', response)
        this.weatherData= response.data
      })
    },
    getWeatherBySearch() {
      console.log('lat: ', this.lat)
      this.$axios(`${ this.apiUrl }?q=${ this.search }&appid=${ this.apiKey }&units=imperial`).then(response=> { 
        console.log('response: ', response)
        this.weatherData= response.data
      })
    }
  }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to top, #200122, #6f0000)
    &.bg-night
      background: linear-gradient(to top, #0f2027, #203a43, #2c5364)
    &.bg-day  
      background: linear-gradient(to top, #2980b9, #6dd5fa)

  .degree
    top: -44px
  /*
  .skyline
    flex: 0 0 200px
    background: url(../statics/skyline.png)
    background-size: contain
    background-position: center bottom */
</style>
