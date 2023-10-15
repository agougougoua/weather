<template>
  <div v-for="city in savedCities" :key="city.id" @click="goToCityView(city)">
    <CityCard :city="city" />
  </div>
  <p v-if="savedCities.length === 0">
    No Locations addedm.To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import CityCard from "./CityCard.vue";
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
const savedCities = ref([]);
const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
    console.log(savedCities);
    const requests = [];
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
        )
      );
      // console.log(requests);
    });

    const weatherData = await Promise.all(requests);
    // await new Promise((res) => setTimeout(res, 1000));
    console.log(weatherData);
    weatherData.forEach((value, index) => {
      savedCities.value[index].weather = value.data;
      console.log(savedCities);
      console.log(savedCities.value[index].weather);
    });
  }

  // 这段代码的主要功能是从本地存储中获取城市数据，然后为每个城市获取天气数据，并将这些天气数据与城市数据关联起来。
};
const router = useRouter();
await getCities();
const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: {
      id: city.id,
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  });
};
</script>
