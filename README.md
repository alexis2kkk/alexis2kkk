/root
  /index.html
 const url = `http://api.openweathermap.org/data/2.5/forecast?q=${CITY}&appid=${API_KEY}&units=metric`;
const url = `https://api.openweathermap.org/data/2.5/forecast?q=${CITY}&appid=${API_KEY}&units=metric`;
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pronóstico del Tiempo - Buenos Aires</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .container {
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        h1 {
            text-align: center;
        }
        .forecast {
            margin-top: 20px;
        }
        .day-forecast {
            margin-bottom: 20px;
        }
        .time-temp {
            margin-left: 20px;
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>Pronóstico del Tiempo - Buenos Aires</h1>
        <div id="forecast" class="forecast">
            Cargando pronóstico...
        </div>
    </div>

    <script>
        const API_KEY = "0896da0a504640e500504defa8267262";
        const CITY = "Buenos Aires";
        const url = `https://api.openweathermap.org/data/2.5/forecast?q=${CITY}&appid=${API_KEY}&units=metric`;

        async function getForecast() {
            try {
                const response = await fetch(url);
                if (!response.ok) {
                    throw new Error(`Error en la solicitud: ${response.statusText}`);
                }
                const data = await response.json();
                return processForecast(data);
            } catch (error) {
                document.getElementById('forecast').innerText = error.message;
            }
        }

        function processForecast(data) {
            const forecastList = data.list;
            const daysForecast = {};

            forecastList.forEach(forecast => {
                const date = new Date((forecast.dt * 1000) - (3 * 60 * 60 * 1000)).toISOString().split('T')[0];
                const time = new Date((forecast.dt * 1000) - (3 * 60 * 60 * 1000)).toISOString().split('T')[1].substring(0, 5);

                if (!daysForecast[date]) {
                    daysForecast[date] = [];
                }

                daysForecast[date].push({
                    time: time,
                    temp: forecast.main.temp,
                    weather: forecast.weather[0].description
                });
            });

            return daysForecast;
        }

        function displayForecast(forecast) {
            const forecastContainer = document.getElementById('forecast');
            forecastContainer.innerHTML = '';

            Object.entries(forecast).slice(0, 3).forEach(([date, forecastInfo]) => {
                const dayForecast = document.createElement('div');
                dayForecast.className = 'day-forecast';

                const dateTitle = document.createElement('h2');
                dateTitle.innerText = `Pronóstico para el ${date}`;
                dayForecast.appendChild(dateTitle);

                forecastInfo.forEach(info => {
                    const timeTemp = document.createElement('p');
                    timeTemp.className = 'time-temp';
                    timeTemp.innerText = `Hora: ${info.time} - Temp: ${info.temp}°C - Clima: ${info.weather}`;
                    dayForecast.appendChild(timeTemp);
                });

                forecastContainer.appendChild(dayForecast);
            });
        }

        getForecast().then(displayForecast);
    </script>

</body>
</html>

