# Weatherapihw

#Import modules
import pandas as pd
import requests
import json
import csv
import numpy
import random
import seaborn as sb
import matplotlib.pyplot as plt
from config import wkey
from pprint import pprint

#import csv
cities = "citiesweather.csv"

#Randomly select 500 cities
#number of records in file (excludes header)
n = sum(1 for line in open(cities, encoding="utf8")) - 1 
s = 500 #desired sample size
skip = sorted(random.sample(range(1,n+1),n-s)) #the 0-indexed header will not be included in the skip list
cities_df = pd.read_csv(cities, skiprows=skip)
cities_df.head()

cities_df.columns

cities_reduced_df = cities_df[['city','lat', 'lng', 'country']]
#cities_reduced_df.head()
cities_df = cities_reduced_df
cities_df.head()

#Add empty columns to df 
cities_df["Temperature (F)"] = ""
cities_df["Cloudiness %"] = ""
cities_df["Wind Speed (mph)"] = ""
cities_df["Humidity %"] = ""
cities_df.head()

len(cities_df)

# Save config information.
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

# Build partial query URL
query_url = f"{url}appid={wkey}&units={units}&q="
print(query_url)

cityList = cities_df['city'].tolist()
print(cityList)

# Save config information.
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

#test request.get
response = requests.get(query_url + "London")
print(response.url)

response = requests.get(query_url + "London").json()

pprint (response)

for index, row in cities_df.iterrows():
    
    city = row['city']
    
    response = requests.get(query_url + str(city))
    print(response.url)
    
 for index, row in cities_df.iterrows():
    
    city = row['city']
    
    response = requests.get(query_url + str(city)).json()
    
    try:
        cities_df.set_value(index, "Temperature (F)",
                            response['main']["temp"])
        cities_df.set_value(index, "Cloudiness %",
                            response["clouds"]["all"])
        cities_df.set_value(index, "Wind Speed (mph)",
                            response["wind"]["speed"])
        cities_df.set_value(index, "Humidity %",
                            response["main"]["humidity"])
        
    except:
        print("Missing field/result... skipping.")
        
print("check complete")

cities_df.head()

cities_df.to_csv("csvweatherpy.csv", header = True)

cities_df_filter = cities_df[cities_df["Temperature (F)"] != ""]
cities_df_filter

cities_df_filter.to_csv("csvweatherpy_filter.csv", header = True)

cities_df_filter.dtypes

#Temp Plot
sb.set()

x = cities_df_filter["lat"]
y = cities_df_filter["Temperature (F)"]

plt.scatter(x, y, marker="o", facecolors="orange", edgecolors="black")
plt.title("Temperature vs Latitude")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
plt.show()

#Cloud Plot
sb.set()

x = cities_df_filter["lat"]
y = cities_df_filter["Cloudiness %"]

plt.scatter(x, y, marker="o", facecolors="gray", edgecolors="black")
plt.title("Cloudiness % vs Latitude")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness %")

plt.show()

#Wind Plot
x = cities_df_filter["lat"]
y = cities_df_filter["Wind Speed (mph)"]

plt.scatter(x, y, marker="*", facecolors="blue", edgecolors="black")
plt.title("Wind Speed (mph) vs Latitude")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")

plt.show()

#Humid Plot
x = cities_df_filter["lat"]
y = cities_df_filter["Humidity %"]

plt.scatter(x, y, marker="o", facecolors="purple", edgecolors="black")
plt.title("Humidity % vs Latitude")
plt.xlabel("Latitude")
plt.ylabel("Humidity %")

plt.show()
