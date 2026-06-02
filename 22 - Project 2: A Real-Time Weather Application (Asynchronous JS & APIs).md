## Project 2: A Real-Time Weather Application (Asynchronous JS & APIs)

A common task for modern frontend developers is fetching real-time data from an external server and reflecting that data inside a clean, responsive user interface.

In this project, we will build a **Real-Time Weather Application**. 

Unlike many weather tutorials that require you to sign up for a private API key (which can expire or charge a fee), we will use the open-source **Open-Meteo API**. This API is entirely free for non-commercial use, requires no registration, and features a companion **Geocoding API** to translate city names (like "Amsterdam" or "New York") into coordinate parameters (Latitude and Longitude).

### Skills Applied
*   Asynchronous JavaScript utilizing **`fetch`** and **`async/await`**.
*   Chaining multiple asynchronous dependent APIs (Geocoding API followed by Forecast API).
*   Dynamic data visualization and mapping data properties (WMO Weather Codes) to human-readable strings and graphics.
*   Graceful handling of asynchronous errors, slow network latency (loading states), and missing records.

---

### Project Files
We will build this application across three files:
1.  `index.html` (Application frame, cards, and containers)
2.  `style.css` (Visual styling, animations, and transitions)
3.  `app.js` (Asynchronous logic, geocoding steps, and DOM injection)

---

## Step 1: The HTML Structure (`index.html`)

Create a file named `index.html`. This document contains our search field, a toggle button, a loading indicator, and placeholders to display the weather results.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-Time Weather App</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="weather-app">
        <!-- Search Panel -->
        <form id="search-form">
            <input 
                type="text" 
                id="city-input" 
                placeholder="Enter city name... (e.g., Tokyo)" 
                required
                autocomplete="off"
            >
            <button type="submit" id="search-btn">Search</button>
        </form>

        <!-- Loading Spinner -->
        <div id="loading" class="hidden">
            <div class="spinner"></div>
            <p>Fetching local forecasts...</p>
        </div>

        <!-- Error Panel -->
        <div id="error-card" class="hidden">
            <p id="error-message">Unable to locate city. Please try again.</p>
        </div>

        <!-- Main Weather Display Card -->
        <main id="weather-card" class="hidden">
            <header class="location-header">
                <h2 id="city-name">City Name</h2>
                <p id="country-name">Country</p>
            </header>

            <div class="weather-body">
                <div class="weather-graphic" id="weather-emoji">☀️</div>
                <div class="temp-display">
                    <span id="temperature">--</span><span class="unit">°C</span>
                </div>
                <p id="weather-condition">Clear Skies</p>
            </div>

            <footer class="weather-details">
                <div class="detail-box">
                    <span class="label">Wind Speed</span>
                    <span class="value" id="wind-speed">-- km/h</span>
                </div>
                <div class="detail-box">
                    <span class="label">Time (Local)</span>
                    <span class="value" id="local-time">--:--</span>
                </div>
            </footer>
        </main>
    </div>

    <script src="app.js"></script>
</body>
</html>
```

---

## Step 2: Visual Styling (`style.css`)

Create a file named `style.css`. It establishes standard grid layouts, centering coordinates, custom variables, and a subtle keyframe animation for our loading spinner.

```css
/* Color Variables & Transitions */
:root {
    --bg-primary: #f0f4f8;
    --card-bg: #ffffff;
    --text-primary: #2d3748;
    --text-secondary: #718096;
    --accent: #3182ce;
    --border-color: #e2e8f0;
}

* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: system-ui, -apple-system, sans-serif;
}

body {
    background-color: var(--bg-primary);
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    padding: 20px;
}

/* App Container */
.weather-app {
    background-color: var(--card-bg);
    width: 100%;
    max-width: 420px;
    padding: 30px;
    border-radius: 16px;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.05);
}

/* Search Form */
#search-form {
    display: flex;
    gap: 10px;
    margin-bottom: 25px;
}

#city-input {
    flex-grow: 1;
    padding: 12px 16px;
    border: 2px solid var(--border-color);
    border-radius: 10px;
    font-size: 1rem;
    outline: none;
    transition: border-color 0.2s;
}

#city-input:focus {
    border-color: var(--accent);
}

#search-btn {
    background-color: var(--accent);
    color: white;
    border: none;
    padding: 12px 20px;
    border-radius: 10px;
    cursor: pointer;
    font-weight: 600;
    font-size: 1rem;
    transition: background-color 0.2s;
}

#search-btn:hover {
    background-color: #2b6cb0;
}

/* Loading Spinner */
#loading {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 15px;
    margin: 40px 0;
    color: var(--text-secondary);
}

