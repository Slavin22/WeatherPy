
# WeatherPy

### Analysis

- My biggest takeaway from the weather data is from the City Latitude vs. Max Temperature plot. Conventional wisdom would suggest that cities along the equator would be the hottest. However, the plot suggests that max temperature actually peaks somewhere in between 10 and 15 degrees north of the equator.


- I would also expect cities near the poles to be the coldest, and this is true. In the plot, it may appear that cities near the north pole are actually significantly colder than those near the south pole. I don't believe this is true. Instead, there is a higher volume of northern cities which skews the appearance of the plot.


- The area highlighted in my first point above also represents another interesting trend. Cities with low humidity are somewhat prevalent outside the 10-15 degrees-north-of-the-equator range (on both sides), but not within that range. This tells me that max temperature and humidity are likely correlated, though asserting that definitively would require more analysis.


```python
# Import dependencies
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns
import random
from citipy import citipy
import requests
import time

# Google API Key + Geocoding/Places URLs
weather_url = "http://api.openweathermap.org/data/2.5/weather?"
weather_key = "appid=0d99449d7128437ead7d88097c7ac304&q="
```

### Generate Cities List


```python
# Get List filled with random unique city names
cities = []
lat =[]
lng = []
countries = []

# Need a way to drop the lat/lng of duplicate cities

for x in range(0, 1500):
    latitude = random.uniform(-90,90)
    longitude = random.uniform(-180,180)
    if (citipy.nearest_city(latitude, longitude).city_name in cities) == False:
        cities.append(citipy.nearest_city(latitude, longitude).city_name)
        countries.append(citipy.nearest_city(latitude, longitude).country_code)
        lat.append(latitude)
        lng.append(longitude)
        
len(cities)
```




    605




