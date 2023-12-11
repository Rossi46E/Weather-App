 }

        private async void Form1_Load(object sender, EventArgs e)
        {
            await GetAndDisplayWeather();
        }

        private async Task GetAndDisplayWeather()
        {
            string istanbulEndpoint = "https://api.openweathermap.org/data/2.5/weather?q=Istanbul&appid=4f47318f4de48bcd86655dba41dd833a";
            string izmirEndpoint = "https://goweather.herokuapp.com/weather/izmir";
            string ankaraEndpoint = "https://goweather.herokuapp.com/weather/ankara";

            try
            {
                WeatherData istanbulWeather = await GetWeatherData(istanbulEndpoint);
                WeatherData izmirWeather = await GetWeatherData(izmirEndpoint);
                WeatherData ankaraWeather = await GetWeatherData(ankaraEndpoint);

                DisplayWeatherData("İstanbul", istanbulWeather);
                DisplayWeatherData("İzmir", izmirWeather);
                DisplayWeatherData("Ankara", ankaraWeather);
            }
            catch (Exception ex)
            {
                MessageBox.Show($"An error occurred: {ex.Message}", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

        private async Task<WeatherData> GetWeatherData(string endpoint)
        {
            using (HttpClient client = new HttpClient())
            {
                HttpResponseMessage response = await client.GetAsync(endpoint);

                if (response.IsSuccessStatusCode)
                {
                    string json = await response.Content.ReadAsStringAsync();
                    WeatherData weatherData = JsonConvert.DeserializeObject<WeatherData>(json);
                    return weatherData;
                }
                else
                {
                    throw new HttpRequestException($"HTTP request failed with status code {response.StatusCode}");
                }
            }
        }

        private void DisplayWeatherData(string cityName, WeatherData weather)
        {
            MessageBox.Show($"{cityName} Weather:\nDescription: {weather.Description}\nTemperature: {weather.Temperature}\n\n3-Day Forecast:\n{GetForecastString(weather)}", cityName, MessageBoxButtons.OK, MessageBoxIcon.Information);
        }

        private string GetForecastString(WeatherData weather)
        {
            StringBuilder forecastString = new StringBuilder();

            foreach (var forecast in weather.Forecast)
            {
                forecastString.AppendLine($"Date: {forecast.Date}, Description: {forecast.Description}, Min: {forecast.MinTemperature}, Max: {forecast.MaxTemperature}");
            }

            return forecastString.ToString();
        }
    }

    class WeatherData
    {
        public string Description { get; set; }
        public string Temperature { get; set; }
        public Forecast[] Forecast { get; set; }
    }

    class Forecast
    {
        public string Date { get; set; }
        public string Description { get; set; }
        public string MinTemperature { get; set; }
        public string MaxTemperature { get; set; }
    }
}
