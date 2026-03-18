# Predictive Weather App

A Django-based weather forecasting web application that combines live weather data from the OpenWeatherMap API with machine learning models to predict rain probability and forecast temperature and humidity over the next 5 hours.

---

## Features

- **Live Weather Data** — Fetches real-time conditions (temperature, humidity, wind, pressure, visibility, cloud cover) for any city worldwide
- **Rain Prediction** — Uses a Random Forest Classifier trained on historical weather data to predict whether it will rain
- **5-Hour Forecast** — Predicts future temperature and humidity using Random Forest Regression models
- **Dynamic UI** — Background and imagery change based on current weather conditions (sunny, cloudy, rain, snow, fog, etc.)
- **Interactive Chart** — Renders a live temperature forecast chart using Chart.js
- **Condition Details** — Displays wind speed, pressure, visibility, min/max temperature, and feels-like temperature

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python, Django 5.2 |
| ML Models | scikit-learn (RandomForestClassifier, RandomForestRegressor) |
| Data Processing | pandas, NumPy |
| Weather API | OpenWeatherMap API |
| Frontend | Django Templates, Bootstrap Icons, Chart.js |
| Styling | Custom CSS |
| Database | SQLite (default Django) |

---

## Project Structure

```
predictive-weather-app/
├── backend.py                   # Standalone CLI version of the app
└── Weather_App/
    ├── manage.py
    ├── weather.csv              # Historical weather dataset for ML training
    ├── Weather_App/
    │   ├── settings.py
    │   ├── urls.py
    │   ├── wsgi.py
    │   └── asgi.py
    └── forecast/
        ├── views.py             # Core ML logic + Django view
        ├── urls.py
        ├── models.py
        ├── templates/
        │   └── weather.html     # Main UI template
        └── static/
            ├── css/style.css
            ├── js/chartSetup.js
            └── img/             # Weather condition images
```

---

## How It Works

1. The user submits a city name via the search form.
2. The app fetches current weather conditions from the OpenWeatherMap API.
3. Historical weather data (`weather.csv`) is loaded and used to train three ML models on each request:
   - A **RandomForestClassifier** to predict rain probability (`RainTomorrow`)
   - A **RandomForestRegressor** for future temperature
   - A **RandomForestRegressor** for future humidity
4. Wind direction (in degrees) is converted to a compass point and label-encoded to match training data.
5. Predictions for the next 5 hours are passed to the Django template and rendered alongside current conditions and a Chart.js line graph.

---

## Setup & Installation

### Prerequisites

- Python 3.10+
- pip

### Steps

1. **Clone the repository**

   ```bash
   git clone https://github.com/your-username/predictive-weather-app.git
   cd predictive-weather-app
   ```

2. **Create and activate a virtual environment**

   ```bash
   python -m venv venv
   source venv/bin/activate      # macOS/Linux
   venv\Scripts\activate         # Windows
   ```

3. **Install dependencies**

   ```bash
   pip install django requests pandas numpy scikit-learn pytz
   ```

4. **Configure the API key**

   Open `Weather_App/forecast/views.py` and replace the `API_KEY` value with your own key from [OpenWeatherMap](https://openweathermap.org/api):

   ```python
   API_KEY = 'your_api_key_here'
   ```

5. **Fix the CSV path in `views.py`**

   Update the hardcoded path in `weather_view` to use a relative or environment-based path:

   ```python
   csv_path = os.path.join(BASE_DIR, 'weather.csv')
   ```

   > **Note:** The current path is hardcoded to a local machine path and must be updated before the app will work on any other system.

6. **Apply migrations and run the server**

   ```bash
   cd Weather_App
   python manage.py migrate
   python manage.py runserver
   ```

7. **Open the app**

   Visit [http://127.0.0.1:8000](http://127.0.0.1:8000) in your browser.

---

## CLI Version

A standalone script (`backend.py`) is included for running predictions directly in the terminal without Django:

```bash
cd predictive-weather-app
python backend.py
```

You will be prompted to enter a city name, and results will be printed to the console.

> **Note:** Update the CSV path in `backend.py` to point to `Weather_App/weather.csv` before running.

---

## Known Issues & Improvements

- **Hardcoded CSV path** in `views.py` must be updated for the app to run on any machine other than the original developer's.
- **API key is hardcoded** — consider moving it to an environment variable (`.env` file with `python-dotenv`).
- **Models are retrained on every request**, which adds latency. Caching or persisting trained models (e.g., with `joblib`) would significantly improve performance.
- **No error handling** for invalid city names or failed API responses.
- **`DEBUG = True`** and the `SECRET_KEY` are exposed — these must be changed before any production deployment.

---

## Dataset

The app uses `weather.csv`, a historical weather dataset with the following key columns used for training:

| Column | Description |
|---|---|
| `MinTemp` | Minimum temperature (°C) |
| `MaxTemp` | Maximum temperature (°C) |
| `WindGustDir` | Wind gust direction (compass) |
| `WindGustSpeed` | Wind gust speed (km/h) |
| `Humidity` | Relative humidity (%) |
| `Pressure` | Atmospheric pressure (hPa) |
| `Temp` | Current temperature (°C) |
| `RainTomorrow` | Target: will it rain tomorrow? (Yes/No) |

---

## Authors

- Mohammad Abdullah Khurram