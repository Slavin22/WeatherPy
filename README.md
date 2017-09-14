
# Pyber Ride Sharing

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




    613




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
      <td>mabaruma</td>
      <td>gy</td>
      <td></td>
      <td></td>
      <td></td>
      <td>9.137290</td>
      <td>-59.148327</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>butaritari</td>
      <td>ki</td>
      <td></td>
      <td></td>
      <td></td>
      <td>17.029502</td>
      <td>169.006132</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>albany</td>
      <td>au</td>
      <td></td>
      <td></td>
      <td></td>
      <td>-65.509853</td>
      <td>114.406913</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>khatanga</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td>78.051246</td>
      <td>100.307766</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>faanui</td>
      <td>pf</td>
      <td></td>
      <td></td>
      <td></td>
      <td>-9.732412</td>
      <td>-156.053334</td>
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
    try:
        city_url = weather_url + weather_key + row["City"]
        print("Processing Record " + str(counter+1) + " of " + str(len(city_data)) + " | " + row["City"])
        print(city_url)
        city_response = requests.get(city_url)
        city_json = city_response.json()
        city_data.set_value(counter, "Cloudiness", city_json["clouds"]["all"])
        city_data.set_value(counter, "Date", city_json["dt"])
        city_data.set_value(counter, "Humidity", city_json["main"]["humidity"])
        city_data.set_value(counter, "Max Temp", city_json["main"]["temp_max"])
        city_data.set_value(counter, "Wind Speed", city_json["wind"]["speed"])
        counter = counter + 1
        time.sleep(.1)
    except:
        print("Error: skip city and continue")
        counter = counter + 1
    
