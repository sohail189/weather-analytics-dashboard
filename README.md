# <span style="color:blue;">Weather Analytics Dashboard</span>

## <span style="color:blue;">Project Overview</span>
The **Weather Analytics Dashboard** is an interactive Power BI report designed to provide real-time and forecasted weather insights.  
It integrates with a Weather API to display **current weather**, a **7-day forecast**, **Air Quality Index (AQI)**, and **city-wise comparisons**.  
This dashboard helps users monitor weather trends, plan ahead, and analyze environmental factors such as air quality and rainfall probability.

<img width="1072" height="628" alt="Screenshot (76)" src="https://github.com/user-attachments/assets/abfcf8b4-71ca-4231-9f12-7bdf7195478b" />

---

## <span style="color:blue;">File Information</span>
- **File Name:** `weather_dashboard.pbix`  
- **Tool Used:** Microsoft Power BI  
- **Data Transformation:** Power Query (ETL process)  
- **Visualization:** Power BI Interactive Reports  

---

## <span style="color:blue;">Objectives</span>
- Display live weather conditions for selected cities.  
- Provide a detailed 7-day weather forecast with temperature and rain chances.  
- Monitor Air Quality Index (AQI) and pollutant levels.  
- Compare weather conditions across multiple cities.  

---

## <span style="color:blue;">Key Features</span>
- **Current Weather:** Temperature, humidity, wind speed, UV index, pressure, precipitation.  
- **7-Day Forecast:** Line chart showing temperature variation across days.  
- **Air Quality Analysis:** AQI with breakdown of CO, NO₂, SO₂, O₃, PM2.5, and PM10.  
- **Rain Probability:** Daily rain chances visualized using bar charts.  
- **City Comparison:** Quick view of multiple cities (Bangalore, Chennai, Hyderabad, Kochi, Mumbai, Vizag).  
- **Day/Night Cycle:** Sunrise and sunset times.  

---

## <span style="color:blue;">ETL Process</span>
The dashboard was built using the **Extract, Transform, Load (ETL)** process:  

1. **Extract:** Weather data pulled via Weather API (7-day forecast).  
2. **Transform:** Data cleaned and processed in Power Query (handling nulls, formatting, creating calculated columns).  
3. **Load:** Data model created with proper relationships between tables.  

---

## <span style="color:blue;">Data Model & Schema</span>
- **Locations:** Stores city/location details.  
- **Current:** Current weather and AQI data.  
- **Forecast_Day:** Daily weather forecast.  
- **Forecast_Hour:** Hourly weather forecast.  
- **Master Table & Weather_Reports (1–6):** Supporting tables for staging and transformations (hidden/not used).  

### <span style="color:blue;">Relationships</span>
- Locations ↔ Current (1-to-1)  
- Locations ↔ Forecast_Day (1-to-many)  
- Locations ↔ Forecast_Hour (1-to-many)  

---

## <span style="color:blue;">DAX Measures</span>
Examples of DAX measures used in the dashboard:  

```DAX
Curr_Temp_C = SUM('Current'[current.temp_c]) & " °C"
Curr_Temp_F = SUM('Current'[current.temp_f]) & " °F"
last_update = "Last Updated, " & FORMAT(FIRSTNONBLANK('Current'[current.last_updated], ""), "dd mmm")
For_Temp_C = AVERAGE(Forcast_Day[forecast.forecastday.day.avgtemp_c]) & " °C"

AQI_Status_Text = 
VAR AQI = ROUND(SELECTEDVALUE('Current'[current.air_quality.pm10]),0)
RETURN SWITCH(
    TRUE(),
    AQI <= 50, "Good",
    AQI <= 100, "Moderate",
    AQI <= 150, "Unhealthy for Sensitive",
    AQI <= 200, "Unhealthy",
    AQI <= 300, "Very Unhealthy",
    "Hazardous"
)

AQI_Color_PM10 = 
VAR AQI = ROUND(SELECTEDVALUE('Current'[current.air_quality.pm10]),0)
RETURN SWITCH(
    TRUE(),
    AQI <= 50, "#43d946",
    AQI <= 100, "#fff570",
    AQI <= 150, "#ff9800",
    AQI <= 200, "#d99343",
    AQI <= 300, "#ff5b0f",
    "#d95243"
)

AQI_Suggestion = 
VAR AQI = SELECTEDVALUE('Current'[current.air_quality.pm10])
RETURN SWITCH(
    TRUE(),
    AQI <= 50, "Air is clean and healthy",
    AQI <= 100, "Acceptable air quality, stay active",
    AQI <= 150, "Sensitive groups should reduce outdoor time",
    AQI <= 200, "Limit prolonged outdoor exertion",
    AQI <= 300, "Avoid outdoor activity if possible",
    "Stay indoors, wear mask if outside"
)