```python
# Create DataFrame from city name List with empty weather data columns
city_data = pd.DataFrame(cities)
city_data.columns = ["City"]
city_data["Country"] = countries
city_data["Cloudiness"] = ""
city_data["Date"] = ""
city_data["Humidity"] = ""
city_data["Lat"] = lat
city_data["Lng"] = lng
city_data["Max Temp"] = ""
city_data["Wind Speed"] = ""

city_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Country</th>
      <th>Cloudiness</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>aksarka</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td>66.464078</td>
      <td>69.203987</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>geraldton</td>
      <td>au</td>
      <td></td>
      <td></td>
      <td></td>
      <td>-35.239901</td>
      <td>88.059787</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>santa vitoria do palmar</td>
      <td>br</td>
      <td></td>
      <td></td>
      <td></td>
      <td>-33.474084</td>
      <td>-52.173456</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>new norfolk</td>
      <td>au</td>
      <td></td>
      <td></td>
      <td></td>
      <td>-75.596752</td>
      <td>124.140291</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>san patricio</td>
      <td>mx</td>
      <td></td>
      <td></td>
      <td></td>
      <td>0.593424</td>
      <td>-114.506393</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
# Fill Weather Data for each city
counter = 0

print("Beginning Data Retrieval")
print("------------------------")

for index, row in city_data.iterrows():
    #try:
        city_url = weather_url + weather_key + row["City"]
        print("Processing Record " + str(counter+1) + " of " + str(len(city_data)) + " | " + row["City"])
        print(city_url)
        city_response = requests.get(city_url)
        city_json = city_response.json()
        city_data.set_value(counter, "Cloudiness", city_json["clouds"]["all"])
        city_data.set_value(counter, "Date", city_json["dt"])
        city_data.set_value(counter, "Humidity", city_json["main"]["humidity"])
        city_data.set_value(counter, "Max Temp", (city_json["main"]["temp_max"] * 9/5 - 459.67))
        city_data.set_value(counter, "Wind Speed", city_json["wind"]["speed"])
        counter = counter + 1
        time.sleep(1)
    #except:
        #print("Error: skip city and continue")
        #counter = counter + 1
    
city_data.head()
```

    Beginning Data Retrieval
    ------------------------
    Processing Record 1 of 605 | aksarka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aksarka
    Processing Record 2 of 605 | geraldton
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=geraldton
    Processing Record 3 of 605 | santa vitoria do palmar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa vitoria do palmar
    Processing Record 4 of 605 | new norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=new norfolk
    Processing Record 5 of 605 | san patricio
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san patricio
    Processing Record 6 of 605 | punta arenas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=punta arenas
    Processing Record 7 of 605 | atuona
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=atuona
    Processing Record 8 of 605 | klaksvik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=klaksvik
    Processing Record 9 of 605 | tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tuktoyaktuk
    Processing Record 10 of 605 | vaini
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vaini
    Processing Record 11 of 605 | namibe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=namibe
    Processing Record 12 of 605 | ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ushuaia
    Processing Record 13 of 605 | carnarvon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=carnarvon
    Processing Record 14 of 605 | saldanha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saldanha
    Processing Record 15 of 605 | narsaq
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=narsaq
    Processing Record 16 of 605 | taolanaro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taolanaro
    Processing Record 17 of 605 | richard toll
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=richard toll
    Processing Record 18 of 605 | bluff
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bluff
    Processing Record 19 of 605 | manati
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=manati
    Processing Record 20 of 605 | ust-maya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ust-maya
    Processing Record 21 of 605 | naze
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=naze
    Processing Record 22 of 605 | khalkhal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=khalkhal
    Processing Record 23 of 605 | belushya guba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=belushya guba
    Processing Record 24 of 605 | saskylakh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saskylakh
    Processing Record 25 of 605 | victoria
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=victoria
    Processing Record 26 of 605 | hasaki
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hasaki
    Processing Record 27 of 605 | luderitz
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=luderitz
    Processing Record 28 of 605 | yumen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yumen
    Processing Record 29 of 605 | talcahuano
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=talcahuano
    Processing Record 30 of 605 | albany
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=albany
    Processing Record 31 of 605 | mataura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mataura
    Processing Record 32 of 605 | bethel
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bethel
    Processing Record 33 of 605 | hobart
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hobart
    Processing Record 34 of 605 | bilma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bilma
    Processing Record 35 of 605 | roald
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=roald
    Processing Record 36 of 605 | mahalapye
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mahalapye
    Processing Record 37 of 605 | tevaitoa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tevaitoa
    Processing Record 38 of 605 | hofn
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hofn
    Processing Record 39 of 605 | butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=butaritari
    Processing Record 40 of 605 | severo-kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=severo-kurilsk
    Processing Record 41 of 605 | castanos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=castanos
    Processing Record 42 of 605 | santiago del estero
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santiago del estero
    Processing Record 43 of 605 | jaisalmer
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jaisalmer
    Processing Record 44 of 605 | lavrentiya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lavrentiya
    Processing Record 45 of 605 | sivas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sivas
    Processing Record 46 of 605 | saint anthony
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint anthony
    Processing Record 47 of 605 | souillac
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=souillac
    Processing Record 48 of 605 | hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hermanus
    Processing Record 49 of 605 | pacific grove
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pacific grove
    Processing Record 50 of 605 | pangnirtung
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pangnirtung
    Processing Record 51 of 605 | barrow
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=barrow
    Processing Record 52 of 605 | havoysund
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=havoysund
    Processing Record 53 of 605 | watertown
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=watertown
    Processing Record 54 of 605 | busselton
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=busselton
    Processing Record 55 of 605 | bambanglipuro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bambanglipuro
    Processing Record 56 of 605 | homer
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=homer
    Processing Record 57 of 605 | swabi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=swabi
    Processing Record 58 of 605 | port hawkesbury
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port hawkesbury
    Processing Record 59 of 605 | susehri
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=susehri
    Processing Record 60 of 605 | basco
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=basco
    Processing Record 61 of 605 | kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kapaa
    Processing Record 62 of 605 | shimoda
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shimoda
    Processing Record 63 of 605 | vidim
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vidim
    Processing Record 64 of 605 | tura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tura
    Processing Record 65 of 605 | piopio
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=piopio
    Processing Record 66 of 605 | qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=qaanaaq
    Processing Record 67 of 605 | wiltz
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=wiltz
    Processing Record 68 of 605 | tabiauea
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tabiauea
    Processing Record 69 of 605 | airai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=airai
    Processing Record 70 of 605 | natal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=natal
    Processing Record 71 of 605 | east brainerd
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=east brainerd
    Processing Record 72 of 605 | sibolga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sibolga
    Processing Record 73 of 605 | tomatlan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tomatlan
    Processing Record 74 of 605 | santo domingo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santo domingo
    Processing Record 75 of 605 | hithadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hithadhoo
    Processing Record 76 of 605 | sola
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sola
    Processing Record 77 of 605 | ilulissat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ilulissat
    Processing Record 78 of 605 | lompoc
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lompoc
    Processing Record 79 of 605 | aklavik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aklavik
    Processing Record 80 of 605 | castro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=castro
    Processing Record 81 of 605 | esperance
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=esperance
    Processing Record 82 of 605 | dikson
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dikson
    Processing Record 83 of 605 | rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rikitea
    Processing Record 84 of 605 | yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yellowknife
    Processing Record 85 of 605 | thompson
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=thompson
    Processing Record 86 of 605 | coulihaut
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=coulihaut
    Processing Record 87 of 605 | mawlaik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mawlaik
    Processing Record 88 of 605 | kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kodiak
    Processing Record 89 of 605 | pevek
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pevek
    Processing Record 90 of 605 | lolodorf
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lolodorf
    Processing Record 91 of 605 | buin
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=buin
    Processing Record 92 of 605 | leona vicario
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=leona vicario
    Processing Record 93 of 605 | upernavik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=upernavik
    Processing Record 94 of 605 | komsomolskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=komsomolskiy
    Processing Record 95 of 605 | cockburn town
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cockburn town
    Processing Record 96 of 605 | hilo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hilo
    Processing Record 97 of 605 | vardo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vardo
    Processing Record 98 of 605 | kaiyuan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kaiyuan
    Processing Record 99 of 605 | marcona
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=marcona
    Processing Record 100 of 605 | taungdwingyi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taungdwingyi
    Processing Record 101 of 605 | rusape
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rusape
    Processing Record 102 of 605 | bredasdorp
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bredasdorp
    Processing Record 103 of 605 | isangel
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=isangel
    Processing Record 104 of 605 | avarua
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=avarua
    Processing Record 105 of 605 | sento se
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sento se
    Processing Record 106 of 605 | kavieng
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kavieng
    Processing Record 107 of 605 | dunedin
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dunedin
    Processing Record 108 of 605 | hambantota
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hambantota
    Processing Record 109 of 605 | mar del plata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mar del plata
    Processing Record 110 of 605 | santa isabel do rio negro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa isabel do rio negro
    Processing Record 111 of 605 | buala
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=buala
    Processing Record 112 of 605 | horsham
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=horsham
    Processing Record 113 of 605 | kovalevskoye
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kovalevskoye
    Processing Record 114 of 605 | nanortalik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nanortalik
    Processing Record 115 of 605 | jahrom
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jahrom
    Processing Record 116 of 605 | mount gambier
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mount gambier
    Processing Record 117 of 605 | agogo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=agogo
    Processing Record 118 of 605 | alyangula
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alyangula
    Processing Record 119 of 605 | bargal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bargal
    Processing Record 120 of 605 | sechura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sechura
    Processing Record 121 of 605 | daxian
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=daxian
    Processing Record 122 of 605 | puerto ayora
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=puerto ayora
    Processing Record 123 of 605 | margate
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=margate
    Processing Record 124 of 605 | camacha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=camacha
    Processing Record 125 of 605 | jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jamestown
    Processing Record 126 of 605 | fontanka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=fontanka
    Processing Record 127 of 605 | hay river
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hay river
    Processing Record 128 of 605 | bulembu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bulembu
    Processing Record 129 of 605 | leningradskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=leningradskiy
    Processing Record 130 of 605 | cherskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cherskiy
    Processing Record 131 of 605 | kahului
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kahului
    Processing Record 132 of 605 | illoqqortoormiut
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=illoqqortoormiut
    Processing Record 133 of 605 | mahebourg
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mahebourg
    Processing Record 134 of 605 | marawi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=marawi
    Processing Record 135 of 605 | jalu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jalu
    Processing Record 136 of 605 | ha giang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ha giang
    Processing Record 137 of 605 | lapua
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lapua
    Processing Record 138 of 605 | surt
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=surt
    Processing Record 139 of 605 | cape town
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cape town
    Processing Record 140 of 605 | bandarbeyla
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bandarbeyla
    Processing Record 141 of 605 | lai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lai
    Processing Record 142 of 605 | babanusah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=babanusah
    Processing Record 143 of 605 | korla
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=korla
    Processing Record 144 of 605 | ahipara
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ahipara
    Processing Record 145 of 605 | kysyl-syr
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kysyl-syr
    Processing Record 146 of 605 | ancud
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ancud
    Processing Record 147 of 605 | flinders
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=flinders
    Processing Record 148 of 605 | trelew
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=trelew
    Processing Record 149 of 605 | east london
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=east london
    Processing Record 150 of 605 | filadelfia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=filadelfia
    Processing Record 151 of 605 | provideniya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=provideniya
    Processing Record 152 of 605 | padang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=padang
    Processing Record 153 of 605 | bengkulu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bengkulu
    Processing Record 154 of 605 | chumphon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chumphon
    Processing Record 155 of 605 | billings
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=billings
    Processing Record 156 of 605 | harrismith
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=harrismith
    Processing Record 157 of 605 | santa marta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa marta
    Processing Record 158 of 605 | chokurdakh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chokurdakh
    Processing Record 159 of 605 | zimovniki
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zimovniki
    Processing Record 160 of 605 | saint-philippe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint-philippe
    Processing Record 161 of 605 | mehamn
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mehamn
    Processing Record 162 of 605 | carahue
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=carahue
    Processing Record 163 of 605 | kalmunai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kalmunai
    Processing Record 164 of 605 | tiksi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tiksi
    Processing Record 165 of 605 | bilibino
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bilibino
    Processing Record 166 of 605 | rio gallegos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rio gallegos
    Processing Record 167 of 605 | beringovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=beringovskiy
    Processing Record 168 of 605 | srikakulam
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=srikakulam
    Processing Record 169 of 605 | fairbanks
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=fairbanks
    Processing Record 170 of 605 | bintulu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bintulu
    Processing Record 171 of 605 | mount isa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mount isa
    Processing Record 172 of 605 | san quintin
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san quintin
    Processing Record 173 of 605 | sao filipe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sao filipe
    Processing Record 174 of 605 | tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tasiilaq
    Processing Record 175 of 605 | labuhan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=labuhan
    Processing Record 176 of 605 | vendas novas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vendas novas
    Processing Record 177 of 605 | kantunilkin
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kantunilkin
    Processing Record 178 of 605 | temaraia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=temaraia
    Processing Record 179 of 605 | rodrigues alves
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rodrigues alves
    Processing Record 180 of 605 | nizhneyansk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nizhneyansk
    Processing Record 181 of 605 | arraial do cabo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=arraial do cabo
    Processing Record 182 of 605 | port elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port elizabeth
    Processing Record 183 of 605 | lorengau
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lorengau
    Processing Record 184 of 605 | tuatapere
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tuatapere
    Processing Record 185 of 605 | belmonte
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=belmonte
    Processing Record 186 of 605 | azle
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=azle
    Processing Record 187 of 605 | zyryanka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zyryanka
    Processing Record 188 of 605 | haapiti
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=haapiti
    Processing Record 189 of 605 | tutoia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tutoia
    Processing Record 190 of 605 | alappuzha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alappuzha
    Processing Record 191 of 605 | port macquarie
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port macquarie
    Processing Record 192 of 605 | yulara
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yulara
    Processing Record 193 of 605 | shache
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shache
    Processing Record 194 of 605 | sume
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sume
    Processing Record 195 of 605 | iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=iqaluit
    Processing Record 196 of 605 | tumannyy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tumannyy
    Processing Record 197 of 605 | zhigansk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zhigansk
    Processing Record 198 of 605 | port alfred
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port alfred
    Processing Record 199 of 605 | tokur
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tokur
    Processing Record 200 of 605 | katangi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=katangi
    Processing Record 201 of 605 | falealupo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=falealupo
    Processing Record 202 of 605 | healdsburg
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=healdsburg
    Processing Record 203 of 605 | attawapiskat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=attawapiskat
    Processing Record 204 of 605 | fortuna
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=fortuna
    Processing Record 205 of 605 | sitka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sitka
    Processing Record 206 of 605 | nebaj
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nebaj
    Processing Record 207 of 605 | chernushka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chernushka
    Processing Record 208 of 605 | lasa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lasa
    Processing Record 209 of 605 | derazhnya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=derazhnya
    Processing Record 210 of 605 | vila franca do campo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vila franca do campo
    Processing Record 211 of 605 | lebu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lebu
    Processing Record 212 of 605 | touros
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=touros
    Processing Record 213 of 605 | yilan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yilan
    Processing Record 214 of 605 | tuy hoa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tuy hoa
    Processing Record 215 of 605 | san juan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san juan
    Processing Record 216 of 605 | plouzane
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=plouzane
    Processing Record 217 of 605 | cravo norte
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cravo norte
    Processing Record 218 of 605 | cortez
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cortez
    Processing Record 219 of 605 | gat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gat
    Processing Record 220 of 605 | acajutla
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=acajutla
    Processing Record 221 of 605 | torbay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=torbay
    Processing Record 222 of 605 | sile
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sile
    Processing Record 223 of 605 | carlagan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=carlagan
    Processing Record 224 of 605 | tilichiki
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tilichiki
    Processing Record 225 of 605 | georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=georgetown
    Processing Record 226 of 605 | nome
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nome
    Processing Record 227 of 605 | khorinsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=khorinsk
    Processing Record 228 of 605 | muros
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=muros
    Processing Record 229 of 605 | ponta delgada
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ponta delgada
    Processing Record 230 of 605 | akureyri
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=akureyri
    Processing Record 231 of 605 | khuzhir
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=khuzhir
    Processing Record 232 of 605 | saint george
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint george
    Processing Record 233 of 605 | bose
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bose
    Processing Record 234 of 605 | sinnamary
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sinnamary
    Processing Record 235 of 605 | minas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=minas
    Processing Record 236 of 605 | tateyama
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tateyama
    Processing Record 237 of 605 | varnamo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=varnamo
    Processing Record 238 of 605 | clyde river
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=clyde river
    Processing Record 239 of 605 | gizo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gizo
    Processing Record 240 of 605 | rogun
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rogun
    Processing Record 241 of 605 | samusu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=samusu
    Processing Record 242 of 605 | southbridge
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=southbridge
    Processing Record 243 of 605 | mareeba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mareeba
    Processing Record 244 of 605 | chitral
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chitral
    Processing Record 245 of 605 | nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nikolskoye
    Processing Record 246 of 605 | yar-sale
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yar-sale
    Processing Record 247 of 605 | los llanos de aridane
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=los llanos de aridane
    Processing Record 248 of 605 | awbari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=awbari
    Processing Record 249 of 605 | pahrump
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pahrump
    Processing Record 250 of 605 | rassvet
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rassvet
    Processing Record 251 of 605 | el terrero
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=el terrero
    Processing Record 252 of 605 | luzhou
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=luzhou
    Processing Record 253 of 605 | rungata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rungata
    Processing Record 254 of 605 | mazamari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mazamari
    Processing Record 255 of 605 | luena
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=luena
    Processing Record 256 of 605 | hami
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hami
    Processing Record 257 of 605 | taoudenni
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taoudenni
    Processing Record 258 of 605 | riverton
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=riverton
    Processing Record 259 of 605 | cidreira
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cidreira
    Processing Record 260 of 605 | port hedland
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port hedland
    Processing Record 261 of 605 | igrim
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=igrim
    Processing Record 262 of 605 | kaeo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kaeo
    Processing Record 263 of 605 | edeia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=edeia
    Processing Record 264 of 605 | manokwari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=manokwari
    Processing Record 265 of 605 | chuy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chuy
    Processing Record 266 of 605 | bontang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bontang
    Processing Record 267 of 605 | rocha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rocha
    Processing Record 268 of 605 | riyadh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=riyadh
    Processing Record 269 of 605 | newnan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=newnan
    Processing Record 270 of 605 | doha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=doha
    Processing Record 271 of 605 | namatanai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=namatanai
    Processing Record 272 of 605 | katobu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=katobu
    Processing Record 273 of 605 | khatanga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=khatanga
    Processing Record 274 of 605 | paamiut
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=paamiut
    Processing Record 275 of 605 | guerrero negro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=guerrero negro
    Processing Record 276 of 605 | sur
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sur
    Processing Record 277 of 605 | bahia blanca
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bahia blanca
    Processing Record 278 of 605 | constitucion
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=constitucion
    Processing Record 279 of 605 | gambo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gambo
    Processing Record 280 of 605 | bima
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bima
    Processing Record 281 of 605 | nam tha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nam tha
    Processing Record 282 of 605 | hervey bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hervey bay
    Processing Record 283 of 605 | galbshtadt
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=galbshtadt
    Processing Record 284 of 605 | zhuzhou
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zhuzhou
    Processing Record 285 of 605 | balikpapan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=balikpapan
    Processing Record 286 of 605 | kavaratti
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kavaratti
    Processing Record 287 of 605 | nouadhibou
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nouadhibou
    Processing Record 288 of 605 | kokopo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kokopo
    Processing Record 289 of 605 | amderma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=amderma
    Processing Record 290 of 605 | sabha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sabha
    Processing Record 291 of 605 | puri
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=puri
    Processing Record 292 of 605 | ust-kamchatsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ust-kamchatsk
    Processing Record 293 of 605 | utiroa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=utiroa
    Processing Record 294 of 605 | champerico
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=champerico
    Processing Record 295 of 605 | toledo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=toledo
    Processing Record 296 of 605 | bathsheba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bathsheba
    Processing Record 297 of 605 | ayame
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ayame
    Processing Record 298 of 605 | surgut
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=surgut
    Processing Record 299 of 605 | indramayu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=indramayu
    Processing Record 300 of 605 | codrington
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=codrington
    Processing Record 301 of 605 | silchar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=silchar
    Processing Record 302 of 605 | itarema
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=itarema
    Processing Record 303 of 605 | zharkent
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zharkent
    Processing Record 304 of 605 | teknaf
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=teknaf
    Processing Record 305 of 605 | arahal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=arahal
    Processing Record 306 of 605 | batsfjord
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=batsfjord
    Processing Record 307 of 605 | bonavista
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bonavista
    Processing Record 308 of 605 | barentsburg
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=barentsburg
    Processing Record 309 of 605 | cabo san lucas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cabo san lucas
    Processing Record 310 of 605 | nemuro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nemuro
    Processing Record 311 of 605 | tsihombe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tsihombe
    Processing Record 312 of 605 | chagda
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chagda
    Processing Record 313 of 605 | iracemapolis
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=iracemapolis
    Processing Record 314 of 605 | egvekinot
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=egvekinot
    Processing Record 315 of 605 | dzhubga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dzhubga
    Processing Record 316 of 605 | ribeira grande
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ribeira grande
    Processing Record 317 of 605 | keuruu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=keuruu
    Processing Record 318 of 605 | harsin
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=harsin
    Processing Record 319 of 605 | itoigawa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=itoigawa
    Processing Record 320 of 605 | lima
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lima
    Processing Record 321 of 605 | russell
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=russell
    Processing Record 322 of 605 | canitas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=canitas
    Processing Record 323 of 605 | daru
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=daru
    Processing Record 324 of 605 | mezen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mezen
    Processing Record 325 of 605 | santa cruz
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa cruz
    Processing Record 326 of 605 | dubrovka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dubrovka
    Processing Record 327 of 605 | shangzhi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shangzhi
    Processing Record 328 of 605 | kirakira
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kirakira
    Processing Record 329 of 605 | saleaula
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saleaula
    Processing Record 330 of 605 | price
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=price
    Processing Record 331 of 605 | jinchang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jinchang
    Processing Record 332 of 605 | port lincoln
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port lincoln
    Processing Record 333 of 605 | metu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=metu
    Processing Record 334 of 605 | banda aceh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=banda aceh
    Processing Record 335 of 605 | djibo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=djibo
    Processing Record 336 of 605 | cairns
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cairns
    Processing Record 337 of 605 | rosetown
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rosetown
    Processing Record 338 of 605 | alta floresta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alta floresta
    Processing Record 339 of 605 | meulaboh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=meulaboh
    Processing Record 340 of 605 | sungaipenuh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sungaipenuh
    Processing Record 341 of 605 | salalah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=salalah
    Processing Record 342 of 605 | antofagasta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=antofagasta
    Processing Record 343 of 605 | vostok
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vostok
    Processing Record 344 of 605 | saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint-pierre
    Processing Record 345 of 605 | nhulunbuy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nhulunbuy
    Processing Record 346 of 605 | maceio
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=maceio
    Processing Record 347 of 605 | phuket
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=phuket
    Processing Record 348 of 605 | walvis bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=walvis bay
    Processing Record 349 of 605 | rio grande
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rio grande
    Processing Record 350 of 605 | faanui
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=faanui
    Processing Record 351 of 605 | faya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=faya
    Processing Record 352 of 605 | warqla
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=warqla
    Processing Record 353 of 605 | poum
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=poum
    Processing Record 354 of 605 | avera
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=avera
    Processing Record 355 of 605 | mys shmidta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mys shmidta
    Processing Record 356 of 605 | darhan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=darhan
    Processing Record 357 of 605 | umzimvubu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=umzimvubu
    Processing Record 358 of 605 | angoche
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=angoche
    Processing Record 359 of 605 | kruisfontein
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kruisfontein
    Processing Record 360 of 605 | halalo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=halalo
    Processing Record 361 of 605 | mollendo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mollendo
    Processing Record 362 of 605 | murray bridge
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=murray bridge
    Processing Record 363 of 605 | benghazi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=benghazi
    Processing Record 364 of 605 | somerset
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=somerset
    Processing Record 365 of 605 | tiruvottiyur
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tiruvottiyur
    Processing Record 366 of 605 | ambilobe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ambilobe
    Processing Record 367 of 605 | blytheville
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=blytheville
    Processing Record 368 of 605 | springdale
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=springdale
    Processing Record 369 of 605 | tecoanapa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tecoanapa
    Processing Record 370 of 605 | broome
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=broome
    Processing Record 371 of 605 | englehart
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=englehart
    Processing Record 372 of 605 | jinchengjiang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jinchengjiang
    Processing Record 373 of 605 | mocuba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mocuba
    Processing Record 374 of 605 | mettur
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mettur
    Processing Record 375 of 605 | barmer
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=barmer
    Processing Record 376 of 605 | gornopravdinsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gornopravdinsk
    Processing Record 377 of 605 | tidore
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tidore
    Processing Record 378 of 605 | wanning
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=wanning
    Processing Record 379 of 605 | dingle
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dingle
    Processing Record 380 of 605 | krasnoselkup
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=krasnoselkup
    Processing Record 381 of 605 | amarillo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=amarillo
    Processing Record 382 of 605 | galveston
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=galveston
    Processing Record 383 of 605 | minsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=minsk
    Processing Record 384 of 605 | garissa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=garissa
    Processing Record 385 of 605 | xai-xai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=xai-xai
    Processing Record 386 of 605 | kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kurilsk
    Processing Record 387 of 605 | katsuura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=katsuura
    Processing Record 388 of 605 | dauriya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dauriya
    Processing Record 389 of 605 | saint-louis
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint-louis
    Processing Record 390 of 605 | grand gaube
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=grand gaube
    Processing Record 391 of 605 | mahadday weyne
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mahadday weyne
    Processing Record 392 of 605 | mwanza
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mwanza
    Processing Record 393 of 605 | kieta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kieta
    Processing Record 394 of 605 | takoradi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=takoradi
    Processing Record 395 of 605 | richards bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=richards bay
    Processing Record 396 of 605 | hailar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hailar
    Processing Record 397 of 605 | jumla
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jumla
    Processing Record 398 of 605 | north bend
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=north bend
    Processing Record 399 of 605 | huarmey
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=huarmey
    Processing Record 400 of 605 | hobyo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hobyo
    Processing Record 401 of 605 | moroni
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=moroni
    Processing Record 402 of 605 | solnechnyy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=solnechnyy
    Processing Record 403 of 605 | paragominas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=paragominas
    Processing Record 404 of 605 | lakes entrance
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lakes entrance
    Processing Record 405 of 605 | alekseyevsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alekseyevsk
    Processing Record 406 of 605 | valle de san juan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=valle de san juan
    Processing Record 407 of 605 | santa rosa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa rosa
    Processing Record 408 of 605 | inhambane
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=inhambane
    Processing Record 409 of 605 | iskateley
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=iskateley
    Processing Record 410 of 605 | tigil
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tigil
    Processing Record 411 of 605 | puerto quijarro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=puerto quijarro
    Processing Record 412 of 605 | goundam
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=goundam
    Processing Record 413 of 605 | caravelas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=caravelas
    Processing Record 414 of 605 | anadyr
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=anadyr
    Processing Record 415 of 605 | chippewa falls
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chippewa falls
    Processing Record 416 of 605 | alofi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alofi
    Processing Record 417 of 605 | atbasar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=atbasar
    Processing Record 418 of 605 | ponyri
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ponyri
    Processing Record 419 of 605 | molteno
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=molteno
    Processing Record 420 of 605 | jinxiang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jinxiang
    Processing Record 421 of 605 | cedar city
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cedar city
    Processing Record 422 of 605 | verkhnevilyuysk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=verkhnevilyuysk
    Processing Record 423 of 605 | todos santos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=todos santos
    Processing Record 424 of 605 | berezovyy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=berezovyy
    Processing Record 425 of 605 | lincoln
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lincoln
    Processing Record 426 of 605 | vila velha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vila velha
    Processing Record 427 of 605 | silyanah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=silyanah
    Processing Record 428 of 605 | pisco
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pisco
    Processing Record 429 of 605 | high level
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=high level
    Processing Record 430 of 605 | karauzyak
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=karauzyak
    Processing Record 431 of 605 | kaitangata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kaitangata
    Processing Record 432 of 605 | half moon bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=half moon bay
    Processing Record 433 of 605 | aberdeen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aberdeen
    Processing Record 434 of 605 | ulaangom
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ulaangom
    Processing Record 435 of 605 | tabou
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tabou
    Processing Record 436 of 605 | fallon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=fallon
    Processing Record 437 of 605 | anaconda
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=anaconda
    Processing Record 438 of 605 | lucea
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lucea
    Processing Record 439 of 605 | kenitra
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kenitra
    Processing Record 440 of 605 | kaputa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kaputa
    Processing Record 441 of 605 | lubango
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lubango
    Processing Record 442 of 605 | honjo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=honjo
    Processing Record 443 of 605 | lolua
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lolua
    Processing Record 444 of 605 | cilegon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cilegon
    Processing Record 445 of 605 | karakendzha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=karakendzha
    Processing Record 446 of 605 | corrales
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=corrales
    Processing Record 447 of 605 | ocos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ocos
    Processing Record 448 of 605 | mahon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mahon
    Processing Record 449 of 605 | sampit
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sampit
    Processing Record 450 of 605 | arlit
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=arlit
    Processing Record 451 of 605 | ponta do sol
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ponta do sol
    Processing Record 452 of 605 | lagoa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lagoa
    Processing Record 453 of 605 | sao borja
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sao borja
    Processing Record 454 of 605 | aykhal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aykhal
    Processing Record 455 of 605 | longyearbyen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=longyearbyen
    Processing Record 456 of 605 | viedma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=viedma
    Processing Record 457 of 605 | carutapera
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=carutapera
    Processing Record 458 of 605 | vaitupu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vaitupu
    Processing Record 459 of 605 | sabzevar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sabzevar
    Processing Record 460 of 605 | marshall
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=marshall
    Processing Record 461 of 605 | eureka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=eureka
    Processing Record 462 of 605 | aitape
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aitape
    Processing Record 463 of 605 | nantucket
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nantucket
    Processing Record 464 of 605 | iquique
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=iquique
    Processing Record 465 of 605 | porto santo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=porto santo
    Processing Record 466 of 605 | mgandu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mgandu
    Processing Record 467 of 605 | burica
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=burica
    Processing Record 468 of 605 | cabedelo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cabedelo
    Processing Record 469 of 605 | kilrush
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kilrush
    Processing Record 470 of 605 | amahai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=amahai
    Processing Record 471 of 605 | lata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lata
    Processing Record 472 of 605 | sentyabrskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sentyabrskiy
    Processing Record 473 of 605 | gillette
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gillette
    Processing Record 474 of 605 | fasa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=fasa
    Processing Record 475 of 605 | nyrob
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nyrob
    Processing Record 476 of 605 | goderich
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=goderich
    Processing Record 477 of 605 | quzhou
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=quzhou
    Processing Record 478 of 605 | vestmanna
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vestmanna
    Processing Record 479 of 605 | okhotsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=okhotsk
    Processing Record 480 of 605 | boa vista
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=boa vista
    Processing Record 481 of 605 | bramming
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bramming
    Processing Record 482 of 605 | zaysan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zaysan
    Processing Record 483 of 605 | upata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=upata
    Processing Record 484 of 605 | ladario
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ladario
    Processing Record 485 of 605 | mpraeso
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mpraeso
    Processing Record 486 of 605 | keelung
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=keelung
    Processing Record 487 of 605 | bambous virieux
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bambous virieux
    Processing Record 488 of 605 | laguna
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=laguna
    Processing Record 489 of 605 | kalomo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kalomo
    Processing Record 490 of 605 | kiama
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kiama
    Processing Record 491 of 605 | hauterive
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hauterive
    Processing Record 492 of 605 | igarka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=igarka
    Processing Record 493 of 605 | dali
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dali
    Processing Record 494 of 605 | killarney
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=killarney
    Processing Record 495 of 605 | katangli
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=katangli
    Processing Record 496 of 605 | kismayo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kismayo
    Processing Record 497 of 605 | qaqortoq
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=qaqortoq
    Processing Record 498 of 605 | bull savanna
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bull savanna
    Processing Record 499 of 605 | sao joao da barra
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sao joao da barra
    Processing Record 500 of 605 | ketchikan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ketchikan
    Processing Record 501 of 605 | dongsheng
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dongsheng
    Processing Record 502 of 605 | piojo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=piojo
    Processing Record 503 of 605 | vestmannaeyjar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vestmannaeyjar
    Processing Record 504 of 605 | illapel
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=illapel
    Processing Record 505 of 605 | contamana
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=contamana
    Processing Record 506 of 605 | tautira
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tautira
    Processing Record 507 of 605 | omboue
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=omboue
    Processing Record 508 of 605 | grindavik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=grindavik
    Processing Record 509 of 605 | nguiu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nguiu
    Processing Record 510 of 605 | quatre cocos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=quatre cocos
    Processing Record 511 of 605 | bani
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bani
    Processing Record 512 of 605 | anloga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=anloga
    Processing Record 513 of 605 | teya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=teya
    Processing Record 514 of 605 | ylivieska
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ylivieska
    Processing Record 515 of 605 | san isidro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san isidro
    Processing Record 516 of 605 | jurmala
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jurmala
    Processing Record 517 of 605 | geresk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=geresk
    Processing Record 518 of 605 | butterworth
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=butterworth
    Processing Record 519 of 605 | saint-leu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint-leu
    Processing Record 520 of 605 | waipawa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=waipawa
    Processing Record 521 of 605 | kondinskoye
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kondinskoye
    Processing Record 522 of 605 | new glasgow
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=new glasgow
    Processing Record 523 of 605 | bangolo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bangolo
    Processing Record 524 of 605 | ahuimanu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ahuimanu
    Processing Record 525 of 605 | mina
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mina
    Processing Record 526 of 605 | borogontsy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=borogontsy
    Processing Record 527 of 605 | cuauhtemoc
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cuauhtemoc
    Processing Record 528 of 605 | altamont
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=altamont
    Processing Record 529 of 605 | kodinsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kodinsk
    Processing Record 530 of 605 | phumi samraong
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=phumi samraong
    Processing Record 531 of 605 | kencong
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kencong
    Processing Record 532 of 605 | yuli
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yuli
    Processing Record 533 of 605 | pio xii
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pio xii
    Processing Record 534 of 605 | cayenne
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cayenne
    Processing Record 535 of 605 | ajaccio
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ajaccio
    Processing Record 536 of 605 | pedasi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pedasi
    Processing Record 537 of 605 | pakxan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pakxan
    Processing Record 538 of 605 | portland
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=portland
    Processing Record 539 of 605 | dingzhou
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dingzhou
    Processing Record 540 of 605 | bowen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bowen
    Processing Record 541 of 605 | stepnyak
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=stepnyak
    Processing Record 542 of 605 | jagdispur
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jagdispur
    Processing Record 543 of 605 | wanlaweyn
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=wanlaweyn
    Processing Record 544 of 605 | mayo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mayo
    Processing Record 545 of 605 | rexburg
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rexburg
    Processing Record 546 of 605 | el tigre
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=el tigre
    Processing Record 547 of 605 | krasne
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=krasne
    Processing Record 548 of 605 | dudinka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dudinka
    Processing Record 549 of 605 | andros town
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=andros town
    Processing Record 550 of 605 | kholtoson
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kholtoson
    Processing Record 551 of 605 | san cristobal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san cristobal
    Processing Record 552 of 605 | porto belo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=porto belo
    Processing Record 553 of 605 | ust-karsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ust-karsk
    Processing Record 554 of 605 | havelock
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=havelock
    Processing Record 555 of 605 | grand-lahou
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=grand-lahou
    Processing Record 556 of 605 | kargil
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kargil
    Processing Record 557 of 605 | kazalinsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kazalinsk
    Processing Record 558 of 605 | lokosovo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lokosovo
    Processing Record 559 of 605 | dadu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dadu
    Processing Record 560 of 605 | pathein
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pathein
    Processing Record 561 of 605 | suntar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=suntar
    Processing Record 562 of 605 | katyuzhanka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=katyuzhanka
    Processing Record 563 of 605 | allingabro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=allingabro
    Processing Record 564 of 605 | te anau
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=te anau
    Processing Record 565 of 605 | honiara
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=honiara
    Processing Record 566 of 605 | tacoronte
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tacoronte
    Processing Record 567 of 605 | suhindol
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=suhindol
    Processing Record 568 of 605 | progreso
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=progreso
    Processing Record 569 of 605 | chapais
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chapais
    Processing Record 570 of 605 | satitoa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=satitoa
    Processing Record 571 of 605 | tambura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tambura
    Processing Record 572 of 605 | leh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=leh
    Processing Record 573 of 605 | husavik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=husavik
    Processing Record 574 of 605 | colares
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=colares
    Processing Record 575 of 605 | formoso do araguaia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=formoso do araguaia
    Processing Record 576 of 605 | port hardy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port hardy
    Processing Record 577 of 605 | arkadelphia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=arkadelphia
    Processing Record 578 of 605 | qasigiannguit
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=qasigiannguit
    Processing Record 579 of 605 | mpulungu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mpulungu
    Processing Record 580 of 605 | nizhnevartovsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nizhnevartovsk
    Processing Record 581 of 605 | thinadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=thinadhoo
    Processing Record 582 of 605 | gangarampur
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gangarampur
    Processing Record 583 of 605 | aripuana
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aripuana
    Processing Record 584 of 605 | kinsale
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kinsale
    Processing Record 585 of 605 | gamba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gamba
    Processing Record 586 of 605 | ixtapa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ixtapa
    Processing Record 587 of 605 | andenes
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=andenes
    Processing Record 588 of 605 | puerto escondido
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=puerto escondido
    Processing Record 589 of 605 | mattru
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mattru
    Processing Record 590 of 605 | piacabucu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=piacabucu
    Processing Record 591 of 605 | toliary
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=toliary
    Processing Record 592 of 605 | esmeraldas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=esmeraldas
    Processing Record 593 of 605 | ambodifototra
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ambodifototra
    Processing Record 594 of 605 | dzhusaly
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dzhusaly
    Processing Record 595 of 605 | bereda
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bereda
    Processing Record 596 of 605 | yomitan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yomitan
    Processing Record 597 of 605 | truro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=truro
    Processing Record 598 of 605 | dakar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dakar
    Processing Record 599 of 605 | ashtabula
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ashtabula
    Processing Record 600 of 605 | tucumcari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tucumcari
    Processing Record 601 of 605 | nayoro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nayoro
    Processing Record 602 of 605 | agropoli
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=agropoli
    Processing Record 603 of 605 | candido mendes
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=candido mendes
    Processing Record 604 of 605 | shenxian
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shenxian
    Processing Record 605 of 605 | madang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=madang





