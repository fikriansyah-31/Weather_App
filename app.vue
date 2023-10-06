<template>
  <div>
    <div v-if="locationPermissionStatus === 'granted'">
      <Weather :latitude="latitude" :longitude="longitude" v-if="showWeather || locationGranted" />
        <Card :latitude="latitude" :longitude="longitude" v-if="showWeather || locationGranted" />
    </div>
    <div v-else>
      <button @click="askForLocationPermission">Allow Access to Location</button>
    </div>
  </div>
</template>

<script>
import Weather from './components/weather.vue';
import Card from './components/card.vue';

export default {
  components: {
    Weather,
    Card
  },
  data() {
    return {
      city: '',
      latitude: null, // Latitude akan diisi otomatis saat izin lokasi diberikan
      longitude: null, // Longitude akan diisi otomatis saat izin lokasi diberikan
      showWeather: false,
      locationPermissionStatus: null
    };
  },
  methods: {
    async askForLocationPermission() {
      if (typeof navigator !== 'undefined' && 'permissions' in navigator) {
        try {
          const permissionStatus = await navigator.permissions.query({
            name: 'geolocation'
          });

          if (permissionStatus.state === 'granted') {
            this.locationPermissionStatus = 'granted';
            await this.loadWeatherByLocation();
          } else if (permissionStatus.state === 'prompt') {
            this.locationPermissionStatus = 'prompt';
          } else {
            this.locationPermissionStatus = 'denied';
          }
        } catch (error) {
          console.error("Error checking geolocation permission:", error);
          this.locationPermissionStatus = 'error';
        }
      } else {
        console.error("Geolocation permissions are not available in this environment.");
        this.locationPermissionStatus = 'unavailable';
      }
    },
    async loadWeatherByLocation() {
      if (navigator.geolocation) {
        try {
          const position = await this.getCurrentPosition();
          const { latitude, longitude } = position.coords;
          this.latitude = latitude;
          this.longitude = longitude;
          this.showWeather = true;
        } catch (error) {
          console.error("Error getting geolocation:", error);
          this.showWeather = false;
        }
      }
    },
    getCurrentPosition() {
      return new Promise((resolve, reject) => {
        navigator.geolocation.getCurrentPosition(resolve, reject);
      });
    },
  },
  created() {
    // Cek izin lokasi saat komponen dimuat
    this.askForLocationPermission();
  },
};
</script>

<style>
/* Tambahkan styling sesuai kebutuhan Anda */
</style>
