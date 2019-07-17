
***
# WEEK 3 ASSIGMENT part 1
by HAMM 
***

### *Libraries*


```python
import numpy as np
import pandas as pd
import requests
from bs4 import BeautifulSoup
```

### *Wiki database*


```python
Data = requests.get('https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M').text
DataClean = BeautifulSoup(Data)
```

### *Table*


```python
table = DataClean.find('table',{'class':'wikitable sortable'})

TableEtq = table.findAll('th') 
Etq = []
for tl in TableEtq:
    Etq.append(tl.get_text(strip=True))

TableFila = table.findAll('tr')
Fila= []
for x in TableFila:
    y = x.find_all('td')
    Fil = [entrada.get_text(strip=True) for entrada in y]
    Fila.append(Fil)
del(Fila[0]) 

Data = pd.DataFrame(Fila,columns=Etq) 

```

### *Results Table*


```python
Data.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
  </tbody>
</table>
</div>



### *Clean Table*


```python
DataLimpia= Data[Data.Borough != 'Not assigned'] 

DataLimpia = DataLimpia.groupby('Postcode', as_index=False)
DataLimpia = DataLimpia.aggregate({'Borough': 'first', 'Neighbourhood': ', '.join})
DataLimpia = DataLimpia[['Postcode', 'Borough', 'Neighbourhood']]

DataLimpia.loc[DataLimpia.Neighbourhood == 'Not assigned', 'Neighbourhood'] \
= DataLimpia.loc[DataLimpia.Neighbourhood == 'Not assigned', 'Borough']
```

### *Final Result  & Table*


```python
DataLimpia.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge, Malvern</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Highland Creek, Rouge Hill, Port Union</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood, Morningside, West Hill</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('Data Clean: ', DataLimpia.shape)
```

    Data Clean:  (103, 3)
    

***
# WEEK 3 ASSIGMENT part 2 
by HAMM
***

### *Libraries*


```python
from io import StringIO
```

### *PostalCodes*


```python
posicion1 = requests.get('https://cocl.us/Geospatial_data')
posicion2 = pd.read_csv(StringIO(posicion1.text))

if np.unique(posicion2['Postal Code'] == DataLimpia['Postcode']):
    DataLimpia[['Latitude', 'Longitude']] = posicion2[['Latitude', 'Longitude']]
else:
    Print('No Postal Codes')
```

### *Result*


```python
DataLimpia.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge, Malvern</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Highland Creek, Rouge Hill, Port Union</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood, Morningside, West Hill</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>



***
# WEEK 3 ASSIGMENT part 3 
by HAMM
***

### *Libraries*


```python
import folium
```

### *MAP OF TORONTO*


```python
MAPA = folium.Map(location=[43.65, -79.38],zoom_start=10)

folium.Marker(location=[43.77, -79.24],popup='Cerdarbrae',color='red',fill=True).add_to(MAPA)

folium.Marker(location=[43.77, -79.22],popup='Woburn',color='blue',fill=True).add_to(MAPA)

```




    <folium.map.Marker at 0x1e23973b908>