<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Country</th>
      <th>Cloudiness</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>aksarka</td>
      <td>ru</td>
      <td>8</td>
      <td>1505496352</td>
      <td>85</td>
      <td>66.464078</td>
      <td>69.203987</td>
      <td>34.2068</td>
      <td>4.58</td>
    </tr>
    <tr>
      <th>1</th>
      <td>geraldton</td>
      <td>au</td>
      <td>20</td>
      <td>1505494800</td>
      <td>93</td>
      <td>-35.239901</td>
      <td>88.059787</td>
      <td>59</td>
      <td>3.1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>santa vitoria do palmar</td>
      <td>br</td>
      <td>56</td>
      <td>1505496358</td>
      <td>85</td>
      <td>-33.474084</td>
      <td>-52.173456</td>
      <td>61.4768</td>
      <td>7.13</td>
    </tr>
    <tr>
      <th>3</th>
      <td>new norfolk</td>
      <td>au</td>
      <td>20</td>
      <td>1505494800</td>
      <td>65</td>
      <td>-75.596752</td>
      <td>124.140291</td>
      <td>41</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>san patricio</td>
      <td>mx</td>
      <td>75</td>
      <td>1505493720</td>
      <td>75</td>
      <td>0.593424</td>
      <td>-114.506393</td>
      <td>89.6</td>
      <td>1.83</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Break out Series for plots