city_data.head()
```

    Beginning Data Retrieval
    ------------------------
    Processing Record 1 of 613 | mabaruma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mabaruma
    Processing Record 2 of 613 | butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=butaritari
    Processing Record 3 of 613 | albany
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=albany
    Processing Record 4 of 613 | khatanga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=khatanga
    Processing Record 5 of 613 | faanui
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=faanui
    Processing Record 6 of 613 | kahului
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kahului
    Processing Record 7 of 613 | khuzhir
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=khuzhir
    Processing Record 8 of 613 | bentiu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bentiu
    Processing Record 9 of 613 | vaini
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vaini
    Processing Record 10 of 613 | acapulco
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=acapulco
    Processing Record 11 of 613 | meulaboh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=meulaboh
    Processing Record 12 of 613 | namatanai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=namatanai
    Processing Record 13 of 613 | ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ushuaia
    Processing Record 14 of 613 | yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yellowknife
    Processing Record 15 of 613 | castro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=castro
    Processing Record 16 of 613 | husavik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=husavik
    Processing Record 17 of 613 | nanao
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nanao
    Processing Record 18 of 613 | punta arenas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=punta arenas
    Processing Record 19 of 613 | reidsville
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=reidsville
    Processing Record 20 of 613 | kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kapaa
    Processing Record 21 of 613 | dikson
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dikson
    Processing Record 22 of 613 | nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nikolskoye
    Processing Record 23 of 613 | san patricio
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san patricio
    Processing Record 24 of 613 | cabo san lucas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cabo san lucas
    Processing Record 25 of 613 | hobart
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hobart
    Processing Record 26 of 613 | taolanaro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taolanaro
    Processing Record 27 of 613 | tashla
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tashla
    Processing Record 28 of 613 | chilca
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chilca
    Processing Record 29 of 613 | ahipara
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ahipara
    Processing Record 30 of 613 | katangli
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=katangli
    Processing Record 31 of 613 | illoqqortoormiut
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=illoqqortoormiut
    Processing Record 32 of 613 | attawapiskat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=attawapiskat
    Processing Record 33 of 613 | atuona
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=atuona
    Processing Record 34 of 613 | beyneu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=beyneu
    Processing Record 35 of 613 | nanakuli
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nanakuli
    Processing Record 36 of 613 | fairbanks
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=fairbanks
    Processing Record 37 of 613 | barentsburg
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=barentsburg
    Processing Record 38 of 613 | kloulklubed
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kloulklubed
    Processing Record 39 of 613 | rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rikitea
    Processing Record 40 of 613 | thompson
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=thompson
    Processing Record 41 of 613 | mataura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mataura
    Processing Record 42 of 613 | kyra
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kyra
    Processing Record 43 of 613 | kaitangata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kaitangata
    Processing Record 44 of 613 | souillac
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=souillac
    Processing Record 45 of 613 | morant bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=morant bay
    Processing Record 46 of 613 | san cristobal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san cristobal
    Processing Record 47 of 613 | bluff
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bluff
    Processing Record 48 of 613 | belushya guba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=belushya guba
    Processing Record 49 of 613 | lasa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lasa
    Processing Record 50 of 613 | chuy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chuy
    Processing Record 51 of 613 | ambon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ambon
    Processing Record 52 of 613 | hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hermanus
    Processing Record 53 of 613 | tiksi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tiksi
    Processing Record 54 of 613 | quatre cocos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=quatre cocos
    Processing Record 55 of 613 | marcona
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=marcona
    Processing Record 56 of 613 | aksu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aksu
    Processing Record 57 of 613 | bredasdorp
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bredasdorp
    Processing Record 58 of 613 | maceio
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=maceio
    Processing Record 59 of 613 | pevek
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pevek
    Processing Record 60 of 613 | never
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=never
    Processing Record 61 of 613 | viransehir
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=viransehir
    Processing Record 62 of 613 | nishihara
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nishihara
    Processing Record 63 of 613 | villazon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=villazon
    Processing Record 64 of 613 | samarai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=samarai
    Processing Record 65 of 613 | cape town
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cape town
    Processing Record 66 of 613 | kruisfontein
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kruisfontein
    Processing Record 67 of 613 | clyde river
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=clyde river
    Processing Record 68 of 613 | bariri
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bariri
    Processing Record 69 of 613 | partizanskoye
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=partizanskoye
    Processing Record 70 of 613 | saint-philippe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint-philippe
    Processing Record 71 of 613 | graham
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=graham
    Processing Record 72 of 613 | khormuj
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=khormuj
    Processing Record 73 of 613 | mayo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mayo
    Processing Record 74 of 613 | pisco
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pisco
    Processing Record 75 of 613 | warqla
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=warqla
    Processing Record 76 of 613 | provideniya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=provideniya
    Processing Record 77 of 613 | hualmay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hualmay
    Processing Record 78 of 613 | gacko
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gacko
    Processing Record 79 of 613 | busselton
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=busselton
    Processing Record 80 of 613 | sorland
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sorland
    Processing Record 81 of 613 | daitari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=daitari
    Processing Record 82 of 613 | putina
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=putina
    Processing Record 83 of 613 | rungata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rungata
    Processing Record 84 of 613 | qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=qaanaaq
    Processing Record 85 of 613 | airai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=airai
    Processing Record 86 of 613 | norman wells
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=norman wells
    Processing Record 87 of 613 | genhe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=genhe
    Processing Record 88 of 613 | saskylakh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saskylakh
    Processing Record 89 of 613 | inhambane
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=inhambane
    Processing Record 90 of 613 | chokurdakh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chokurdakh
    Processing Record 91 of 613 | port alfred
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port alfred
    Processing Record 92 of 613 | puerto ayora
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=puerto ayora
    Processing Record 93 of 613 | nhulunbuy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nhulunbuy
    Processing Record 94 of 613 | teya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=teya
    Processing Record 95 of 613 | hilo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hilo
    Processing Record 96 of 613 | jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jamestown
    Processing Record 97 of 613 | barrow
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=barrow
    Processing Record 98 of 613 | imbituba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=imbituba
    Processing Record 99 of 613 | shenjiamen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shenjiamen
    Processing Record 100 of 613 | new norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=new norfolk
    Processing Record 101 of 613 | bijni
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bijni
    Processing Record 102 of 613 | nalut
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nalut
    Processing Record 103 of 613 | la solana
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=la solana
    Processing Record 104 of 613 | hami
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hami
    Processing Record 105 of 613 | hamilton
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hamilton
    Processing Record 106 of 613 | adamas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=adamas
    Processing Record 107 of 613 | marsh harbour
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=marsh harbour
    Processing Record 108 of 613 | codrington
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=codrington
    Processing Record 109 of 613 | saleaula
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saleaula
    Processing Record 110 of 613 | santa vitoria
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa vitoria
    Processing Record 111 of 613 | georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=georgetown
    Processing Record 112 of 613 | cabedelo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cabedelo
    Processing Record 113 of 613 | esperance
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=esperance
    Processing Record 114 of 613 | banda aceh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=banda aceh
    Processing Record 115 of 613 | kasongo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kasongo
    Processing Record 116 of 613 | cadillac
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cadillac
    Processing Record 117 of 613 | lerwick
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lerwick
    Processing Record 118 of 613 | westerly
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=westerly
    Processing Record 119 of 613 | severo-kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=severo-kurilsk
    Processing Record 120 of 613 | port elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port elizabeth
    Processing Record 121 of 613 | skalistyy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=skalistyy
    Processing Record 122 of 613 | mecca
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mecca
    Processing Record 123 of 613 | mount gambier
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mount gambier
    Processing Record 124 of 613 | victoria
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=victoria
    Processing Record 125 of 613 | mwinilunga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mwinilunga
    Processing Record 126 of 613 | amderma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=amderma
    Processing Record 127 of 613 | hofn
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hofn
    Processing Record 128 of 613 | ardesen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ardesen
    Processing Record 129 of 613 | kursenai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kursenai
    Processing Record 130 of 613 | loei
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=loei
    Processing Record 131 of 613 | torrington
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=torrington
    Processing Record 132 of 613 | ribeira grande
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ribeira grande
    Processing Record 133 of 613 | villeneuve-sur-lot
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=villeneuve-sur-lot
    Processing Record 134 of 613 | evensk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=evensk
    Processing Record 135 of 613 | saint george
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint george
    Processing Record 136 of 613 | ijaki
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ijaki
    Processing Record 137 of 613 | candido mendes
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=candido mendes
    Processing Record 138 of 613 | labytnangi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=labytnangi
    Processing Record 139 of 613 | alice springs
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alice springs
    Processing Record 140 of 613 | qingdao
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=qingdao
    Processing Record 141 of 613 | salalah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=salalah
    Processing Record 142 of 613 | lewisporte
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lewisporte
    Processing Record 143 of 613 | vaitupu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vaitupu
    Processing Record 144 of 613 | taoudenni
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taoudenni
    Processing Record 145 of 613 | lompoc
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lompoc
    Processing Record 146 of 613 | merritt
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=merritt
    Processing Record 147 of 613 | saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint-pierre
    Processing Record 148 of 613 | usvyaty
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=usvyaty
    Processing Record 149 of 613 | yar-sale
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yar-sale
    Processing Record 150 of 613 | laredo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=laredo
    Processing Record 151 of 613 | touros
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=touros
    Processing Record 152 of 613 | marolambo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=marolambo
    Processing Record 153 of 613 | zhigansk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zhigansk
    Processing Record 154 of 613 | bubaque
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bubaque
    Processing Record 155 of 613 | kavaratti
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kavaratti
    Processing Record 156 of 613 | shitkino
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shitkino
    Processing Record 157 of 613 | colares
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=colares
    Processing Record 158 of 613 | vila franca do campo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vila franca do campo
    Processing Record 159 of 613 | namibe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=namibe
    Processing Record 160 of 613 | zabol
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zabol
    Processing Record 161 of 613 | pangnirtung
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pangnirtung
    Processing Record 162 of 613 | klaksvik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=klaksvik
    Processing Record 163 of 613 | mahebourg
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mahebourg
    Processing Record 164 of 613 | taboga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taboga
    Processing Record 165 of 613 | halalo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=halalo
    Processing Record 166 of 613 | strezhevoy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=strezhevoy
    Processing Record 167 of 613 | sioux lookout
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sioux lookout
    Processing Record 168 of 613 | upernavik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=upernavik
    Processing Record 169 of 613 | ilulissat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ilulissat
    Processing Record 170 of 613 | alofi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alofi
    Processing Record 171 of 613 | tessalit
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tessalit
    Processing Record 172 of 613 | rio bravo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rio bravo
    Processing Record 173 of 613 | half moon bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=half moon bay
    Processing Record 174 of 613 | longyearbyen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=longyearbyen
    Processing Record 175 of 613 | nioro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nioro
    Processing Record 176 of 613 | worland
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=worland
    Processing Record 177 of 613 | hervey bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hervey bay
    Processing Record 178 of 613 | kuche
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kuche
    Processing Record 179 of 613 | whitianga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=whitianga
    Processing Record 180 of 613 | shimoda
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shimoda
    Processing Record 181 of 613 | goderich
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=goderich
    Processing Record 182 of 613 | wuning
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=wuning
    Processing Record 183 of 613 | lagoa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lagoa
    Processing Record 184 of 613 | yeppoon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yeppoon
    Processing Record 185 of 613 | alghero
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alghero
    Processing Record 186 of 613 | drayton valley
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=drayton valley
    Processing Record 187 of 613 | east london
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=east london
    Processing Record 188 of 613 | camalu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=camalu
    Processing Record 189 of 613 | inderborskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=inderborskiy
    Processing Record 190 of 613 | ancud
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ancud
    Processing Record 191 of 613 | fortuna
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=fortuna
    Processing Record 192 of 613 | ponta do sol
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ponta do sol
    Processing Record 193 of 613 | harper
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=harper
    Processing Record 194 of 613 | baruun-urt
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=baruun-urt
    Processing Record 195 of 613 | sompeta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sompeta
    Processing Record 196 of 613 | presidencia roque saenz pena
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=presidencia roque saenz pena
    Processing Record 197 of 613 | viedma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=viedma
    Processing Record 198 of 613 | bambous virieux
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bambous virieux
    Processing Record 199 of 613 | havoysund
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=havoysund
    Processing Record 200 of 613 | sangar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sangar
    Processing Record 201 of 613 | talakan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=talakan
    Processing Record 202 of 613 | sikonge
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sikonge
    Processing Record 203 of 613 | grindavik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=grindavik
    Processing Record 204 of 613 | ksenyevka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ksenyevka
    Processing Record 205 of 613 | barra do garcas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=barra do garcas
    Processing Record 206 of 613 | lebu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lebu
    Processing Record 207 of 613 | tiznit
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tiznit
    Processing Record 208 of 613 | tsihombe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tsihombe
    Processing Record 209 of 613 | ankang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ankang
    Processing Record 210 of 613 | grand river south east
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=grand river south east
    Processing Record 211 of 613 | tigil
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tigil
    Processing Record 212 of 613 | tortoli
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tortoli
    Processing Record 213 of 613 | great malvern
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=great malvern
    Processing Record 214 of 613 | yerbogachen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yerbogachen
    Error: skip city and continue
    Processing Record 215 of 613 | pacific grove
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pacific grove
    Processing Record 216 of 613 | college
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=college
    Processing Record 217 of 613 | tuggurt
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tuggurt
    Processing Record 218 of 613 | krasnoselkup
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=krasnoselkup
    Processing Record 219 of 613 | yichang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yichang
    Processing Record 220 of 613 | tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tuktoyaktuk
    Processing Record 221 of 613 | arenal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=arenal
    Processing Record 222 of 613 | bira
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bira
    Processing Record 223 of 613 | kirakira
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kirakira
    Processing Record 224 of 613 | coquimbo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=coquimbo
    Processing Record 225 of 613 | kieta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kieta
    Processing Record 226 of 613 | pyshchug
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pyshchug
    Processing Record 227 of 613 | puerto ayacucho
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=puerto ayacucho
    Processing Record 228 of 613 | cherskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cherskiy
    Processing Record 229 of 613 | tuatapere
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tuatapere
    Processing Record 230 of 613 | tulun
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tulun
    Processing Record 231 of 613 | iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=iqaluit
    Processing Record 232 of 613 | mar del plata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mar del plata
    Processing Record 233 of 613 | natal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=natal
    Processing Record 234 of 613 | kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kodiak
    Processing Record 235 of 613 | taltal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taltal
    Processing Record 236 of 613 | jabiru
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jabiru
    Processing Record 237 of 613 | williams lake
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=williams lake
    Processing Record 238 of 613 | umzimvubu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=umzimvubu
    Processing Record 239 of 613 | the valley
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=the valley
    Processing Record 240 of 613 | shubarkuduk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shubarkuduk
    Processing Record 241 of 613 | karamsad
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=karamsad
    Processing Record 242 of 613 | hay river
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hay river
    Processing Record 243 of 613 | port lincoln
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port lincoln
    Processing Record 244 of 613 | tarko-sale
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tarko-sale
    Processing Record 245 of 613 | mount pleasant
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mount pleasant
    Processing Record 246 of 613 | dauriya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dauriya
    Processing Record 247 of 613 | hanyang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hanyang
    Processing Record 248 of 613 | adre
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=adre
    Processing Record 249 of 613 | pandan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pandan
    Processing Record 250 of 613 | carnarvon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=carnarvon
    Processing Record 251 of 613 | raudeberg
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=raudeberg
    Processing Record 252 of 613 | naze
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=naze
    Processing Record 253 of 613 | beaverlodge
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=beaverlodge
    Processing Record 254 of 613 | kuala terengganu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kuala terengganu
    Processing Record 255 of 613 | yanan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yanan
    Processing Record 256 of 613 | ocos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ocos
    Processing Record 257 of 613 | andenes
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=andenes
    Processing Record 258 of 613 | vanimo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vanimo
    Processing Record 259 of 613 | avarua
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=avarua
    Processing Record 260 of 613 | cayenne
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cayenne
    Processing Record 261 of 613 | tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tasiilaq
    Processing Record 262 of 613 | buzmeyin
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=buzmeyin
    Processing Record 263 of 613 | minuri
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=minuri
    Processing Record 264 of 613 | dabat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dabat
    Processing Record 265 of 613 | portland
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=portland
    Processing Record 266 of 613 | sao jose da coroa grande
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sao jose da coroa grande
    Processing Record 267 of 613 | seoul
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=seoul
    Processing Record 268 of 613 | bengkulu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bengkulu
    Processing Record 269 of 613 | karaul
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=karaul
    Processing Record 270 of 613 | port blair
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port blair
    Processing Record 271 of 613 | mchinji
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mchinji
    Processing Record 272 of 613 | yulara
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yulara
    Processing Record 273 of 613 | opuwo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=opuwo
    Processing Record 274 of 613 | najran
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=najran
    Processing Record 275 of 613 | bogdanita
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bogdanita
    Processing Record 276 of 613 | sarkand
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sarkand
    Processing Record 277 of 613 | plattsburgh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=plattsburgh
    Processing Record 278 of 613 | bairiki
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bairiki
    Processing Record 279 of 613 | bosaso
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bosaso
    Processing Record 280 of 613 | san francisco
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san francisco
    Processing Record 281 of 613 | pitimbu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pitimbu
    Processing Record 282 of 613 | upington
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=upington
    Processing Record 283 of 613 | kavieng
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kavieng
    Processing Record 284 of 613 | kajaani
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kajaani
    Processing Record 285 of 613 | mareeba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mareeba
    Processing Record 286 of 613 | severnyy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=severnyy
    Processing Record 287 of 613 | kindu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kindu
    Processing Record 288 of 613 | taga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taga
    Processing Record 289 of 613 | lyskovo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lyskovo
    Processing Record 290 of 613 | bafia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bafia
    Processing Record 291 of 613 | rapid valley
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rapid valley
    Processing Record 292 of 613 | bayan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bayan
    Processing Record 293 of 613 | filadelfia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=filadelfia
    Processing Record 294 of 613 | kontagora
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kontagora
    Processing Record 295 of 613 | pueblo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pueblo
    Processing Record 296 of 613 | richards bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=richards bay
    Processing Record 297 of 613 | waipawa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=waipawa
    Processing Record 298 of 613 | rio gallegos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rio gallegos
    Processing Record 299 of 613 | pahrump
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pahrump
    Processing Record 300 of 613 | muravlenko
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=muravlenko
    Processing Record 301 of 613 | matamoros
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=matamoros
    Processing Record 302 of 613 | mosalsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mosalsk
    Processing Record 303 of 613 | constitucion
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=constitucion
    Processing Record 304 of 613 | saldanha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saldanha
    Processing Record 305 of 613 | balykshi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=balykshi
    Processing Record 306 of 613 | komsomolskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=komsomolskiy
    Processing Record 307 of 613 | sitka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sitka
    Processing Record 308 of 613 | gilgit
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gilgit
    Processing Record 309 of 613 | grand-santi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=grand-santi
    Processing Record 310 of 613 | ohara
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ohara
    Processing Record 311 of 613 | mercedes
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mercedes
    Processing Record 312 of 613 | luderitz
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=luderitz
    Processing Record 313 of 613 | poltavka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=poltavka
    Processing Record 314 of 613 | yishui
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yishui
    Processing Record 315 of 613 | kinablangan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kinablangan
    Processing Record 316 of 613 | ngong
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ngong
    Processing Record 317 of 613 | olafsvik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=olafsvik
    Processing Record 318 of 613 | brae
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=brae
    Processing Record 319 of 613 | kwekwe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kwekwe
    Processing Record 320 of 613 | gimli
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gimli
    Processing Record 321 of 613 | arraial do cabo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=arraial do cabo
    Processing Record 322 of 613 | hihifo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hihifo
    Processing Record 323 of 613 | torbay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=torbay
    Processing Record 324 of 613 | jasidih
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jasidih
    Processing Record 325 of 613 | satitoa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=satitoa
    Processing Record 326 of 613 | maunabo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=maunabo
    Processing Record 327 of 613 | tazovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tazovskiy
    Processing Record 328 of 613 | sao filipe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sao filipe
    Processing Record 329 of 613 | bourbonnais
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bourbonnais
    Processing Record 330 of 613 | oga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=oga
    Processing Record 331 of 613 | aden
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aden
    Processing Record 332 of 613 | poso
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=poso
    Processing Record 333 of 613 | hithadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hithadhoo
    Processing Record 334 of 613 | pousat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pousat
    Processing Record 335 of 613 | riyadh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=riyadh
    Processing Record 336 of 613 | bur gabo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bur gabo
    Processing Record 337 of 613 | ternate
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ternate
    Processing Record 338 of 613 | mancio lima
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mancio lima
    Processing Record 339 of 613 | meyungs
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=meyungs
    Processing Record 340 of 613 | ullapool
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ullapool
    Processing Record 341 of 613 | hickory
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hickory
    Processing Record 342 of 613 | praia da vitoria
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=praia da vitoria
    Processing Record 343 of 613 | mahibadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mahibadhoo
    Processing Record 344 of 613 | tabarqah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tabarqah
    Processing Record 345 of 613 | tecoanapa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tecoanapa
    Processing Record 346 of 613 | high level
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=high level
    Processing Record 347 of 613 | galiwinku
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=galiwinku
    Processing Record 348 of 613 | nome
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nome
    Processing Record 349 of 613 | paracuru
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=paracuru
    Processing Record 350 of 613 | raga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=raga
    Processing Record 351 of 613 | khani
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=khani
    Processing Record 352 of 613 | warrington
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=warrington
    Processing Record 353 of 613 | lucea
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lucea
    Processing Record 354 of 613 | umm kaddadah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=umm kaddadah
    Processing Record 355 of 613 | katy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=katy
    Processing Record 356 of 613 | burica
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=burica
    Processing Record 357 of 613 | linxia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=linxia
    Processing Record 358 of 613 | los llanos de aridane
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=los llanos de aridane
    Processing Record 359 of 613 | luena
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=luena
    Processing Record 360 of 613 | petropavlovsk-kamchatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=petropavlovsk-kamchatskiy
    Processing Record 361 of 613 | narsaq
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=narsaq
    Processing Record 362 of 613 | manoel urbano
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=manoel urbano
    Processing Record 363 of 613 | rincon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rincon
    Processing Record 364 of 613 | stawell
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=stawell
    Processing Record 365 of 613 | troitsko-pechorsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=troitsko-pechorsk
    Processing Record 366 of 613 | nelson bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nelson bay
    Processing Record 367 of 613 | lugovoy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lugovoy
    Processing Record 368 of 613 | emerald
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=emerald
    Processing Record 369 of 613 | palasbari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=palasbari
    Processing Record 370 of 613 | shingu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shingu
    Processing Record 371 of 613 | livramento
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=livramento
    Processing Record 372 of 613 | bahia blanca
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bahia blanca
    Processing Record 373 of 613 | irtyshskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=irtyshskiy
    Processing Record 374 of 613 | dembi dolo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dembi dolo
    Processing Record 375 of 613 | elko
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=elko
    Processing Record 376 of 613 | polunochnoye
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=polunochnoye
    Processing Record 377 of 613 | moose factory
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=moose factory
    Processing Record 378 of 613 | thornbury
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=thornbury
    Processing Record 379 of 613 | talnakh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=talnakh
    Processing Record 380 of 613 | sentyabrskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sentyabrskiy
    Processing Record 381 of 613 | praia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=praia
    Processing Record 382 of 613 | ponta delgada
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ponta delgada
    Processing Record 383 of 613 | tenenkou
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tenenkou
    Processing Record 384 of 613 | tumannyy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tumannyy
    Processing Record 385 of 613 | humaita
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=humaita
    Processing Record 386 of 613 | xining
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=xining
    Processing Record 387 of 613 | doctor pedro p. pena
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=doctor pedro p. pena
    Processing Record 388 of 613 | okhotsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=okhotsk
    Processing Record 389 of 613 | elizabeth city
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=elizabeth city
    Processing Record 390 of 613 | seabra
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=seabra
    Processing Record 391 of 613 | roma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=roma
    Processing Record 392 of 613 | tondano
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tondano
    Processing Record 393 of 613 | henties bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=henties bay
    Processing Record 394 of 613 | niort
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=niort
    Processing Record 395 of 613 | alta floresta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alta floresta
    Processing Record 396 of 613 | avera
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=avera
    Processing Record 397 of 613 | mogadishu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mogadishu
    Processing Record 398 of 613 | newport
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=newport
    Processing Record 399 of 613 | katsuura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=katsuura
    Processing Record 400 of 613 | geraldton
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=geraldton
    Processing Record 401 of 613 | bilma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bilma
    Processing Record 402 of 613 | pyaozerskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pyaozerskiy
    Processing Record 403 of 613 | oromocto
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=oromocto
    Processing Record 404 of 613 | cidreira
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cidreira
    Processing Record 405 of 613 | mount isa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mount isa
    Processing Record 406 of 613 | labutta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=labutta
    Processing Record 407 of 613 | voznesenye
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=voznesenye
    Processing Record 408 of 613 | sharan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sharan
    Processing Record 409 of 613 | amambai
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=amambai
    Processing Record 410 of 613 | austin
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=austin
    Processing Record 411 of 613 | kommunisticheskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kommunisticheskiy
    Processing Record 412 of 613 | tafo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tafo
    Processing Record 413 of 613 | menongue
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=menongue
    Processing Record 414 of 613 | palabuhanratu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=palabuhanratu
    Processing Record 415 of 613 | chuchkovo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chuchkovo
    Processing Record 416 of 613 | cascais
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cascais
    Processing Record 417 of 613 | beringovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=beringovskiy
    Processing Record 418 of 613 | niesky
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=niesky
    Processing Record 419 of 613 | port hedland
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port hedland
    Processing Record 420 of 613 | samsun
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=samsun
    Processing Record 421 of 613 | coahuayana
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=coahuayana
    Processing Record 422 of 613 | mitsamiouli
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mitsamiouli
    Processing Record 423 of 613 | nanton
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nanton
    Processing Record 424 of 613 | devonport
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=devonport
    Processing Record 425 of 613 | batticaloa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=batticaloa
    Processing Record 426 of 613 | vitim
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vitim
    Processing Record 427 of 613 | berbera
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=berbera
    Processing Record 428 of 613 | ekhabi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ekhabi
    Processing Record 429 of 613 | mukhen
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mukhen
    Processing Record 430 of 613 | yei
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yei
    Processing Record 431 of 613 | mayskiy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mayskiy
    Processing Record 432 of 613 | aklavik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aklavik
    Processing Record 433 of 613 | mrirt
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mrirt
    Processing Record 434 of 613 | puerto escondido
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=puerto escondido
    Processing Record 435 of 613 | karaton
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=karaton
    Processing Record 436 of 613 | mildura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mildura
    Processing Record 437 of 613 | chiang khong
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chiang khong
    Processing Record 438 of 613 | axim
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=axim
    Processing Record 439 of 613 | soyo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=soyo
    Processing Record 440 of 613 | hecun
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hecun
    Processing Record 441 of 613 | brewster
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=brewster
    Processing Record 442 of 613 | olinda
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=olinda
    Processing Record 443 of 613 | choma
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=choma
    Processing Record 444 of 613 | sakakah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sakakah
    Processing Record 445 of 613 | oskaloosa
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=oskaloosa
    Processing Record 446 of 613 | nanortalik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nanortalik
    Processing Record 447 of 613 | mys shmidta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mys shmidta
    Processing Record 448 of 613 | folkestone
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=folkestone
    Processing Record 449 of 613 | bisho
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bisho
    Processing Record 450 of 613 | krasnoarmeysk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=krasnoarmeysk
    Processing Record 451 of 613 | tacuarembo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tacuarembo
    Processing Record 452 of 613 | russell
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=russell
    Processing Record 453 of 613 | talcahuano
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=talcahuano
    Processing Record 454 of 613 | wulanhaote
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=wulanhaote
    Processing Record 455 of 613 | kaupanger
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kaupanger
    Processing Record 456 of 613 | jiuquan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jiuquan
    Processing Record 457 of 613 | sanmenxia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sanmenxia
    Processing Record 458 of 613 | salta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=salta
    Processing Record 459 of 613 | fort nelson
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=fort nelson
    Processing Record 460 of 613 | mahajanga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mahajanga
    Processing Record 461 of 613 | tabiauea
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tabiauea
    Processing Record 462 of 613 | sechura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sechura
    Processing Record 463 of 613 | kalanguy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kalanguy
    Processing Record 464 of 613 | seydi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=seydi
    Processing Record 465 of 613 | olenino
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=olenino
    Processing Record 466 of 613 | ust-maya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ust-maya
    Processing Record 467 of 613 | key largo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=key largo
    Processing Record 468 of 613 | san felipe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san felipe
    Processing Record 469 of 613 | nizhneyansk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nizhneyansk
    Processing Record 470 of 613 | hernani
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hernani
    Processing Record 471 of 613 | muros
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=muros
    Processing Record 472 of 613 | itoman
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=itoman
    Processing Record 473 of 613 | bodden town
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bodden town
    Processing Record 474 of 613 | asau
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=asau
    Processing Record 475 of 613 | walvis bay
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=walvis bay
    Processing Record 476 of 613 | port moresby
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=port moresby
    Processing Record 477 of 613 | tamiahua
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tamiahua
    Processing Record 478 of 613 | issaquah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=issaquah
    Processing Record 479 of 613 | adrar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=adrar
    Processing Record 480 of 613 | bluefield
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bluefield
    Processing Record 481 of 613 | karratha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=karratha
    Processing Record 482 of 613 | flinders
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=flinders
    Processing Record 483 of 613 | chulucanas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=chulucanas
    Processing Record 484 of 613 | longlac
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=longlac
    Processing Record 485 of 613 | yozgat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yozgat
    Processing Record 486 of 613 | grajau
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=grajau
    Processing Record 487 of 613 | denpasar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=denpasar
    Processing Record 488 of 613 | bandar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bandar
    Processing Record 489 of 613 | tadine
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tadine
    Processing Record 490 of 613 | bethel
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bethel
    Processing Record 491 of 613 | dharchula
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=dharchula
    Processing Record 492 of 613 | atasu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=atasu
    Processing Record 493 of 613 | homer
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=homer
    Processing Record 494 of 613 | boende
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=boende
    Processing Record 495 of 613 | beidao
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=beidao
    Processing Record 496 of 613 | manokwari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=manokwari
    Processing Record 497 of 613 | stokmarknes
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=stokmarknes
    Processing Record 498 of 613 | varhaug
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=varhaug
    Processing Record 499 of 613 | iranshahr
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=iranshahr
    Processing Record 500 of 613 | buchanan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=buchanan
    Processing Record 501 of 613 | springdale
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=springdale
    Processing Record 502 of 613 | kampot
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kampot
    Processing Record 503 of 613 | carutapera
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=carutapera
    Processing Record 504 of 613 | mochalishche
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mochalishche
    Processing Record 505 of 613 | hovd
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hovd
    Processing Record 506 of 613 | tomatlan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tomatlan
    Processing Record 507 of 613 | renqiu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=renqiu
    Processing Record 508 of 613 | mafinga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mafinga
    Processing Record 509 of 613 | laramie
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=laramie
    Processing Record 510 of 613 | yangshan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yangshan
    Processing Record 511 of 613 | saint-leu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=saint-leu
    Processing Record 512 of 613 | xaltianguis
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=xaltianguis
    Processing Record 513 of 613 | bulolo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bulolo
    Processing Record 514 of 613 | melito di porto salvo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=melito di porto salvo
    Processing Record 515 of 613 | westport
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=westport
    Processing Record 516 of 613 | yeltsovka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yeltsovka
    Processing Record 517 of 613 | temaraia
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=temaraia
    Processing Record 518 of 613 | hasaki
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=hasaki
    Processing Record 519 of 613 | tromso
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tromso
    Processing Record 520 of 613 | ust-ishim
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ust-ishim
    Processing Record 521 of 613 | oranjemund
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=oranjemund
    Processing Record 522 of 613 | sisimiut
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sisimiut
    Processing Record 523 of 613 | lata
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lata
    Processing Record 524 of 613 | sao felix do xingu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sao felix do xingu
    Processing Record 525 of 613 | jinchengjiang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=jinchengjiang
    Processing Record 526 of 613 | gamba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gamba
    Processing Record 527 of 613 | sabang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sabang
    Processing Record 528 of 613 | egvekinot
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=egvekinot
    Processing Record 529 of 613 | ilmajoki
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ilmajoki
    Processing Record 530 of 613 | anloga
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=anloga
    Processing Record 531 of 613 | garden city
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=garden city
    Processing Record 532 of 613 | la ronge
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=la ronge
    Processing Record 533 of 613 | vredendal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vredendal
    Processing Record 534 of 613 | arroyo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=arroyo
    Processing Record 535 of 613 | guymon
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=guymon
    Processing Record 536 of 613 | kaeo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kaeo
    Processing Record 537 of 613 | nouakchott
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nouakchott
    Processing Record 538 of 613 | alegrete
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=alegrete
    Processing Record 539 of 613 | santa cruz
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa cruz
    Processing Record 540 of 613 | porto novo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=porto novo
    Processing Record 541 of 613 | pemangkat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pemangkat
    Processing Record 542 of 613 | ambilobe
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ambilobe
    Processing Record 543 of 613 | majene
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=majene
    Processing Record 544 of 613 | lavrentiya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lavrentiya
    Processing Record 545 of 613 | huarmey
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=huarmey
    Processing Record 546 of 613 | ruteng
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ruteng
    Processing Record 547 of 613 | vestmannaeyjar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vestmannaeyjar
    Processing Record 548 of 613 | sibu
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sibu
    Processing Record 549 of 613 | batagay-alyta
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=batagay-alyta
    Processing Record 550 of 613 | havre-saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=havre-saint-pierre
    Processing Record 551 of 613 | abu dhabi
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=abu dhabi
    Processing Record 552 of 613 | atambua
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=atambua
    Processing Record 553 of 613 | mehamn
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mehamn
    Processing Record 554 of 613 | lima
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lima
    Processing Record 555 of 613 | tocopilla
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tocopilla
    Processing Record 556 of 613 | abu zabad
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=abu zabad
    Processing Record 557 of 613 | tuku
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tuku
    Processing Record 558 of 613 | kholodnyy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=kholodnyy
    Processing Record 559 of 613 | basoko
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=basoko
    Processing Record 560 of 613 | srednekolymsk
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=srednekolymsk
    Processing Record 561 of 613 | lethem
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lethem
    Processing Record 562 of 613 | yurgamysh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=yurgamysh
    Processing Record 563 of 613 | tura
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tura
    Processing Record 564 of 613 | umm ruwabah
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=umm ruwabah
    Processing Record 565 of 613 | nemuro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nemuro
    Processing Record 566 of 613 | conakry
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=conakry
    Processing Record 567 of 613 | bedele
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bedele
    Processing Record 568 of 613 | paranaiba
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=paranaiba
    Processing Record 569 of 613 | faya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=faya
    Processing Record 570 of 613 | paragominas
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=paragominas
    Processing Record 571 of 613 | cap malheureux
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=cap malheureux
    Processing Record 572 of 613 | vastervik
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vastervik
    Processing Record 573 of 613 | nchelenge
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nchelenge
    Processing Record 574 of 613 | mandal
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mandal
    Processing Record 575 of 613 | burgeo
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=burgeo
    Processing Record 576 of 613 | tamandare
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tamandare
    Processing Record 577 of 613 | colimes
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=colimes
    Processing Record 578 of 613 | abha
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=abha
    Processing Record 579 of 613 | taraz
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=taraz
    Processing Record 580 of 613 | sambava
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=sambava
    Processing Record 581 of 613 | tenneville
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tenneville
    Processing Record 582 of 613 | zolotinka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zolotinka
    Processing Record 583 of 613 | vorukh
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=vorukh
    Processing Record 584 of 613 | nantucket
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nantucket
    Processing Record 585 of 613 | uyuni
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=uyuni
    Processing Record 586 of 613 | qovlar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=qovlar
    Processing Record 587 of 613 | santa vitoria do palmar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa vitoria do palmar
    Processing Record 588 of 613 | guerrero negro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=guerrero negro
    Processing Record 589 of 613 | witrivier
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=witrivier
    Processing Record 590 of 613 | preeceville
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=preeceville
    Processing Record 591 of 613 | pontianak
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pontianak
    Processing Record 592 of 613 | shakhovskaya
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=shakhovskaya
    Processing Record 593 of 613 | gadoros
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=gadoros
    Processing Record 594 of 613 | altamont
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=altamont
    Processing Record 595 of 613 | ordzhonikidze
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ordzhonikidze
    Processing Record 596 of 613 | mao
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=mao
    Processing Record 597 of 613 | nha trang
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=nha trang
    Processing Record 598 of 613 | aasiaat
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=aasiaat
    Processing Record 599 of 613 | santa maria del oro
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=santa maria del oro
    Processing Record 600 of 613 | rupert
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=rupert
    Processing Record 601 of 613 | potsdam
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=potsdam
    Processing Record 602 of 613 | guelengdeng
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=guelengdeng
    Processing Record 603 of 613 | argos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=argos
    Processing Record 604 of 613 | tracy
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=tracy
    Processing Record 605 of 613 | lorengau
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=lorengau
    Processing Record 606 of 613 | carauari
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=carauari
    Processing Record 607 of 613 | pandharpur
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=pandharpur
    Processing Record 608 of 613 | zhezkazgan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=zhezkazgan
    Processing Record 609 of 613 | luvianos
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=luvianos
    Processing Record 610 of 613 | bukama
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=bukama
    Processing Record 611 of 613 | maloshuyka
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=maloshuyka
    Processing Record 612 of 613 | ondorhaan
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=ondorhaan
    Processing Record 613 of 613 | san francisco del mar
    http://api.openweathermap.org/data/2.5/weather?appid=0d99449d7128437ead7d88097c7ac304&q=san francisco del mar





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
      <td>mabaruma</td>
      <td>gy</td>
      <td>48</td>
      <td>1505426356</td>
      <td>76</td>
      <td>9.137290</td>
      <td>-59.148327</td>
      <td>302.71</td>
      <td>3.65</td>
    </tr>
    <tr>
      <th>1</th>
      <td>butaritari</td>
      <td>ki</td>
      <td>80</td>
      <td>1505426356</td>
      <td>100</td>
      <td>17.029502</td>
      <td>169.006132</td>
      <td>302.31</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>albany</td>
      <td>au</td>
      <td>75</td>
      <td>1505422440</td>
      <td>65</td>
      <td>-65.509853</td>
      <td>114.406913</td>
      <td>299.15</td>
      <td>2.1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>khatanga</td>
      <td>ru</td>
      <td>8</td>
      <td>1505426357</td>
      <td>88</td>
      <td>78.051246</td>
      <td>100.307766</td>
      <td>275.76</td>
      <td>4.9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>faanui</td>
      <td>pf</td>
      <td>0</td>
      <td>1505426357</td>
      <td>100</td>
      <td>-9.732412</td>
      <td>-156.053334</td>
      <td>299.21</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Convert Temp
city_data["Max Temp"] = city_data["Max Temp"] * 9/5 - 459.67

# Break out Series for plots
lats = city_data.iloc[:, 5]
lngs = city_data.iloc[:, 6]
temps = city_data.iloc[:, 7]
winds = city_data.iloc[:, 8]
humids = city_data.iloc[:, 4]
clouds = city_data.iloc[:, 2]
```

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
plt.savefig("Resources/Latitude_Cloudiness.png")
plt.show()
```


![png](output_14_0.png)



```python

```
