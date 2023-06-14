<script setup>
import { onMounted, ref } from "vue";
//////// showDate

const date = new Date();
const days = [
  "Sunday",
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
];
const weekday = days[date.getDay()];

const months = [
  "January",
  "February",
  "March",
  "April",
  "May",
  "June",
  "July",
  "August",
  "September",
  "October",
  "November",
  "December",
];
const month = months[date.getMonth()];
const day = date.getDate();
const year = date.getFullYear();
const temp = ref(null);
const cityName = ref("Almaty");
const wind = ref(null);
const icon = ref(null);
const description = ref(null);
const loading = ref(false);

//////// showWeather

const inputValue = ref("Almaty");

function weatherBalloon() {
  const apiKey = "2051cb81bde7a8763309b769a547f6a5";
  const url = ref(
    `https://api.openweathermap.org/data/2.5/weather?q=${inputValue.value}&units=metric&appid=${apiKey}`
  );
  inputValue.value = "";
  fetch(url.value)
    .then(function (resp) {
      return resp.json();
    })
    .then(function (data) {
      loading.value = true;
      cityName.value = data.name;
      temp.value = Math.round(data.main.temp);
      wind.value = Math.round(data.wind.speed);
      icon.value = data.weather[0].icon;
      description.value = data.weather[0].main;
    })
    .catch(function (error) {
      console.log(error);
    })
    .finally(function () {
      loading.value = false;
    });
}
onMounted(() => {
  weatherBalloon();
});
</script>

<template>
  <div id="app" :class="temp < 16 ? 'cold' : 'warm'">
    <div class="wrapper-content">
      <div class="header">
        <div class="date badge rounded-pill text-bg-light">
          <p class="date__item">{{ `${weekday}` }}</p>
          <p class="date__item">{{ `${day} ${month}` }}</p>
          <p class="date__item">{{ `${year}` }}</p>
        </div>

        <div class="searcher">
          <input
            class="form-control me-2"
            type="search"
            placeholder="Search..."
            aria-label="Search"
            v-model="inputValue"
            @keyup.enter="weatherBalloon"
          /><button
            class="btn btn-outline-light"
            @click="weatherBalloon"
            type="submit"
          >
            Submit
          </button>
        </div>
      </div>

      <div class="weather">
        <h1 class="weather__city-name badge rounded-pill text-bg-light">
          {{ cityName }}
        </h1>
        <div class="weather__description">
          <h2 class="temp badge rounded-pill text-bg-light">{{ temp }} °C</h2>
          <div>
            <img
              v-if="icon"
              :src="`http://openweathermap.org/img/w/${icon}.png`"
              alt="icon"
            />
            <h3 class="badge badge rounded-pill text-bg-light">
              {{ description }}
            </h3>
            <div>
              <h3 class="badge badge rounded-pill text-bg-light">
                Wind: {{ wind }} m/s
              </h3>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
[v-cloak] {
  display: none;
}

* {
  box-sizing: border-box;
  margin: 0;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}
#app {
  display: flex;
  flex-direction: column;
  height: 100vh;
  align-items: center;
  margin: 0 auto;
  max-width: 500px;
  background-size: cover;
  font-size: 20px;
}
.warm {
  background-image: url("./assets/img/warm.jpeg");
}

.cold {
  background-image: url("./assets/img/cold.jpeg");
}

.header {
  display: flex;
  gap: 60px;
}
.date {
  .date__item {
    margin: 0;
  }
}
.searcher {
  display: inline-flex;
  align-items: center;
  input {
    height: 40px;
  }
  button {
    height: 40px;
  }
}
.wrapper-content {
  display: flex;
  flex-direction: column;
  gap: 50px;
  text-align: center;
  margin-top: 10px;
}

.weather {
  margin-top: 40px;
  .weather__city-name {
    font-size: 40px;
    padding: auto;
  }
}

.weather__description {
  display: flex;
  justify-content: space-around;
  align-items: center;
  margin-top: 60px;

  .temp {
    background-color: white;
    font-size: 40px;
  }
}
</style>