lats = city_data.iloc[:, 5]
lngs = city_data.iloc[:, 6]
temps = city_data.iloc[:, 7]
winds = city_data.iloc[:, 8]
humids = city_data.iloc[:, 4]
clouds = city_data.iloc[:, 2]

# Save CSV of weather data externally
city_data.to_csv("output/WeatherData.csv")
city_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Country</th>
      <th>Cloudiness</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>aksarka</td>
      <td>ru</td>
      <td>8</td>
      <td>1505496352</td>
      <td>85</td>
      <td>66.464078</td>
      <td>69.203987</td>
      <td>34.2068</td>
      <td>4.58</td>
    </tr>
    <tr>
      <th>1</th>
      <td>geraldton</td>
      <td>au</td>
      <td>20</td>
      <td>1505494800</td>
      <td>93</td>
      <td>-35.239901</td>
      <td>88.059787</td>
      <td>59</td>
      <td>3.1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>santa vitoria do palmar</td>
      <td>br</td>
      <td>56</td>
      <td>1505496358</td>
      <td>85</td>
      <td>-33.474084</td>
      <td>-52.173456</td>
      <td>61.4768</td>
      <td>7.13</td>
    </tr>
    <tr>
      <th>3</th>
      <td>new norfolk</td>
      <td>au</td>
      <td>20</td>
      <td>1505494800</td>
      <td>65</td>
      <td>-75.596752</td>
      <td>124.140291</td>
      <td>41</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>san patricio</td>
      <td>mx</td>
      <td>75</td>
      <td>1505493720</td>
      <td>75</td>
      <td>0.593424</td>
      <td>-114.506393</td>
      <td>89.6</td>
      <td>1.83</td>
    </tr>
  </tbody>
