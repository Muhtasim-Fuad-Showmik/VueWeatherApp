<template>
    <div>

    </div>
</template>

<script setup>
    import axios from 'axios';
    import { useRoute } from 'vue-router';

    const route = useRoute();
    const getWeatherData = async () => {
        try {
            const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&appid=bb97c3056aa8ce1c35113df190782271&units=imperial`);
            
            /**
             * Some calculations are necessary while using the application to access weather data 
             * all across different timezones. 9AM at your place might be 3PM elsewhere and we 
             * need to account for this.
             */
            // Calculating current date & time
            const localOffset = new Date().getTimezoneOffset() * 60000;
            const utc = weatherData.data.current.dt * 1000 + localOffset;
            weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;
            
            // Calciulating hourly weather offset
            weatherData.data.hourly.forEach((hour) => {
                const utc = hour.dt * 1000 + localOffset;
                hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
            });

            return weatherData;

        } catch(err) {
            console.log(err);
        }
    };
    const weatherData = await getWeatherData();
    console.log("Weather Data: ", weatherData);
</script>