# Исследование объявлений о продаже квартир

В распоряжении данные сервиса Яндекс Недвижимость — архив объявлений за несколько лет о продаже квартир в Санкт-Петербурге и соседних населённых пунктах.

Задача — выполнить предобработку данных и изучить их, чтобы найти интересные особенности и зависимости, которые существуют на рынке недвижимости.

О каждой квартире в базе содержится два типа данных: добавленные пользователем и картографические. Например, к первому типу относятся площадь квартиры, её этаж и количество балконов, ко второму — расстояния до центра города, аэропорта и ближайшего парка. 

### 1. Откройте файл с данными и изучите общую информацию. 
```
import pandas as pd
import matplotlib.pyplot as plt 
import seaborn as sns
```
```
df = pd.read_csv('/datasets/real_estate_data.csv',  sep='\t')
```
выведем первые 5 строк
```
df.head()
```
|   | total_images | last_price | total_area | first_day_exposition | rooms | ceiling_height | floors_total | living_area | floor | is_apartment | ... | kitchen_area | balcony |   locality_name | airports_nearest | cityCenters_nearest | parks_around3000 | parks_nearest | ponds_around3000 | ponds_nearest | days_exposition |
|--:|-------------:|-----------:|-----------:|---------------------:|------:|---------------:|-------------:|------------:|------:|-------------:|----:|-------------:|--------:|----------------:|-----------------:|--------------------:|-----------------:|--------------:|-----------------:|--------------:|----------------:|
| 0 | 20           | 13000000.0 | 108.0      | 2019-03-07T00:00:00  | 3     | 2.70           | 16.0         | 51.0        | 8     | NaN          | ... | 25.0         | NaN     | Санкт-Петербург | 18863.0          | 16028.0             | 1.0              | 482.0         | 2.0              | 755.0         | NaN             |
| 1 | 7            | 3350000.0  | 40.4       | 2018-12-04T00:00:00  | 1     | NaN            | 11.0         | 18.6        | 1     | NaN          | ... | 11.0         | 2.0     | посёлок Шушары  | 12817.0          | 18603.0             | 0.0              | NaN           | 0.0              | NaN           | 81.0            |
| 2 | 10           | 5196000.0  | 56.0       | 2015-08-20T00:00:00  | 2     | NaN            | 5.0          | 34.3        | 4     | NaN          | ... | 8.3          | 0.0     | Санкт-Петербург | 21741.0          | 13933.0             | 1.0              | 90.0          | 2.0              | 574.0         | 558.0           |
| 3 | 0            | 64900000.0 | 159.0      | 2015-07-24T00:00:00  | 3     | NaN            | 14.0         | NaN         | 9     | NaN          | ... | NaN          | 0.0     | Санкт-Петербург | 28098.0          | 6800.0              | 2.0              | 84.0          | 3.0              | 234.0         | 424.0           |
| 4 | 2            | 10000000.0 | 100.0      | 2018-06-19T00:00:00  | 2     | 3.03           | 14.0         | 32.0        | 13    | NaN          | ... | 41.0         | NaN     | Санкт-Петербург | 31856.0          | 8098.0              | 2.0              | 112.0         | 1.0              | 48.0          | 121.0           |

изучим общую информацию о датафрейме
```
df.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 23699 entries, 0 to 23698
Data columns (total 22 columns):
 #   Column                Non-Null Count  Dtype  
---  ------                --------------  -----  
 0   total_images          23699 non-null  int64  
 1   last_price            23699 non-null  float64
 2   total_area            23699 non-null  float64
 3   first_day_exposition  23699 non-null  object 
 4   rooms                 23699 non-null  int64  
 5   ceiling_height        14504 non-null  float64
 6   floors_total          23613 non-null  float64
 7   living_area           21796 non-null  float64
 8   floor                 23699 non-null  int64  
 9   is_apartment          2775 non-null   object 
 10  studio                23699 non-null  bool   
 11  open_plan             23699 non-null  bool   
 12  kitchen_area          21421 non-null  float64
 13  balcony               12180 non-null  float64
 14  locality_name         23650 non-null  object 
 15  airports_nearest      18157 non-null  float64
 16  cityCenters_nearest   18180 non-null  float64
 17  parks_around3000      18181 non-null  float64
 18  parks_nearest         8079 non-null   float64
 19  ponds_around3000      18181 non-null  float64
 20  ponds_nearest         9110 non-null   float64
 21  days_exposition       20518 non-null  float64
dtypes: bool(2), float64(14), int64(3), object(3)
memory usage: 3.7+ MB
```
Построим общую гистограмму для всех столбцов таблицы.
```
df.hist(figsize=(15, 20))
```
![cover](https://github.com/vs-gorgan/practicum.yandex/blob/main/03_spb_real_estate/hist.png)

### 2. Предобработка данных

**2.1 Удаление пропусков