</table>
</div>



### Latitude vs. Temperature Plot


```python
# Initialize plot + format the scatter
fig, ax = plt.subplots()
ax.scatter(lats, temps, alpha=0.75, facecolor="darkblue", edgecolors="black")

# Format and print the full plot
plt.title("City Latitude vs. Max Temperature (09/14/17)")
plt.xlabel("Latitude")
plt.ylabel("Max Temperature (F)")

plt.xlim(-95,95)
plt.ylim(0,110)
plt.savefig("Resources/Latitude_MaxTemperature.png")
plt.show()
```


![png](output_8_0.png)


### Latitude vs. Humidity Plot


```python
# Initialize plot + format the scatter
fig, ax = plt.subplots()
ax.scatter(lats, humids, alpha=0.75, facecolor="darkblue", edgecolors="black")

# Format and print the full plot
plt.title("City Latitude vs. Humidity (09/14/17)")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")

plt.xlim(-95,95)
plt.ylim(-5,105)
plt.savefig("Resources/Latitude_Humidity.png")
plt.show()
```


![png](output_10_0.png)


### Latitude vs. Cloudiness Plot


```python
# Initialize plot + format the scatter
fig, ax = plt.subplots()
ax.scatter(lats, clouds, alpha=0.75, facecolor="darkblue", edgecolors="black")

# Format and print the full plot
plt.title("City Latitude vs. Cloudiness (09/14/17)")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")

plt.xlim(-95,95)
plt.ylim(-5,105)
plt.savefig("Resources/Latitude_Cloudiness.png")
plt.show()
```


![png](output_12_0.png)


### Latitude vs. Wind Speed Plot


```python
# Initialize plot + format the scatter
fig, ax = plt.subplots()
ax.scatter(lats, winds, alpha=0.75, facecolor="darkblue", edgecolors="black")

# Format and print the full plot
plt.title("City Latitude vs. Wind Speed (09/14/17)")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (MPH)")

plt.xlim(-95,95)
plt.ylim(-5,20)
plt.savefig("Resources/Latitude_WindSpeed.png")
plt.show()
```


![png](output_14_0.png)



```python

```
