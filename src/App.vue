<template>
<div class="top-area">
  <div class="select-area" v-bind:style="{ backgroundImage: 'url(' + weather_image + ')' }">
    <div class="image">
      <img v-bind:src="weather_image" alt="">
    </div>
    <select v-model="checkedCity" @change="fetchWeather" class="form-select" aria-label="Default select example" name="cities" id="city">
      <option  value="istanbul">Istanbul</option>
      <option  value="izmir">Izmır</option>
      <option  value="ankara">Ankara</option>
    </select>  
    <div class="temp_container" v-if="weather.LocalObservationDateTime">
        <p class="weather_item text-center mt-2">feels like : <span style="font-weight:700">{{weather.RealFeelTemperature.Metric.Value}}°</span> </p>
        <p class="weather_item temperature text-center"> <span>{{weather.Temperature.Metric.Value}}°</span></p>        
        <p class="weather_item weather_condition" ><span>{{weather.WeatherText}}</span></p>
        <div class="weather_icons">
          <p><i class='fas fa-wind' style='font-size:24px;'></i> {{weather.WindGust.Speed.Metric.Value}}+km/h</p>        
          <p> <i class='fas fa-arrows-alt-v' style='font-size:24px'></i> {{weather.UVIndexText}}</p>
          <p><i class='fas fa-location-arrow' style='font-size:24px'></i>{{weather.Wind.Direction.Degrees}}{{weather.Wind.Direction.Localized}}</p>
        </div>
    </div>
  </div>
</div>
  <Chart v-bind:fiveDaystemperature="fiveDaystemperature" v-bind:fiveDays="fiveDays"/>
</template>

<script>
import Chart from "./components/Chart.vue";

export default {
  components: {
    Chart: Chart,
  },
  data() {
    return {
      url_base: 'https://raw.githubusercontent.com/ardatasci/frontend-recruitment-task/master/',
      weather: {},
      fiveDaystemperature: [],
      fiveDays: [],
      checkedCity: 'istanbul',
      weather_image: ' ',
    }
  },
  methods: {
    fetchWeather(e) {
      const storageData =JSON.parse(localStorage.getItem(e.target.value));
      if(storageData){
        this.setResults(storageData);
      } else {
        Promise.all([
        fetch(`${this.url_base}${e.target.value}CurrentWeather.json`).then(resp => resp.json()),
        fetch(`${this.url_base}${e.target.value}5DaysWeather.json`).then(resp => resp.json()),
      ]).then(res => this.setResults(res, e.target.value))
    }   
     localStorage.setItem('selectedCity',`${e.target.value}`);
    },
    setResults(results, city) {
    localStorage.setItem(city, JSON.stringify(results));
      const data = results[0][0];
      this.weather = data;
      this.weather_image = `https://developer.accuweather.com/sites/default/files/${'0'+data.WeatherIcon}-s.png`;
      this.fiveDaystemperature = [];
      this.fiveDays = [];      
      results[1].DailyForecasts.forEach((item=> {
        this.fiveDays.push(item.Date);
        this.fiveDaystemperature.push(item.Temperature.Maximum.Value);
      }))
    },
  },
   beforeMount(){
    const selectedCity = localStorage.getItem('selectedCity');
      const storageData =JSON.parse(localStorage.getItem(selectedCity ? selectedCity : 'istanbul'));
      if(storageData){
        this.setResults(storageData);
      } else {
      Promise.all([
      fetch(`${this.url_base}${selectedCity ? selectedCity : 'istanbul'}CurrentWeather.json`).then(resp => resp.json()),
      fetch(`${this.url_base}${selectedCity ? selectedCity : 'istanbul'}5DaysWeather.json`).then(resp => resp.json()),
    ]).then(this.setResults)  
    }
     this.checkedCity = selectedCity
 },
}
</script>

<style>
@import './style.css';

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
  background-image: url('../gorgeous-clouds-background-with-blue-sky-design_1017-25501.webp'); 
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-size: cover;
}
</style>

