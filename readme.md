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

### 5.File Structure

---

- `MyServlet.java`: The main servlet class that handles the HTTP requests and integrates with the OpenWeatherMap API.
- `index.jsp`: The JSP file for the main page, containing the HTML, CSS, and JavaScript code.
- `weather.jsp`: The JSP file that displays the retrieved weather information.
- `style.css`: The CSS file containing the styles for the application.
- `myScript.js`: The JavaScript file containing the code for dynamically updating the weather icon based on the current weather conditions.
- `images/weather-logo.png`: The logo image used on the main page.

---

### 6.Contributing

---

Contributions are welcome! If you find any issues or have suggestions for improvement, please open an issue or submit a pull request.

---

### 7.License

---

This project is licensed under the [MIT License](LICENSE).
