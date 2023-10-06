<template>
  <div class="bg-blue-300 min-h-screen">
    <div class="flex flex-col items-center">
      <div class="header container text-center my-10">
        <h1 class="text-4xl font-bold text-gray-700">Weather App</h1>
      </div>
      <div class="searchbar w-full md:w-3/4 mx-2 relative text-center right-6">
        <input
          v-model="city"
          type="text"
          class="input form-input sm:w-2/5 py-2 mt-2 md:mt-0 pr-8 pl-4 rounded-lg shadow-lg"
          placeholder="Enter a City Name"
        />
        <button
          @click="searchWeather"
          class="absolute top-0 mt-2 md:mt-0 m-2 p-2 text-white bg-blue-500 rounded-lg hover:bg-blue-600"
        >
          Search
        </button>
      </div>
    </div>
    <div class="mt-8">
      <LocationPermission
        v-if="!locationGranted"
        @location-granted="onLocationGranted"
        @location-denied="onLocationDenied"
      />
      <Weather :city="city" v-if="showWeather || locationGranted" />
    </div>
    <div class="mt-5">
      <Card :cityname="city" v-if="showWeather || locationGranted" />
    </div>
  </div>
</template>

<script>
import Weather from './components/weather.vue';
import Card from './components/card.vue';
import LocationPermission from "./components/locationPermission.vue";

export default {
  components: {
    Weather,
    Card,
    LocationPermission,
  },
  data() {
    return {
      city: '',
      showWeather: false,
      locationGranted: false,
      latitude: null,
      longitude: null,
    };
  },
  methods: {
    async searchWeather() {
      this.showWeather = false;
      await this.$nextTick();
      this.showWeather = true;
    },
    onLocationGranted({ latitude, longitude }) {
      this.latitude = latitude;
      this.longitude = longitude;
      this.locationGranted = true;

      // Sekarang, Anda dapat memanggil metode untuk mendapatkan data cuaca berdasarkan lokasi.
      // Misalnya: this.fetchWeatherDataByLocation(latitude, longitude);
    },
    onLocationDenied() {
      this.locationGranted = false;
      console.error("Location access denied.");
    },
    async loadWeatherByLocation() {
      if (navigator.geolocation) {
        try {
          const position = await this.getCurrentPosition();
          const { latitude, longitude } = position.coords;
          // Panggil metode untuk memuat cuaca berdasarkan lokasi
        this.fetchWeatherDataByLocation(latitude, longitude);
        } catch (error) {
          console.error("Error getting geolocation:", error);
        }
      }
    },
    getCurrentPosition() {
      return new Promise((resolve, reject) => {
        navigator.geolocation.getCurrentPosition(resolve, reject);
      });
    },
  },
};
</script>

<style>
/* Tambahkan styling sesuai kebutuhan Anda */
</style>
