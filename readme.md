# Weather Web App

This is a web application built using **Java Servlets**, **JSP**, and the **OpenWeatherMap API**. It allows users to retrieve current weather information for a specified city.

---

### 1.Technologies Used

---

- Java
- Servlets
- JSP
- HTML
- CSS
- JavaScript
- OpenWeatherMap API
- Google Fonts

---

### 2.Getting Started

---

To run this application locally, you'll need a Java Development Environment (JDK) and a web server like Apache Tomcat.

1. Clone the repository or download the source code.
2. Import the project into your preferred Java IDE (e.g., Eclipse, IntelliJ IDEA).
3. Configure the web server to deploy the application.
4. Obtain an API key from the OpenWeatherMap API website (https://openweathermap.org/api).
5. Replace the placeholder API key in the `MyServlet.java` file with your actual API key.
6. Run the application and access it through the configured URL (e.g., `http://localhost:8080/WeatherApp`).

---

### 3.Usage

---

1. On the main page, enter the name of the city for which you want to retrieve weather information.
2. Click the search button or press Enter.
3. The application will fetch the current weather data from the OpenWeatherMap API and display it on the page.

---

### 4.Features

---

- User-friendly interface with a search input field and a submit button.
- Displays the current weather information for the specified city, including temperature, humidity, wind speed, and a weather icon representing the current conditions.
- Responsive design for optimal viewing on different devices.

---

### 5.File Structure and Code

---

- `MyServlet.java`: The main servlet class that handles the HTTP requests and integrates with the OpenWeatherMap API.

_[src/main/java/MyPackage/MyServlet.java](src/main/java/MyPackage/MyServlet.java)_

```java
package MyPackage;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Date;
import java.util.Scanner;

import com.google.gson.Gson;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;

/**
 * Servlet implementation class MyServlet
 */
public class MyServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#HttpServlet()
     */
    public MyServlet() {
        super();
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.getWriter().append("Served at: ").append(request.getContextPath());
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

		//API Setup
		String apiKey = "8071998b04156cb295e9e3c3b8336359";
		// Get the City name from the form's input
		String city = request.getParameter("city");
		// Create the URL for the OpenWeatherMap API request
		String apiUrl = "https://api.openweathermap.org/data/2.5/weather?q=" + city + "&appid=" + apiKey;

		// API Integration
		URL url = new URL(apiUrl);
		HttpURLConnection connection = (HttpURLConnection)url.openConnection();
		connection.setRequestMethod("GET");

		// Reading the data from the Network
		InputStream inputStream = connection.getInputStream();
		InputStreamReader inputStreamReader = new InputStreamReader(inputStream);

		// Store data in String
		StringBuilder responseContent = new StringBuilder();

		// To take input from inputStreamReader
		Scanner scanner = new Scanner(inputStreamReader);

		while(scanner.hasNext()) {
			responseContent.append(scanner.nextLine());
		}

		scanner.close();
		//System.out.println(responseContent);

		// TypeCasting the data into JSON (separate data)
		Gson gson = new Gson();
		JsonObject jsonObject = gson.fromJson(responseContent.toString(), JsonObject.class);
		//System.out.println(jsonObject);

		// SEPARATION of DATA

		// Date & Time
		long dateTimeStamp = jsonObject.get("dt").getAsLong() * 1000;
		String date = new Date(dateTimeStamp).toString();

		// Temperature
		double temperatureKelvin = jsonObject.getAsJsonObject("main").get("temp").getAsDouble();
		int temperatureCelsius = (int) (temperatureKelvin - 273.15);

		// Humidity
		int humidity = jsonObject.getAsJsonObject("main").get("humidity").getAsInt();

		// Wind Speed
		double windSpeed = jsonObject.getAsJsonObject("wind").get("speed").getAsDouble();

		// Weather Condition
		JsonElement weatherCondition = jsonObject.getAsJsonArray("weather").get(0).getAsJsonObject().get("main");

		// THIS DATA CANT BE PRESENTED BY HTML SO WE WILL USE JSP
		// Set the data as request attributes (for sending the jsp page)
		request.setAttribute("date", date);
		request.setAttribute("city", city);
		request.setAttribute("temperature", temperatureCelsius);
		request.setAttribute("weatherCondition", weatherCondition);
		request.setAttribute("humidity", humidity);
		request.setAttribute("windSpeed", windSpeed);
		request.setAttribute("weatherData", responseContent.toString());

		connection.disconnect();
		// Forward the request to the weather.jsp page for rendering
		request.getRequestDispatcher("index.jsp").forward(request, response);
	}
}
```

- `index.jsp`: The JSP file for the main page, containing the HTML, CSS, and JavaScript code.

_[src/main/webapp/index.jsp](src/main/webapp/index.jsp)_

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App</title>
     <link rel="stylesheet" href="style.css" />
     <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />
</head>

<body>

    <div class="mainContainer">
     <form action="MyServlet" method="post" class="searchInput">
            <input type="text" placeholder="Enter City Name" id="searchInput" value="New Delhi" name="city"/>
            <button id="searchButton"><i class="fa-solid fa-magnifying-glass"></i></button>
      </form>
        <div class="weatherDetails">
            <div class="weatherIcon">
                <img src="" alt="Clouds" id="weather-icon">
                <h2>${temperature} °C</h2>
                 <input type="hidden" id="wc" value="${weatherCondition}"> </input>
            </div>

            <div class="cityDetails">
                <div class="desc"><strong>${city}</strong></div>
                <div class="date">${date}</div>
            </div>
            <div class="windDetails">
            	<div class="humidityBox">
            	<img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhgr7XehXJkOPXbZr8xL42sZEFYlS-1fQcvUMsS2HrrV8pcj3GDFaYmYmeb3vXfMrjGXpViEDVfvLcqI7pJ03pKb_9ldQm-Cj9SlGW2Op8rxArgIhlD6oSLGQQKH9IqH1urPpQ4EAMCs3KOwbzLu57FDKv01PioBJBdR6pqlaxZTJr3HwxOUlFhC9EFyw/s320/thermometer.png" alt="Humidity">
                <div class="humidity">
                   <span>Humidity </span>
                   <h2>${humidity}% </h2>
                </div>
               </div>

                <div class="windSpeed">
                    <img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiyaIguDPkbBMnUDQkGp3wLRj_kvd_GIQ4RHQar7a32mUGtwg3wHLIe0ejKqryX8dnJu-gqU6CBnDo47O7BlzCMCwRbB7u0Pj0CbtGwtyhd8Y8cgEMaSuZKrw5-62etXwo7UoY509umLmndsRmEqqO0FKocqTqjzHvJFC2AEEYjUax9tc1JMWxIWAQR4g/s320/wind.png">
                    <div class="wind">
                    <span>Wind Speed</span>
                    <h2> ${windSpeed} km/h</h2>
                    </div>
                </div>
            </div>
        </div>
    </div>


    <script src="myScript.js"></script>

</body>
</html>
```

- `style.css`: The CSS file containing the styles for the application.

_[src/main/webapp/style.css](src/main/webapp/style.css)_

```css
@charset "ISO-8859-1";
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Ubuntu", sans-serif;
}

