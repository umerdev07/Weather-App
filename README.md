# WeatherApp

WeatherApp is an Android application that provides real-time weather updates for different cities. The app features a splash screen, a main activity for displaying weather information, and dynamic UI changes based on the weather conditions.

## Features

- Splash screen that transitions to the main activity
- Displays current weather information, including temperature, humidity, wind speed, and more
- Search functionality to find weather information for different cities
- Dynamic background and animations based on the weather conditions and time of day
- Displays local time, date, and day name

   ```gradle
   dependencies {
       implementation("com.airbnb.android:lottie:6.4.0")
       implementation ("com.squareup.retrofit2:converter-gson:2.9.0")
       implementation ("com.squareup.retrofit2:retrofit:2.9.0")
   }
   ```

4. Make sure to add the necessary permissions to your `AndroidManifest.xml` file:

   ```xml
   <uses-permission android:name="android.permission.INTERNET" />
   ```

5. Build the project and run it on an emulator or a physical device.

## Usage

1. Launch the application on your Android device.
2. The splash screen will appear for 3 seconds.
3. After 3 seconds, the main activity will be displayed with weather information for the default city (Lahore).
4. Use the search bar to enter the name of any city to get its weather information.

## Code Overview

### SplashActivity.kt

The splash activity handles the display of the splash screen and transitions to the main activity after a delay.

#### Important Methods

- `onCreate(Bundle savedInstanceState)`: Sets up the splash screen by hiding the action bar and setting the window to full screen. It also initializes a handler to transition to the main activity after 3 seconds.

### MainActivity.kt

The main activity handles the display of weather information and allows users to search for weather information for different cities.

#### Important Methods

- `onCreate(Bundle savedInstanceState)`: Initializes the UI components, hides the action bar, and sets up the search view and initial weather data fetch.
- `fetchWeatherdata(String cityname)`: Fetches weather data for the specified city using the OpenWeatherMap API.
- `Searchview()`: Sets up the search view to handle user input and fetch weather data based on the search query.
- `changeImageaccordingtoWeathercondition(String conditions, Int timezoneOffset, Long sunrise, Long sunset)`: Updates the background and animations based on the weather conditions and time of day.
- `displaylocaltime(Int timezoneOffset)`: Displays the local time based on the city's timezone offset.

### Example Layouts

#### splash.xml

Here is a basic example of what your `splash.xml` file might look like:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@drawable/splash_background">

    <ImageView
        android:id="@+id/splash_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:src="@drawable/splash_logo" />
</RelativeLayout>
```

#### activity_main.xml

Here is a basic example of what your `activity_main.xml` file might look like:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.appcompat.widget.SearchView
        android:id="@+id/search_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:queryHint="Search city" />

    <!-- Add TextViews and other UI components for displaying weather information -->

</RelativeLayout>
```

## API Integration

WeatherApp uses the OpenWeatherMap API to fetch weather data. To use the API, you need an API key from OpenWeatherMap.

### Adding the API Key

Replace `"YOUR_API_KEY"` in the `fetchWeatherdata` method with your actual API key.

### Example Retrofit API Interface

Create an interface `ApiInterface.kt` to define the API endpoints:

```kotlin
import retrofit2.Call
import retrofit2.http.GET
import retrofit2.http.Query

import com.maths.weatherapp.WeatherApp
import retrofit2.Call
import retrofit2.http.GET
import retrofit2.http.Query

interface ApiInterface {
    @GET("weather")
    fun getWeatherData(
        @Query("q") city: String,
        @Query("appid") appid: String,
        @Query("units") units: String
    ): Call<WeatherApp>
}

```

### Example Data Model

Create a data model `WeatherApp.kt` to represent the JSON response from the API:

```kotlin
package com.maths.weatherapp

data class WeatherApp(
    val base: String,
    val clouds: Clouds,
    val cod: Int,
    val coord: Coord,
    val dt: Int,
    val id: Int,
    val main: Main,
    val name: String,
    val sys: Sys,
    val timezone: Int,
    val visibility: Int,
    val weather: List<Weather>,
    val wind: Wind
)

Thank you for using Weather App!
