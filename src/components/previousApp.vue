<template>
    <div id="app" :class="typeof weather.main !== 'undefined' && weather.main.temp > 20 ? 'warm' : ''">
      <main>
        <div class="search-box">
          <input type="text"
          class="search-bar"
          placeholder="search..."
          v-model="query"
          @keypress="fetchWeather"
          />
          
        </div>
        <div class="weather-wrap" v-if="weather.main">
          <div class="location-box">
            <div class="location">
              {{ weather.name }}, {{ weather.sys.country }}
            </div>
            <div class="date">
              {{ dateBuilder() }}
            </div>
            <div class="weather-box">
              <div class="temp">{{ Math.round(weather.main.temp) }}°c </div>
              <div class="weather">{{ weather.weather[0].main }}</div>
              
            </div>
          </div>
        </div>
      </main>
    </div>
    </template>
    
    <script>
    
    export default {
      name: 'App',
      data(){
        return {
          api_key: "779486a384c3e90d67a4306de8a1268c",
          url_base: "https://api.openweathermap.org/data/2.5/weather",
          query: '',
          weather: {}
        }
      },
      methods:{
        fetchWeather(event){
          if(event.key === 'Enter'){
            fetch(`${this.url_base}?q=${this.query}&appid=${this.api_key}&units=metric`)
            .then(response => response.json())
            .then(data => {
              this.weather = data;
              console.log(this.weather);
            })
            .catch(error => {
              console.error('Error fetching weather data:', error);
            });
          }
        },
        dateBuilder(){
          let d = new Date();
          let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
          let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
          let day = days[d.getDay()];
          let date = d.getDate();
          let month = months[d.getMonth()];
          let year = d.getFullYear();
          return `${day} ${date} ${month} ${year}`;
        }
      }
      
    }
    </script>
    <style scoped>
    *{
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    body{
      font-family: 'montserrat', sans-serif;
      background-color: #f0f0f0;
    }
    #app{
      background-image:url('./assets/coldy.jpeg');
      background-size: cover;
      background-position: bottom;
      transition: 0.4s;
    
    }
    #app.warm{
      background-image: url(./assets/warmy.jpg);
    }
    main{
      min-height:100vh ;
      padding: 25px;
      display: flex;
      flex-direction: column;
      
      
      
    }
    .search-box{
      width: 100%;
     margin-bottom:30px;
    }
    .search-box .search-bar{
      display:block;
      width: 100%;
      padding: 15px;
      color: #313131;
      font-size: 20px;
      border: none;
      appearance: none;
      outline: none;
      background: none;
      border-radius: 0px 16px 0px 16px;
      transition: 0.4s;
      background-color: rgba(255, 255, 255, 0.5);
    }
    .search-box .search-bar:focus{
      box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
      background-color: rgba(255, 255, 255, 0.75);
      border-radius: 16px 0px 16px 0px;
    }
    .location-box .location{
      color:#FFF;
      font-size: 32px;
      font-weight: 500;
      text-align: center;
      text-shadow:1px 3px rgba(0, 0, 0, 0.25);
    }
    .location-box .date{
      color:#FFF;
      font-size: 20px;
      font-weight: 300;
      font-style: italic;
      text-align: center;
      text-shadow:1px 3px rgba(0, 0, 0, 0.25);
    }
    
    .weather-box{
      text-align: center;
    }
    .weather-box .temp{
     display: inline-block;
     padding: 10px 25px;
     color:#FFF;
     font-size:102px;
     font-weight:900px;
      text-shadow:3px 6px rgba(0, 0, 0, 0.25);
      background-color: rgba(255,255, 255, 0.25);
      border-radius: 16px;
      margin: 30px 0px;
      box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
    }
    .weather-box .weather{
      color: #f0f0f0;;
      font-size: 48px;
      font-weight: 400;
      text-shadow:3px 6px rgba(0, 0, 0, 0.25);
    }
    </style>
    
    