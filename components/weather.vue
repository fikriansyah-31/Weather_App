<template>
    <div class="bg-blue-300 flex justify-center">
      <div class="bg-blue-200 p-8 rounded-lg shadow-lg lg:w-2/4 xl:w-2/4 ms-2">
        <h2 class="text-3xl font-semibold mb-4">Weather Today</h2>
        <div class="flex flex-col lg:flex-row">
          <div class="flex-1">
            <div class="flex items-center mb-4">
              <span class="text-xl font-semibold mr-2">Day:</span>
              <span class="text-xl">{{ dayOfWeek }}</span>
            </div>
            <div class="flex items-center mb-4">
              <span class="text-xl font-semibold mr-2">City:</span>
              <span class="text-xl">{{ name }} - {{ country }}</span>
            </div>
            <div class="flex items-center mb-4">
              <span class="text-xl font-semibold mr-2">Weather:</span>
              <span class="text-xl">{{ description }}</span>
              <img v-if="iconUrl" :src="iconUrl" alt="Weather Icon" class="ml-2" />
            </div>
            <div class="flex items-center mb-4">
              <span class="text-xl font-semibold mr-2">Temperature:</span>
              <span class="text-xl">{{ temperature }}°C</span>
            </div>
            <div class="flex items-center mb-4">
              <span class="text-xl font-semibold mr-2">Date:</span>
              <span class="text-xl">{{ date }}</span>
            </div>
            <div class="flex items-center mb-4">
              <span class="text-xl font-semibold mr-2">Time:</span>
              <span class="text-xl">{{ time }}</span>
            </div>
          </div>
          <div class="flex-1">
            <div class="flex items-center mb-4">
              <span class="text-xl font-semibold mr-2">Humidity:</span>
              <span class="text-xl">{{ humidity }}%</span>
            </div>
            <div class="flex items-center mb-4">
              <span class="text-xl font-semibold mr-2">Wind:</span>
              <span class="text-xl">{{ wind }} m/s</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    props: {
      city: {
        type: String,
        required: true
      }
    },
    data() {
      return {
        cityname: this.city,
        temperature: null,
        description: null,
        iconUrl: null,
        name: null,
        date: null,
        time: null,
        dayOfWeek: null,
        humidity: null, 
        wind: null,
        country: null,
        monthName: [
          'January', 'February', 'March', 'April', 'May', 'June', 'July',
          'August', 'September', 'October', 'November', 'December'
        ]
      };
    },
    async created() {
      try {
        const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=9c2271a2d650fc3a5de255638a4a7a05`);
        const weatherData = response.data;
  
        if (weatherData.cod === '404') {
          this.description = 'City not found';
        } else {
          this.temperature = Math.round(weatherData.main.temp);
          this.description = weatherData.weather[0].description;
          this.name = weatherData.name;
          this.country = weatherData.sys.country;
          const d = new Date(weatherData.dt * 1000);
          this.date = `${d.getDate()} - ${this.monthName[d.getMonth()]} - ${d.getFullYear()}`;
          this.time = `${d.getHours()}:${d.getMinutes()}:${d.getSeconds()}`;
          this.dayOfWeek = this.getDayOfWeek(d.getDay());
  
          if (weatherData.weather[0].icon) {
            this.iconUrl = `https://openweathermap.org/img/wn/${weatherData.weather[0].icon}.png`;
          }
  
          this.wind = weatherData.wind.speed;
          this.humidity = weatherData.main.humidity; 
        }
  
        console.log(weatherData);
      } catch (error) {
        console.error(error);
        this.description = 'City not found';
      }
    },
    methods: {
      getDayOfWeek(dayIndex) {
        const daysOfWeek = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
        return daysOfWeek[dayIndex];
      }
    }
  };
  </script>
  
  <style>
  /* Add custom styles here if needed */
  </style>
  