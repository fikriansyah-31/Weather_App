<template>
  <div>
    <button @click="askForLocationPermission">Allow Access to Location</button>
  </div>
</template>

<script>
export default {
  methods: {
    async askForLocationPermission() {
      if ("geolocation" in navigator) {
        try {
          const position = await this.getCurrentPosition();
          const { latitude, longitude } = position.coords;
          this.$emit("location-granted", { latitude, longitude });
        } catch (error) {
          console.error("Error getting geolocation:", error);
          this.$emit("location-denied");
        }
      } else {
        console.error("Geolocation is not available in this browser.");
        this.$emit("location-denied");
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