```python
MAPA
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgCiAgICAgICAgPHNjcmlwdD4KICAgICAgICAgICAgTF9OT19UT1VDSCA9IGZhbHNlOwogICAgICAgICAgICBMX0RJU0FCTEVfM0QgPSBmYWxzZTsKICAgICAgICA8L3NjcmlwdD4KICAgIAogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY2RuLmpzZGVsaXZyLm5ldC9ucG0vbGVhZmxldEAxLjQuMC9kaXN0L2xlYWZsZXQuanMiPjwvc2NyaXB0PgogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY29kZS5qcXVlcnkuY29tL2pxdWVyeS0xLjEyLjQubWluLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9qcy9ib290c3RyYXAubWluLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5qcyI+PC9zY3JpcHQ+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vY2RuLmpzZGVsaXZyLm5ldC9ucG0vbGVhZmxldEAxLjQuMC9kaXN0L2xlYWZsZXQuY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vYm9vdHN0cmFwLzMuMi4wL2Nzcy9ib290c3RyYXAubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLXRoZW1lLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9mb250LWF3ZXNvbWUvNC42LjMvY3NzL2ZvbnQtYXdlc29tZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vY2RuanMuY2xvdWRmbGFyZS5jb20vYWpheC9saWJzL0xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLzIuMC4yL2xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL3Jhd2Nkbi5naXRoYWNrLmNvbS9weXRob24tdmlzdWFsaXphdGlvbi9mb2xpdW0vbWFzdGVyL2ZvbGl1bS90ZW1wbGF0ZXMvbGVhZmxldC5hd2Vzb21lLnJvdGF0ZS5jc3MiLz4KICAgIDxzdHlsZT5odG1sLCBib2R5IHt3aWR0aDogMTAwJTtoZWlnaHQ6IDEwMCU7bWFyZ2luOiAwO3BhZGRpbmc6IDA7fTwvc3R5bGU+CiAgICA8c3R5bGU+I21hcCB7cG9zaXRpb246YWJzb2x1dGU7dG9wOjA7Ym90dG9tOjA7cmlnaHQ6MDtsZWZ0OjA7fTwvc3R5bGU+CiAgICAKICAgICAgICAgICAgPG1ldGEgbmFtZT0idmlld3BvcnQiIGNvbnRlbnQ9IndpZHRoPWRldmljZS13aWR0aCwKICAgICAgICAgICAgICAgIGluaXRpYWwtc2NhbGU9MS4wLCBtYXhpbXVtLXNjYWxlPTEuMCwgdXNlci1zY2FsYWJsZT1ubyIgLz4KICAgICAgICAgICAgPHN0eWxlPgogICAgICAgICAgICAgICAgI21hcF9kNTMxYmUwNTg1OTE0MzUxYThlNTQzNzMwM2YwZDEwNiB7CiAgICAgICAgICAgICAgICAgICAgcG9zaXRpb246IHJlbGF0aXZlOwogICAgICAgICAgICAgICAgICAgIHdpZHRoOiAxMDAuMCU7CiAgICAgICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICAgICAgbGVmdDogMC4wJTsKICAgICAgICAgICAgICAgICAgICB0b3A6IDAuMCU7CiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgIDwvc3R5bGU+CiAgICAgICAgCjwvaGVhZD4KPGJvZHk+ICAgIAogICAgCiAgICAgICAgICAgIDxkaXYgY2xhc3M9ImZvbGl1bS1tYXAiIGlkPSJtYXBfZDUzMWJlMDU4NTkxNDM1MWE4ZTU0MzczMDNmMGQxMDYiID48L2Rpdj4KICAgICAgICAKPC9ib2R5Pgo8c2NyaXB0PiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFwX2Q1MzFiZTA1ODU5MTQzNTFhOGU1NDM3MzAzZjBkMTA2ID0gTC5tYXAoCiAgICAgICAgICAgICAgICAibWFwX2Q1MzFiZTA1ODU5MTQzNTFhOGU1NDM3MzAzZjBkMTA2IiwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBjZW50ZXI6IFs0My42NSwgLTc5LjM4XSwKICAgICAgICAgICAgICAgICAgICBjcnM6IEwuQ1JTLkVQU0czODU3LAogICAgICAgICAgICAgICAgICAgIHpvb206IDEwLAogICAgICAgICAgICAgICAgICAgIHpvb21Db250cm9sOiB0cnVlLAogICAgICAgICAgICAgICAgICAgIHByZWZlckNhbnZhczogZmFsc2UsCiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICk7CgogICAgICAgICAgICAKCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfNzM5ZDc0NWY5YTU5NDY4Yjg5M2ZhMzIyMGFlMDU1NTQgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICJodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZyIsCiAgICAgICAgICAgICAgICB7ImF0dHJpYnV0aW9uIjogIkRhdGEgYnkgXHUwMDI2Y29weTsgXHUwMDNjYSBocmVmPVwiaHR0cDovL29wZW5zdHJlZXRtYXAub3JnXCJcdTAwM2VPcGVuU3RyZWV0TWFwXHUwMDNjL2FcdTAwM2UsIHVuZGVyIFx1MDAzY2EgaHJlZj1cImh0dHA6Ly93d3cub3BlbnN0cmVldG1hcC5vcmcvY29weXJpZ2h0XCJcdTAwM2VPRGJMXHUwMDNjL2FcdTAwM2UuIiwgImRldGVjdFJldGluYSI6IGZhbHNlLCAibWF4TmF0aXZlWm9vbSI6IDE4LCAibWF4Wm9vbSI6IDE4LCAibWluWm9vbSI6IDAsICJub1dyYXAiOiBmYWxzZSwgIm9wYWNpdHkiOiAxLCAic3ViZG9tYWlucyI6ICJhYmMiLCAidG1zIjogZmFsc2V9CiAgICAgICAgICAgICkuYWRkVG8obWFwX2Q1MzFiZTA1ODU5MTQzNTFhOGU1NDM3MzAzZjBkMTA2KTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFya2VyXzFmZTBiMzgxYzA0MDRiNmFhNDI0YzJjOTA1ODZhNGMwID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzcsIC03OS4yNF0sCiAgICAgICAgICAgICAgICB7ImNvbG9yIjogInJlZCIsICJmaWxsIjogdHJ1ZX0KICAgICAgICAgICAgKS5hZGRUbyhtYXBfZDUzMWJlMDU4NTkxNDM1MWE4ZTU0MzczMDNmMGQxMDYpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2QyYjYyMmI3NjE2NjQzNjZhNjU5MTkyZWI4ODMxNzFlID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9lNzVkNDM0OTRjMWM0ZjIxYWUyNGIzNDA5OTM3ODVlNyA9ICQoYDxkaXYgaWQ9Imh0bWxfZTc1ZDQzNDk0YzFjNGYyMWFlMjRiMzQwOTkzNzg1ZTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNlcmRhcmJyYWU8L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfZDJiNjIyYjc2MTY2NDM2NmE2NTkxOTJlYjg4MzE3MWUuc2V0Q29udGVudChodG1sX2U3NWQ0MzQ5NGMxYzRmMjFhZTI0YjM0MDk5Mzc4NWU3KTsKICAgICAgICAKCiAgICAgICAgbWFya2VyXzFmZTBiMzgxYzA0MDRiNmFhNDI0YzJjOTA1ODZhNGMwLmJpbmRQb3B1cChwb3B1cF9kMmI2MjJiNzYxNjY0MzY2YTY1OTE5MmViODgzMTcxZSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIG1hcmtlcl9mNWJjMTE5YjE2NGQ0MmI4OTQ0MTE5YTA5NjNiYzQ1NSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3LCAtNzkuMjJdLAogICAgICAgICAgICAgICAgeyJjb2xvciI6ICJibHVlIiwgImZpbGwiOiB0cnVlfQogICAgICAgICAgICApLmFkZFRvKG1hcF9kNTMxYmUwNTg1OTE0MzUxYThlNTQzNzMwM2YwZDEwNik7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfOTQ5ODk3Nzc1ODJlNGY1Y2I1YjUwYWJiYTA2MjczZTkgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sXzA1Zjc5NTFjM2UwZDRkNjE5YjU0ZDE2MzYxZjQ4NWM2ID0gJChgPGRpdiBpZD0iaHRtbF8wNWY3OTUxYzNlMGQ0ZDYxOWI1NGQxNjM2MWY0ODVjNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V29idXJuPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzk0OTg5Nzc3NTgyZTRmNWNiNWI1MGFiYmEwNjI3M2U5LnNldENvbnRlbnQoaHRtbF8wNWY3OTUxYzNlMGQ0ZDYxOWI1NGQxNjM2MWY0ODVjNik7CiAgICAgICAgCgogICAgICAgIG1hcmtlcl9mNWJjMTE5YjE2NGQ0MmI4OTQ0MTE5YTA5NjNiYzQ1NS5iaW5kUG9wdXAocG9wdXBfOTQ5ODk3Nzc1ODJlNGY1Y2I1YjUwYWJiYTA2MjczZTkpCiAgICAgICAgOwoKICAgICAgICAKICAgIAo8L3NjcmlwdD4=" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>


