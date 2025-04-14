<template>
  <!-- The :class directive checks if the isWarm computed property is true (i.e., if the temperature is greater than 20°C), and if so, it applies the class warm to the root div. This class dynamically changes the background image based on the temperature. -->
  <div id="app" :class="isWarm ? 'warm' : ''">
    <main>
      <!-- The search-box contains an input field (search-bar), where the user can type a city or location. The v-model directive binds the input field to the query data property, allowing the user to update the location they want to search for.
The @keypress="fetchWeather" directive calls the fetchWeather method when the user presses a key (e.g., Enter) while the input field is focused. -->
      <div class="search-box">
        <input type="text" class="search-bar" placeholder="Search..." v-model="query" @keypress="fetchWeather" />
      </div>
      <!-- When weather data (weather.main) is available, the weather details are displayed in the weather-wrap div.
The location-box shows the city name (weather.name) and the country (weather.sys.country).
The dateBuilder() method is used to generate and display the current date in a readable format (e.g., "Sunday 25 April 2021").
The temperature and weather conditions (like "Clear", "Rain", etc.) are displayed within the weather-box. The formattedTemp computed property is used to display the temperature rounded to the nearest integer. -->
      <div class="weather-wrap" v-if="weather.main">
        <div class="location-box">
          <div class="location">
            {{ weather.name }},
            {{ weather.sys.country }}
          </div>
          <div class="date">
            {{ dateBuilder() }}
          </div>
          <div class="weather-box">
            <div class="temp">
              {{ formattedTemp }}°C
            </div>
            <div class="weather">
              {{ weather.weather[0].main }}
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>
<script>
  export default {
    name: 'App',
    data() {
      return {
        api_key: process.env.VUE_APP_API_KEY,
        url_base: process.env.VUE_APP_API_BASE,
        query: '',
        weather: {},
      };
    },
    computed: {
      // Computed property to check if the temperature is greater than 20 to change the background image
      isWarm() {
        return this.weather.main && this.weather.main.temp > 20;
      },
      // Computed property to round temperature value not decimal
      formattedTemp() {
        return Math.round(this.weather.main.temp);
      }
    },
    //  This method fetches weather data when the user presses the "Enter" key while typing in the search bar. It constructs a URL with the location (query) and fetches the data from the OpenWeatherMap API. The units=metric parameter ensures that the temperature is returned in Celsius.
    // If the request is successful, the data is stored in the weather object.
    // If there’s an error, it logs an error message to the console.
    methods: {
      fetchWeather(event) {
        if (event.key === 'Enter' && this.query) {
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
      dateBuilder() {
        let d = new Date();
        let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
        let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
        let day = days[d.getDay()];
        let date = d.getDate();
        let month = months[d.getMonth()];
        let year = d.getFullYear();
        return `${day} ${date} ${month} ${year}`;
      }
    },
    mounted() {
  // Check if the browser supports geolocation
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
      position => {
        // Get latitude and longitude
        const latitude = position.coords.latitude;
        const longitude = position.coords.longitude;

        // Fetch the weather data for the current location using lat/lon
        fetch(`${this.url_base}?lat=${latitude}&lon=${longitude}&appid=${this.api_key}&units=metric`)
          .then(response => response.json())
          .then(data => {
            this.weather = data; // Update weather data with the fetched results
            this.query = `${data.name}, ${data.sys.country}`; // Set query to city name and country
            console.log('Weather data for current location:', this.weather);
          })
          .catch(error => {
            console.error('Error fetching weather data for current location:', error);
            // Fallback to a default location if fetching fails
            this.query = 'Nepal';
            this.fetchWeather({ key: 'Enter' });
          });
      },
      error => {
        console.error('Error fetching geolocation:', error);
        // Fallback to a default location if geolocation fails
        this.query = 'Nepal'; // Default location
        this.fetchWeather({ key: 'Enter' });
      }
    );
  } else {
    console.log('Geolocation is not supported by this browser.');
    // Fallback to a default location if geolocation is not supported
    this.query = 'Nepal';
    this.fetchWeather({ key: 'Enter' });
  }
},
    watch: {
      query(newQuery) {
        // Auto-fetch when query changes (if you want to fetch as the user types)
        //When we are typing a location it gives the data of locations that matches the spelling in search bar.
        if (newQuery) {
          this.fetchWeather({
            key: 'Enter'
          });
        }
      }
    }
  };
</script>
<style scoped>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  body {
    font-family: 'montserrat', sans-serif;
    background-color: #f0f0f0;
  }

  #app {
    background-image: url('./assets/coldy.jpeg');
    background-size: cover;
    background-position: bottom;
    transition: 0.4s;

  }

  #app.warm {
    background-image: url(./assets/warmy.jpg);
  }

  main {
    min-height: 100vh;
    padding: 25px;
    display: flex;
    flex-direction: column;



  }

  .search-box {
    width: 100%;
    margin-bottom: 30px;
  }

  .search-box .search-bar {
    display: block;
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

  .search-box .search-bar:focus {
    box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
    background-color: rgba(255, 255, 255, 0.75);
    border-radius: 16px 0px 16px 0px;
  }

  .location-box .location {
    color: #FFF;
    font-size: 32px;
    font-weight: 500;
    text-align: center;
    text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  }

  .location-box .date {
    color: #FFF;
    font-size: 20px;
    font-weight: 300;
    font-style: italic;
    text-align: center;
    text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  }

  .weather-box {
    text-align: center;
  }

  .weather-box .temp {
    display: inline-block;
    padding: 10px 25px;
    color: #FFF;
    font-size: 102px;
    font-weight: 900px;
    text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
    background-color: rgba(255, 255, 255, 0.25);
    border-radius: 16px;
    margin: 30px 0px;
    box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  }

  .weather-box .weather {
    color: #f0f0f0;
    ;
    font-size: 48px;
    font-weight: 400;
    text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  }
</style>