.spinner {
    width: 40px;
    height: 40px;
    border: 4px solid var(--border-color);
    border-top: 4px solid var(--accent);
    border-radius: 50%;
    animation: spin 0.8s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

/* Error Card */
#error-card {
    background-color: #fff5f5;
    border: 1px solid #fed7d7;
    color: #c53030;
    padding: 15px;
    border-radius: 10px;
    text-align: center;
    margin: 20px 0;
    font-size: 0.95rem;
}

/* Weather Card Main Layout */
#weather-card {
    text-align: center;
    animation: fadeIn 0.4s ease-out;
}

@keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
}

.location-header h2 {
    color: var(--text-primary);
    font-size: 1.8rem;
    margin-bottom: 4px;
}

.location-header p {
    color: var(--text-secondary);
    font-size: 1rem;
    text-transform: uppercase;
    letter-spacing: 1px;
}

/* Weather Body */
.weather-body {
    margin: 30px 0;
}

.weather-graphic {
    font-size: 5rem;
    margin-bottom: 10px;
}

.temp-display {
    font-size: 4rem;
    font-weight: 700;
    color: var(--text-primary);
    line-height: 1;
}

.temp-display .unit {
    font-size: 2rem;
    font-weight: 400;
    color: var(--text-secondary);
    vertical-align: super;
}

#weather-condition {
    color: var(--text-secondary);
    font-size: 1.2rem;
    font-weight: 500;
    margin-top: 10px;
}

/* Weather Details Footer */
.weather-details {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 15px;
    padding-top: 25px;
    border-top: 2px solid var(--border-color);
    margin-top: 25px;
}

