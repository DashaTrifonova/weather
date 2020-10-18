<template>
  <div class="home">
    {{ status }}
    <div v-if="loading">Загружается</div>
    <div v-if="info">
      <h1>{{ mainInfo.city }}, {{ mainInfo.temp }}°</h1>
      <p>Влажность {{ mainInfo.humidity }}% Ветер {{ mainInfo.wind } }м/с</p>
      <p>{{ mainInfo.weather }}</p>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios';

export default {
  name: 'Home',
  data: () => ({
    status: '',
    info: null,
    loading: false,
  }),
  mounted() {
    if (!navigator.geolocation) {
      this.status = 'Разрешите доступ к геопозиции';
    } else {
      this.status = 'Определяю...';
      navigator.geolocation.getCurrentPosition(
        (position) => {
          this.status = '';
          this.getWeather(position.coords.latitude, position.coords.longitude);
        },
        () => {
          this.status = 'Разрешите доступ к геопозиции';
        },
      );
    }
  },
  computed: {
    mainInfo() {
      return {
        city: this.info.name,
        temp: Math.round(this.info.main.temp),
        humidity: Math.round(this.info.main.humidity),
        weather: this.info.weather[0].description,
        wind: Math.round(this.info.wind.speed),
      };
    },
  },
  methods: {
    getWeather(lat, long) {
      this.loading = true;
      axios
        .get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${long}&units=metric&lang=ru&appid=d0e89fc89523134758ba52e2afdb6674`,
        )
        .then((res) => {
          this.info = res.data;
        })
        .finally(() => {
          this.loading = false;
        });
    },
  },
};
</script>