body {
  display: flex;
  height: 100vh;
  align-items: center;
  justify-content: center;
  background: linear-gradient(to right, #2b40ff, #07121a);
}

.mainContainer {
  width: 25rem;
  height: auto;
  border-radius: 1rem;
  background: #fff;
  box-shadow: 0 14px 28px rgba(0, 0, 0, 0.25), 0 10px 10px rgba(0, 0, 0, 0.22);
}

.searchInput {
  width: 100%;
  display: flex;
  padding: 1rem 1rem;
  justify-content: center;
}

.searchInput input {
  width: 100%;
  height: 2rem;
  outline: none;
  font-size: 1rem;
  color: #525050;
  padding: 0.2rem 0.5rem;
  border-radius: 1.5rem;
  border: 1px solid #b3b3b3;
}

.searchInput input:focus {
  border: 1px solid #9c9dde;
}

.searchInput button {
  width: 2.2rem;
  height: 2rem;
  cursor: pointer;
  color: #9b9b9b;
  border-radius: 50%;
  margin-left: 0.4rem;
  transition: all 0.3s ease;
  background-color: #fff;
  border: 1px solid #b3b3b3;
}

.searchInput button:hover {
  color: #fff;
  background-color: #9c9dde;
  border: 1px solid #9c9dde;
}

.weatherIcon {
  display: flex;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  justify-content: center;
}

.weatherIcon img {
  max-width: 100%;
  width: 8rem;
}

.weatherDetails .temp {
  font-size: 2rem;
}
.cityDetails {
  color: #323232;
  font-size: 2.5rem;
  text-align: center;
  margin-bottom: 0.5rem;
}

.cityDetails .date {
  color: #323232;
  font-size: 1.5rem;
  text-align: center;
  margin-bottom: 0.5rem;
}

.windDetails {
  display: flex;
  margin-top: 1rem;
  margin-bottom: 1.5rem;
  justify-content: space-around;
}

.windDetails .humidityBox {
  display: flex;
  font-size: 1rem;
  color: #323232;
}

.windDetails .humidity .humidityValue {
  text-align: center;
  font-size: 2rem;
  color: #323232;
}

.windDetails .humidityBox img {
  max-height: 3rem;
  margin-right: 0.5rem;
}

.windDetails .windSpeed {
  display: flex;
  font-size: 1rem;
  color: #323232;
}

.windDetails .windSpeed img {
  max-height: 3rem;
  margin-right: 0.5rem;
}

image-logo {
  height: auto;
  width: auto;
  filter: drop-shadow(20px 10px 10px black);
}
```

- `myScript.js`: The JavaScript file containing the code for dynamically updating the weather icon based on the current weather conditions.

_[src/main/webapp/myScript.js](src/main/webapp/myScript.js)_

```js
var weatherIcon = document.getElementById("weather-icon");

var val = document.getElementById("wc").value;
switch (val) {
  case "Clouds":
    weatherIcon.src =
      "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwFTkt5z_dxU6w1UnS1PxiZV3HDiPGsAW5Lrsp09MnlCmkQre9GzO8MnGytaaY1eZoqBN6SMJ4U578_uDtiuXswovr1T3o-Kt5KK0mlN_zC0RDodJFaKHQ3Uk-HIZ3vuMvAKNJi8DDFwWA7F6BOxz78Oh-UePwJTuc3PG0ZIZypPE1xlMPl5z46joaEw/s320/Clouds.png";
    break;
  case "Clear":
    weatherIcon.src =
      "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj7pmzNCftryAfpa1YBSzVeYtjgxDQnw09Ug0HVV47J8GEtHPYTH9hJgZ2M1k0YgE0pcZ1qekr4C14zyPCiVuQAfXLClK8Ww3hYB6v77yElP7Lo5BnUKo4n-w6yB17FAbw51WST6YKS0GMwyA4fYNxOZxEyNL6HhUfFRgVhOW0GyRdBRriMHFQ-qfh4cA/s320/sun.png";
    break;
  case "Rain":
    weatherIcon.src =
      "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgDW_NdwvxV796rkFf43qmUDiTQePn5dg7PDfn1SijfpjtB0AWJMBcifU6LWyW7iOtjZhfqIJnKEGQ1PwbbXS7NoKMSAmvy7i2ljWXMYLue3EBIBBR2qTFbs6QCe5eoFr2CU9WzCVJ8u0J3z3eAo3Ajv1LXamZASFtbj9sA_gD-Kp3hfgAk17Xh17RoLQ/s320/rainy.png";
    break;
  case "Mist":
    weatherIcon.src =
      "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVpL23l0t1U_ibWi01TFcHMF6J_t-9Ada5PavGlwG4M_mKIcx0pV1md9SN9ip1d84NaVowml5Do16XO3nsuttnM2-Ov05d-wCjEYjdzaOYfKvijw8k6Hfj9pOiPyEZTp2W20EPbTeONTgJE2Rdxs4KZUfg6f2PmbMF1094NcqJ7DwSFUQwYiRmVCNvuA/s320/mist.png";
    break;
  case "Snow":
    weatherIcon.src =
      "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-P3iT_uQK95qFY4h7QGdEtbRc1aVQo9BZy0ZWyPBvCNrP-4wnRStw0xYj9e4xa4ZlYISeNZqVJ33UP4YukR4jBennDD_obIN4QxYNZHdzG_z6_MNL2U08wMXwdFhtfvitW5LGiHgrwMJFC8QJFqbSO3woGSBqOdagGxaEQ20_S31Gc-GYL4vYzPzaPw/s320/snow.png";
    break;
  case "Haze":
    weatherIcon.src =
      "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjld66Ia5g_hpBn3Impi3zzOBHqWkjQInGLxTb2uXksuCsrkQU8HjlVyLobEJEGg8fRSIxeFzldGEHUmWcaiZBwAcRy4dGDpFX1BjTSB56qmBjW5tEW3RSC9_mCuLU_a8RuXchxGY7Oc8HLLl-IfaDW19Z0ZJJfNae9tECXRIyEu7rmJ3da08z8cI-phw/s320/haze.png";
    break;
}
```

- `weather-logo.png`: The logo image used on the main page.

![Weather Logo](src/main/webapp/images/weather-logo.png)

---

### 6.Contributing

---

Contributions are welcome! If you find any issues or have suggestions for improvement, please open an issue or submit a pull request.

---

### 7.License

---

This project is licensed under the [MIT License](LICENSE).