.detail-box {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.detail-box .label {
    font-size: 0.85rem;
    color: var(--text-secondary);
    text-transform: uppercase;
}

.detail-box .value {
    font-size: 1.1rem;
    font-weight: 600;
    color: var(--text-primary);
}

/* Display Toggles */
.hidden {
    display: none !important;
}
```

---

## Step 3: Application Logic (`app.js`)

Create a file named `app.js`. This script performs the asynchronous tasks. It acts as a controller: intercepting input, querying coordinates via a Geocoding service, sending those coordinates to a forecast generator, interpreting numeric meteorological codes, and rendering the results.

```javascript
// ==========================================
// 1. DICTIONARIES & HELPER CONFIGURATIONS
// ==========================================
// Maps WMO (World Meteorological Organization) codes to user-friendly conditions & emojis
const weatherCodeMap = {
    0: { condition: "Clear Sky", emoji: "☀️" },
    1: { condition: "Mainly Clear", emoji: "🌤️" },
    2: { condition: "Partly Cloudy", emoji: "⛅" },
    3: { condition: "Overcast", emoji: "☁️" },
    45: { condition: "Foggy", emoji: "🌫️" },
    48: { condition: "Depositing Rime Fog", emoji: "🌫️" },
    51: { condition: "Light Drizzle", emoji: "🌦️" },
    53: { condition: "Moderate Drizzle", emoji: "🌦️" },
    55: { condition: "Dense Drizzle", emoji: "🌧️" },
    61: { condition: "Slight Rain", emoji: "🌧️" },
    63: { condition: "Moderate Rain", emoji: "🌧️" },
    65: { condition: "Heavy Rain", emoji: "🌧️" },
    71: { condition: "Slight Snowfall", emoji: "❄️" },
    73: { condition: "Moderate Snowfall", emoji: "❄️" },
    75: { condition: "Heavy Snowfall", emoji: "❄️" },
    80: { condition: "Slight Rain Showers", emoji: "🌦️" },
    81: { condition: "Moderate Rain Showers", emoji: "🌧️" },
    82: { condition: "Violent Rain Showers", emoji: "⛈️" },
    95: { condition: "Thunderstorm", emoji: "⛈️" }
};

// ==========================================
// 2. DOM ELEMENT SELECTION
// ==========================================
const searchForm = document.getElementById("search-form");
const cityInput = document.getElementById("city-input");
const loadingIndicator = document.getElementById("loading");
const errorCard = document.getElementById("error-card");
const errorMessage = document.getElementById("error-message");
const weatherCard = document.getElementById("weather-card");

const cityNameDisplay = document.getElementById("city-name");
const countryNameDisplay = document.getElementById("country-name");
const weatherEmojiDisplay = document.getElementById("weather-emoji");
const temperatureDisplay = document.getElementById("temperature");
const conditionDisplay = document.getElementById("weather-condition");
const windSpeedDisplay = document.getElementById("wind-speed");
const localTimeDisplay = document.getElementById("local-time");

// ==========================================
// 3. ASYNCHRONOUS OPERATIONS
// ==========================================

/**
 * Step 1: Takes a plain text city name and searches Open-Meteo's Geocoding API.
 * Returns coordinates (latitude, longitude) and location naming variables.
 */
async function getCoordinates(city) {
    const cleanCityName = encodeURIComponent(city.trim());
    const geocodeUrl = `https://geocoding-api.open-meteo.com/v1/search?name=${cleanCityName}&count=1&language=en&format=json`;

    const response = await fetch(geocodeUrl);

    if (!response.ok) {
        throw new Error("Unable to establish connection with geocoding database.");
    }

    const data = await response.json();

    // If the results array is missing or empty, the search failed
    if (!data.results || data.results.length === 0) {
        throw new Error(`Could not find a city matching "${city}".`);
    }

    // Extract first search result
    const { latitude, longitude, name, country, admin1 } = data.results[0];
    
    return {
        lat: latitude,
        lon: longitude,
        name: name,
        country: country,
        region: admin1 || "" // State/Province fallback
    };
}

/**
 * Step 2: Queries Open-Meteo's Weather API using numeric coordinates.
 * Returns an object containing the current weather metrics.
 */
async function fetchWeatherData(lat, lon) {
    // We request the current weather metrics, setting the timezone to automatic
    const weatherUrl = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true&timezone=auto`;

    const response = await fetch(weatherUrl);

    if (!response.ok) {
        throw new Error("Unable to fetch weather forecast data.");
    }

    const data = await response.json();
    
    if (!data.current_weather) {
        throw new Error("Weather forecast payload is formatted incorrectly.");
    }

    return data.current_weather;
}

// ==========================================
// 4. UI COORDINATION & CONTROLLER LOGIC
// ==========================================

/**
 * Handles the orchestrating flow: loading toggles, sequentially querying APIs,
 * processing code transformations, and updating the UI cards.
 */
async function handleWeatherSearch(event) {
    event.preventDefault();
    const cityQuery = cityInput.value.trim();
    if (!cityQuery) return;

    // Reset visibility states
    errorCard.classList.add("hidden");
    weatherCard.classList.add("hidden");
    loadingIndicator.classList.remove("hidden");

    try {
        // Fetch coordinates
        const location = await getCoordinates(cityQuery);

        // Fetch forecast
        const weather = await fetchWeatherData(location.lat, location.lon);

        // Update presentation
        updateWeatherUI(location, weather);
    } catch (error) {
        // Display caught errors clearly
        errorMessage.textContent = error.message;
        errorCard.classList.remove("hidden");
    } finally {
        // Remove loading state when all operations are finished
        loadingIndicator.classList.add("hidden");
    }
}

/**
 * Updates UI DOM elements with processed weather data
 */
function updateWeatherUI(location, weather) {
    // 1. Set location header labels
    cityNameDisplay.textContent = location.name;
    countryNameDisplay.textContent = location.region 
        ? `${location.region}, ${location.country}` 
        : location.country;

    // 2. Map WMO Weather Code to localized description and emoji
    const codeData = weatherCodeMap[weather.weathercode] || { condition: "Unknown Conditions", emoji: "❓" };
    conditionDisplay.textContent = codeData.condition;
    weatherEmojiDisplay.textContent = codeData.emoji;

    // 3. Format measurement values
    temperatureDisplay.textContent = Math.round(weather.temperature);
    windSpeedDisplay.textContent = `${weather.windspeed} km/h`;

    // 4. Format the API time output (Convert ISO 8601 string "YYYY-MM-DDTHH:MM" to "HH:MM")
    if (weather.time && weather.time.includes("T")) {
        const timeSegment = weather.time.split("T")[1]; // ["YYYY-MM-DD", "HH:MM"]
        localTimeDisplay.textContent = timeSegment;
    } else {
        localTimeDisplay.textContent = "--:--";
    }

    // Reveal populated weather card layout
    weatherCard.classList.remove("hidden");
}

// ==========================================
// 5. EVENT BINDING & STARTUP
// ==========================================
searchForm.addEventListener("submit", handleWeatherSearch);
```

---

### Project Review

By completing this second capstone project, you have combined several concepts covered in our core curriculum:

1.  **Asynchronous Orchestration:** Modern applications often depend on data from multiple APIs. Here, we sequentially executed two separate network requests. We could not call `fetchWeatherData` until we had resolved the latitude and longitude from the geocoding data returned by `getCoordinates`.
2.  **Graceful API Error Handling:** Traditional network requests fail silently if there are issues. By checking `response.ok` on both functions and throwing custom errors (`throw new Error(...)`), we catch failures inside our `try...catch` block and display a user-friendly message.
3.  **Mapping Status Keys:** Raw data often arrives in compact, computer-friendly formats (like numerical code fields). We used a simple JavaScript look-up dictionary (`weatherCodeMap`) to convert those codes into readable strings and emojis.
4.  **Managing UI States:** In asynchronous applications, it is important to communicate with users during long-running tasks. We handled this by programmatically toggling the `.hidden` class on our loading spinner, weather cards, and error blocks.
