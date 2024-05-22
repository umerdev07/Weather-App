    @file:Suppress("DEPRECATION")

    package com.maths.weatherapp

    import ApiInterface
    import android.os.Bundle
    import android.view.Window
    import android.view.WindowManager
    import android.widget.SearchView
    import android.widget.Toast
    import androidx.appcompat.app.AppCompatActivity
    import com.maths.weatherapp.databinding.ActivityMainBinding
    import retrofit2.Call
    import retrofit2.Callback
    import retrofit2.Response
    import retrofit2.Retrofit
    import retrofit2.converter.gson.GsonConverterFactory
    import java.text.SimpleDateFormat
    import java.util.Date
    import java.util.Locale
    import java.util.TimeZone

    //92e157feb8cf6cbda855c8c5160b79e9
    class MainActivity : AppCompatActivity() {
        private val binding : ActivityMainBinding by lazy {
            ActivityMainBinding.inflate(layoutInflater)
        }
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
        requestWindowFeature(Window.FEATURE_NO_TITLE)
            this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN ,WindowManager.LayoutParams.FLAG_FULLSCREEN)
            getSupportActionBar()?.hide()
            setContentView(binding.root)
            fetchWeatherdata("Lahore")
            Searchview()
        }

        private fun Searchview() {
            val  searchview = binding.searchBar
            searchview.setOnQueryTextListener(object : SearchView.OnQueryTextListener{
                override fun onQueryTextSubmit(query: String?): Boolean {
                    if (query != null) {
                        fetchWeatherdata(query)
                    }
                    return true
                }

                override fun onQueryTextChange(newText: String?): Boolean {
                   return true
                }

            })
        }

        private fun fetchWeatherdata(cityname: String) {
            try {
                val retrofit = Retrofit.Builder()
                    .addConverterFactory(GsonConverterFactory.create())
                    .baseUrl("https://api.openweathermap.org/data/2.5/")
                    .build().create(ApiInterface::class.java)
                val response =
                    retrofit.getWeatherData(cityname, "92e157feb8cf6cbda855c8c5160b79e9", "metric")
                response.enqueue(object : Callback<WeatherApp> {
                    override fun onResponse(
                        call: Call<WeatherApp>,
                        response: Response<WeatherApp>
                    ) {
                        val responseBody = response.body()
                        if (response.isSuccessful && responseBody != null) {
                            val temperature = responseBody.main.temp
                            val maxtemp = responseBody.main.temp_max
                            val mintemp = responseBody.main.temp_min
                            val humumidity = responseBody.main.humidity
                            val windspeed = responseBody.wind.speed
                            val sunRise = responseBody.sys.sunrise
                            val sunSet = responseBody.sys.sunset
                            val condition = responseBody.weather.firstOrNull()?.main ?: "unknown"
                            val sealevel = responseBody.main.pressure
                            val cityname = responseBody.name
                            val timezoneOffset = responseBody.timezone

                            binding.temperature.text = "$temperature °C"
                            binding.maxTemp.text = "MAX: $maxtemp °C"
                            binding.minTemp.text = "MIN: $mintemp °C"
                            binding.humidity.text = "$humumidity %"
                            binding.wind.text = "$windspeed m/s"
                            binding.sunrise.text = "${time(sunRise.toLong())}"
                            binding.sunset.text = "${time(sunSet.toLong())}"
                            binding.weather.text = "$condition"
                            binding.weatherCondition.text = "$condition"
                            binding.sea.text = "$sealevel hPa"
                            binding.city.text = "$cityname"
                            binding.day.text = dayname(System.currentTimeMillis())
                            binding.date.text = date()
                            changeImageaccordingtoWeathercondition(condition, timezoneOffset,
                                sunRise.toLong(), sunSet.toLong()
                            )


                            displaylocaltime(timezoneOffset)
                        }
                    }

                    override fun onFailure(call: Call<WeatherApp>, t: Throwable) {
                        // Handle failure
                    }
                })
            } catch (e: Exception) {
                Toast.makeText(this, "Check your Internet Connection", Toast.LENGTH_SHORT).show()
            }
        }

        private fun displaylocaltime(timezoneOffset: Int) {
                 val currentmills = System.currentTimeMillis()
                 val localTimeMillis = currentmills + timezoneOffset * 1000L
                 val localTime = Date(localTimeMillis)
                 val sdf = SimpleDateFormat("hh:mm a", Locale.getDefault())
                 sdf.timeZone = TimeZone.getTimeZone("UTC")
                 binding.timezone.text = sdf.format(localTime)
        }

        // Extension function to round double value to 2 decimal places



        private fun changeImageaccordingtoWeathercondition(conditions:String , timezoneOffset: Int, sunrise: Long, sunset: Long) {
            val utcTimeMillis = System.currentTimeMillis() - TimeZone.getDefault().rawOffset
            val localTimeMillis = utcTimeMillis + timezoneOffset * 1000L

            val isDaytime = localTimeMillis in (sunrise * 1000)..(sunset * 1000)
            val isNighttime = localTimeMillis !in (sunrise * 1000)..(sunset * 1000)

            if (isDaytime) {
                when (conditions) {
                    "Partly Clouds", "Overcast", "Mist", "Clouds", "Smoke", "Few Clouds" -> {
                        binding.root.setBackgroundResource(R.drawable.cloud_bg)
                        binding.lottieAnimationView.setAnimation(R.raw.cloud)
                    }
                    "Haze" -> {
                        binding.root.setBackgroundResource(R.drawable.haze)
                        binding.lottieAnimationView.setAnimation(R.raw.haze)
                    }
                    "Foggy" -> {
                        binding.root.setBackgroundResource(R.drawable.fog)
                        binding.lottieAnimationView.setAnimation(R.raw.fog)
                    }
                    "Clear Sky", "Sunny", "Clear" -> {
                        binding.root.setBackgroundResource(R.drawable.sun_bg)
                        binding.lottieAnimationView.setAnimation(R.raw.sun)
                    }
                    "Light Rain", "Rain", "Drizzle", "Moderate Rain", "Showers", "Heavy Rain" -> {
                        binding.root.setBackgroundResource(R.drawable.rain_bg)
                        binding.lottieAnimationView.setAnimation(R.raw.rain)
                    }
                    "Light snow", "Snow", "Moderate snow", "Heavy Snow" -> {
                        binding.root.setBackgroundResource(R.drawable.snow_bg)
                        binding.lottieAnimationView.setAnimation(R.raw.snow)
                    }
                    "Blizzard" -> {
                        binding.root.setBackgroundResource(R.drawable.blizzard)
                        binding.lottieAnimationView.setAnimation(R.raw.snow)
                    }
                    "Thunderstorm"->{
                        binding.root.setBackgroundResource(R.drawable.thunderstrom)
                        binding.lottieAnimationView.setAnimation(R.raw.thunder)
                    }
                }
            } else if (isNighttime) {
                when (conditions) {
                    "Partly Clouds", "Overcast", "Mist", "Clouds", "Smoke", "Few Clouds" -> {
                        binding.root.setBackgroundResource(R.drawable.night_cloud)
                        binding.lottieAnimationView.setAnimation(R.raw.cloud)
                    }
                    "Haze" -> {
                        binding.root.setBackgroundResource(R.drawable.night_haze)
                        binding.lottieAnimationView.setAnimation(R.raw.haze)
                    }
                    "Foggy" -> {
                        binding.root.setBackgroundResource(R.drawable.night_fog)
                        binding.lottieAnimationView.setAnimation(R.raw.fog)
                    }
                    "Clear Sky", "Clear" -> {
                        binding.root.setBackgroundResource(R.drawable.night_sky)
                        binding.lottieAnimationView.setAnimation(R.raw.moon)
                    }
                    "Light Rain", "Rain", "Drizzle", "Moderate Rain", "Showers", "Heavy Rain" -> {
                        binding.root.setBackgroundResource(R.drawable.night_rain)
                        binding.lottieAnimationView.setAnimation(R.raw.rain)
                    }
                    "Light snow", "Snow", "Moderate snow", "Heavy Snow" -> {
                        binding.root.setBackgroundResource(R.drawable.night_snow)
                        binding.lottieAnimationView.setAnimation(R.raw.snow)
                    }
                    "Blizzard" -> {
                        binding.root.setBackgroundResource(R.drawable.night_blizzard)
                        binding.lottieAnimationView.setAnimation(R.raw.snow)
                    }
                    "Thunderstorm"->{
                        binding.root.setBackgroundResource(R.drawable.thunderstrom)
                        binding.lottieAnimationView.setAnimation(R.raw.thunder)
                    }
                }
            } else {
                // Handle other cases or show a default image
            }

            binding.lottieAnimationView.playAnimation()
        }

        private fun date(): String {
            val sdf = SimpleDateFormat("dd MM yyyy", Locale.getDefault())
            return sdf.format(Date())
        }
        private fun time(timestamp:Long): String {
            val sdf = SimpleDateFormat("HH:mm", Locale.getDefault())
            return sdf.format(Date(timestamp *1000))
        }
        fun dayname (timestamp: Long) :String{
            val sdf = SimpleDateFormat("EEEE" , Locale.getDefault())
            return sdf.format((Date()))
        }
    }