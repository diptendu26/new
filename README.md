# weather forecast
import requests
import geocoder
import tkinter as tk
from tkinter import messagebox

# ---------------- CONFIG ----------------
API_KEY = "YOUR_OPENWEATHERMAP_API_KEY"
BASE_URL = "https://api.weatherapi.com/v1/forecast.json"
# ----------------------------------------


def get_location():
    g = geocoder.ip('me')
    if g.ok:
        return g.city
    return None


def get_weather(city):
    params = {
        "q": city,
        "appid": API_KEY,
        "units": "metric"
    }
    response = requests.get(BASE_URL, params=params)
    response.raise_for_status()
    return response.json()


def display_weather():
    try:
        city = get_location()
        if not city:
            raise Exception("Unable to detect location")

        data = get_weather(city)

        temp = data["main"]["temp"]
        feels_like = data["main"]["feels_like"]
        humidity = data["main"]["humidity"]
        description = data["weather"][0]["description"].title()
        wind = data["wind"]["speed"]

        result_label.config(
            text=f"""
City: {city}
Temperature: {temp}°C
Feels Like: {feels_like}°C
Condition: {description}
Humidity: {humidity}%
Wind Speed: {wind} m/s
"""
        )

    except Exception as e:
        messagebox.showerror("Error", str(e))


# ---------------- GUI ----------------
root = tk.Tk()
root.title("Weather Forecast App")
root.geometry("350x300")
root.resizable(False, False)

title = tk.Label(root, text="Real-Time Weather", font=("Arial", 16, "bold"))
title.pack(pady=10)

fetch_button = tk.Button(
    root,
    text="Get My Weather",
    font=("Arial", 12),
    command=display_weather
)
fetch_button.pack(pady=10)

result_label = tk.Label(root, text="", font=("Arial", 11), justify="left")
result_label.pack(pady=10)

root.mainloop()
