<template>
  <div class="bg-blue-300 flex justify-center">
    <div class="bg-blue-200 p-8 rounded-lg shadow-lg lg:w-2/4 xl:w-2/4 ms-2">
      <div v-if="loading" class="loading">Loading...</div>
      <div v-else class="grid grid-cols-1 gap-4 sm:grid-cols-2 md:grid-cols-3">
        <div class="bg-blue-400 p-4 rounded-lg shadow-md" v-for="(day, index) in weatherData" :key="day.date">
          <div class="text-lg font-semibold py-3">{{ getDayName(day.date) }}</div>
          <div class="text-lg font-semibold py-3">
            <img :src="day.iconUrl" :alt="day.description" />
            {{ day.description }}
          </div>
          <div class="text-lg font-semibold py-3">{{ day.temperature }}°C</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';

export default {
  props: {
    latitude: Number,
    longitude: Number
  },
  data() {
    return {
      weatherData: [],
      loading: false
    };
  },
  mounted() {
    this.fetchWeatherData();
  },
  methods: {
    async fetchWeatherData() {
      const lat = this.latitude;
      const lon = this.longitude;
      const apiKey = '9c2271a2d650fc3a5de255638a4a7a05';
      const apiUrl = `https://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&units=metric&appid=${apiKey}`;

      try {
        this.loading = true;
        const response = await axios.get(apiUrl);
        const forecastData = response.data.list;
        const uniqueDates = new Set();
        this.weatherData = forecastData
          .filter(item => {
            const date = moment(item.dt * 1000).format('YYYY-MM-DD');
            if (!uniqueDates.has(date)) {
              uniqueDates.add(date);
              return true;
            }
            return false;
          })
          .slice(0, 3) 
          .map(item => ({
            temperature: Math.round(item.main.temp),
            description: item.weather[0].description,
            iconUrl: `https://openweathermap.org/img/wn/${item.weather[0].icon}.png`,
            date: moment(item.dt * 1000) 
          }));
        this.loading = false;
      } catch (error) {
        console.error(error);
        this.loading = false;
      }
    },
    getDayName(date) {
      return date.format('ddd'); 
    }
  }
};
</script>

<style>
@media screen and (max-width: 768px) {
  .grid {
    display: flex;
    flex-wrap: nowrap;
    overflow-x: auto;
  }
  .bg-blue-100 {
    min-width: 250px;
  }
}
</style>
