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

**2.1. Удаление пропусков**

Определим число прокусков для каждого столбца. Создадим переменную `skip`
```
skip = pd.DataFrame(df.isna().sum()) \
    .rename(columns={0: 'before'})
skip
```
|                      | before |
|---------------------:|-------:|
|     total_images     | 0      |
|      last_price      | 0      |
|      total_area      | 0      |
| first_day_exposition | 0      |
|         rooms        | 0      |
|    ceiling_height    | 9195   |
|     floors_total     | 86     |
|      living_area     | 1903   |
|         floor        | 0      |
|     is_apartment     | 20924  |
|        studio        | 0      |
|       open_plan      | 0      |
|     kitchen_area     | 2278   |
|        balcony       | 11519  |
|     locality_name    | 49     |
|   airports_nearest   | 5542   |
|  cityCenters_nearest | 5519   |
|   parks_around3000   | 5518   |
|     parks_nearest    | 15620  |
|   ponds_around3000   | 5518   |
|     ponds_nearest    | 14589  |
|    days_exposition   | 3181   |

Пропуски данных в:

`ceiling_height` — высота потолков (м)
Сгруппируем данные по этажам. Заполним медианным значением.

`floors_total` — всего этажей в доме

`living_area` — жилая площадь в квадратных метрах (м²)

`is_apartment` — апартаменты (булев тип)
Всего 23,6 тыс строк. Данные отсутствуют в 21 тыс строк.
Вкорее всего если ответ отрицательный, т.е. не аппартаменты - значение отсутствует.
Установм False для пропусков.

`kitchen_area` — площадь кухни в квадратных метрах (м²)

`balcony` — число балконов
При заполнении пропусков применим аналогочный с апартаментами метод.
Пропуски заменим на ноль.

`locality_name` — название населённого пункта

`airports_nearest` — расстояние до ближайшего аэропорта в метрах (м)

`cityCenters_nearest` — расстояние до центра города (м)

`parks_around3000` — число парков в радиусе 3 км

`parks_nearest` — расстояние до ближайшего парка (м)

`ponds_around3000` — число водоёмов в радиусе 3 км

`ponds_nearest` — расстояние до ближайшего водоёма (м)

`days_exposition` — сколько дней было размещено объявление (от публикации до снятия)

Начнём заполнение пропусков в колонке с количеством балконов. Скорее всего в квартирах с пропущенными значениями нет балконов
```
# заменим пропуски данных в колонке с числом балконов на ноль
df['balcony'] = df['balcony'].fillna(0)

# для столбца balcony установим тип float. 
df['balcony'] = pd.to_numeric(df['balcony'])
```
```
# число балконов всегда целое число. Установим тип int
df['balcony'] = df['balcony'].astype(int)
df['balcony'].value_counts()
```
```
0    15277
1     4195
2     3659
5      304
4      183
3       81
Name: balcony, dtype: int64
```
Колонка `ceiling_height` имеет 9195 пропусков. Установим для неё корректный тип данных. В данном случае нам нужны вещественные числа.
```
df['ceiling_height'] = pd.to_numeric(df['ceiling_height'], errors='coerce') # переведём значение столбца в тип float
ceiling_height = df['ceiling_height'] # извлечем столбец с высотой потолков в отдельный список
ceiling_height = ceiling_height.sort_values() # примените к нему метод сортировки
ceiling_height_unique = ceiling_height.unique() # выведем уникальные значения
ceiling_height_unique
```
```
array([  1.  ,   1.2 ,   1.75,   2.  ,   2.2 ,   2.25,   2.3 ,   2.34,
         2.4 ,   2.45,   2.46,   2.47,   2.48,   2.49,   2.5 ,   2.51,
         2.52,   2.53,   2.54,   2.55,   2.56,   2.57,   2.58,   2.59,
         2.6 ,   2.61,   2.62,   2.63,   2.64,   2.65,   2.66,   2.67,
         2.68,   2.69,   2.7 ,   2.71,   2.72,   2.73,   2.74,   2.75,
         2.76,   2.77,   2.78,   2.79,   2.8 ,   2.81,   2.82,   2.83,
         2.84,   2.85,   2.86,   2.87,   2.88,   2.89,   2.9 ,   2.91,
         2.92,   2.93,   2.94,   2.95,   2.96,   2.97,   2.98,   2.99,
         3.  ,   3.01,   3.02,   3.03,   3.04,   3.05,   3.06,   3.07,
         3.08,   3.09,   3.1 ,   3.11,   3.12,   3.13,   3.14,   3.15,
         3.16,   3.17,   3.18,   3.2 ,   3.21,   3.22,   3.23,   3.24,
         3.25,   3.26,   3.27,   3.28,   3.29,   3.3 ,   3.31,   3.32,
         3.33,   3.34,   3.35,   3.36,   3.37,   3.38,   3.39,   3.4 ,
         3.42,   3.43,   3.44,   3.45,   3.46,   3.47,   3.48,   3.49,
         3.5 ,   3.51,   3.52,   3.53,   3.54,   3.55,   3.56,   3.57,
         3.58,   3.59,   3.6 ,   3.62,   3.63,   3.65,   3.66,   3.67,
         3.68,   3.69,   3.7 ,   3.75,   3.76,   3.78,   3.8 ,   3.82,
         3.83,   3.84,   3.85,   3.86,   3.87,   3.88,   3.9 ,   3.93,
         3.95,   3.98,   4.  ,   4.06,   4.1 ,   4.14,   4.15,   4.19,
         4.2 ,   4.25,   4.3 ,   4.37,   4.4 ,   4.45,   4.5 ,   4.65,
         4.7 ,   4.8 ,   4.9 ,   5.  ,   5.2 ,   5.3 ,   5.5 ,   5.6 ,
         5.8 ,   6.  ,   8.  ,   8.3 ,  10.3 ,  14.  ,  20.  ,  22.6 ,
        24.  ,  25.  ,  26.  ,  27.  ,  27.5 ,  32.  , 100.  ,    nan])
```
Выделяются значения более 20. Вероятно, был установлен неверный формат.
```
# посмотрим информацию о недвижимости с высотой потолка более 20 м
df.query('ceiling_height > 20')
```
|       | total_images | last_price | total_area | first_day_exposition | rooms | ceiling_height | floors_total | living_area | floor | is_apartment | ... | kitchen_area | balcony |                   locality_name | airports_nearest | cityCenters_nearest | parks_around3000 | parks_nearest | ponds_around3000 | ponds_nearest | days_exposition |
|------:|-------------:|-----------:|-----------:|---------------------:|------:|---------------:|-------------:|------------:|------:|-------------:|----:|-------------:|--------:|--------------------------------:|-----------------:|--------------------:|-----------------:|--------------:|-----------------:|--------------:|----------------:|
|  355  | 17           | 3600000.0  | 55.2       | 2018-07-12T00:00:00  | 2     | 25.0           | 5.0          | 32.0        | 2     | False        | ... | NaN          | 2       | Гатчина                         | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | 259.0           |
|  3148 | 14           | 2900000.0  | 75.0       | 2018-11-12T00:00:00  | 3     | 32.0           | 3.0          | 53.0        | 2     | NaN          | ... | 8.0          | 0       | Волхов                          | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | NaN             |
|  4643 | 0            | 4300000.0  | 45.0       | 2018-02-01T00:00:00  | 2     | 25.0           | 9.0          | 30.0        | 2     | NaN          | ... | 7.0          | 1       | Санкт-Петербург                 | 12016.0          | 13256.0             | 1.0              | 658.0         | 1.0              | 331.0         | 181.0           |
|  4876 | 7            | 3000000.0  | 25.0       | 2017-09-27T00:00:00  | 0     | 27.0           | 25.0         | 17.0        | 17    | NaN          | ... | NaN          | 2       | посёлок Мурино                  | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | 28.0            |
|  5076 | 0            | 3850000.0  | 30.5       | 2018-10-03T00:00:00  | 1     | 24.0           | 5.0          | 19.5        | 1     | True         | ... | 5.5          | 0       | Санкт-Петербург                 | 29686.0          | 8389.0              | 3.0              | 397.0         | 1.0              | 578.0         | 7.0             |
|  5246 | 0            | 2500000.0  | 54.0       | 2017-10-13T00:00:00  | 2     | 27.0           | 5.0          | 30.0        | 3     | NaN          | ... | 9.0          | 2       | деревня Мины                    | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | 540.0           |
|  5669 | 4            | 4400000.0  | 50.0       | 2017-08-08T00:00:00  | 2     | 26.0           | 9.0          | 21.3        | 3     | NaN          | ... | 7.0          | 2       | Санкт-Петербург                 | 28981.0          | 10912.0             | 1.0              | 305.0         | 0.0              | NaN           | 267.0           |
|  5807 | 17           | 8150000.0  | 80.0       | 2019-01-09T00:00:00  | 2     | 27.0           | 36.0         | 41.0        | 13    | NaN          | ... | 12.0         | 5       | Санкт-Петербург                 | 18732.0          | 20444.0             | 0.0              | NaN           | 3.0              | 80.0          | 38.0            |
|  6246 | 6            | 3300000.0  | 44.4       | 2019-03-25T00:00:00  | 2     | 25.0           | 5.0          | 31.3        | 5     | NaN          | ... | 5.7          | 0       | Кронштадт                       | 68923.0          | 50649.0             | 1.0              | 417.0         | 2.0              | 73.0          | NaN             |
|  9379 | 5            | 3950000.0  | 42.0       | 2017-03-26T00:00:00  | 3     | 25.0           | 5.0          | 30.0        | 2     | NaN          | ... | 5.2          | 0       | Санкт-Петербург                 | 11647.0          | 13581.0             | 0.0              | NaN           | 0.0              | NaN           | NaN             |
| 10773 | 8            | 3800000.0  | 58.0       | 2017-10-13T00:00:00  | 2     | 27.0           | 10.0         | 30.1        | 3     | False        | ... | 8.1          | 2       | посёлок Мурино                  | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | 71.0            |
| 11285 | 0            | 1950000.0  | 37.0       | 2019-03-20T00:00:00  | 1     | 25.0           | 5.0          | 17.0        | 4     | False        | ... | 9.0          | 2       | Луга                            | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | 18.0            |
| 14382 | 9            | 1700000.0  | 35.0       | 2015-12-04T00:00:00  | 1     | 25.0           | 5.0          | 20.0        | 2     | False        | ... | 8.0          | 1       | поселок Новый Свет              | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | 206.0           |
| 17857 | 1            | 3900000.0  | 56.0       | 2017-12-22T00:00:00  | 3     | 27.0           | 5.0          | 33.0        | 4     | False        | ... | NaN          | 0       | Санкт-Петербург                 | 41030.0          | 15543.0             | 0.0              | NaN           | 0.0              | NaN           | 73.0            |
| 18545 | 6            | 3750000.0  | 43.0       | 2019-03-18T00:00:00  | 2     | 25.0           | 5.0          | 29.0        | 3     | False        | ... | NaN          | 0       | Санкт-Петербург                 | 27054.0          | 8033.0              | 1.0              | 540.0         | 0.0              | NaN           | 12.0            |
| 20478 | 11           | 8000000.0  | 45.0       | 2017-07-18T00:00:00  | 1     | 27.0           | 4.0          | 22.0        | 2     | NaN          | ... | 10.0         | 1       | Санкт-Петербург                 | 18975.0          | 3246.0              | 0.0              | NaN           | 3.0              | 449.0         | 429.0           |
| 20507 | 12           | 5950000.0  | 60.0       | 2018-02-19T00:00:00  | 2     | 22.6           | 14.0         | 35.0        | 11    | NaN          | ... | 13.0         | 1       | Санкт-Петербург                 | 27028.0          | 12570.0             | 0.0              | NaN           | 0.0              | NaN           | 40.0            |
| 21377 | 19           | 4900000.0  | 42.0       | 2017-04-18T00:00:00  | 1     | 27.5           | 24.0         | 37.7        | 19    | False        | ... | 11.0         | 2       | Санкт-Петербург                 | 42742.0          | 9760.0              | 0.0              | NaN           | 0.0              | NaN           | 61.0            |
| 21824 | 20           | 2450000.0  | 44.0       | 2019-02-12T00:00:00  | 2     | 27.0           | 2.0          | 38.0        | 2     | False        | ... | 8.6          | 2       | городской поселок Большая Ижора | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | NaN             |
| 22336 | 19           | 9999000.0  | 92.4       | 2019-04-05T00:00:00  | 2     | 32.0           | 6.0          | 55.5        | 5     | False        | ... | 16.5         | 4       | Санкт-Петербург                 | 18838.0          | 3506.0              | 0.0              | NaN           | 3.0              | 511.0         | NaN             |
| 22869 | 0            | 15000000.0 | 25.0       | 2018-07-25T00:00:00  | 1     | 100.0          | 5.0          | 14.0        | 5     | True         | ... | 11.0         | 5       | Санкт-Петербург                 | 34963.0          | 8283.0              | 1.0              | 223.0         | 3.0              | 30.0          | 19.0            |
| 22938 | 14           | 4000000.0  | 98.0       | 2018-03-15T00:00:00  | 4     | 27.0           | 2.0          | 73.0        | 2     | True         | ... | 9.0          | 1       | деревня Нижняя                  | NaN              | NaN                 | NaN              | NaN           | NaN              | NaN           | 27.0            |

Одноэтажных зданий в списке нет. Многоэтажные здания с высотой потолка каждого этажа более 20 м не могут быть. Есть одна строка с высотой потолка 100 м. Тут явно ошибка, удалим её.

```
df = df[df.ceiling_height != 100]
```
```
# для значений более 20 м уменьшим десятичный разряд
df['ceiling_height'].where(~(df.ceiling_height > 20), other=ceiling_height/10, inplace=True)
```
```
# заполним пропуски медианным значением выполнив группировку по количеству этажей
df['ceiling_height'] = df['ceiling_height'] \
    .fillna(df.groupby('floors_total')['ceiling_height'] \
    .transform('median'))
```
```
# посмотрим количество объявлений с высотой потолка более 4 м
df.query('ceiling_height > 4').count()
```
```
total_images            53
last_price              53
total_area              53
first_day_exposition    53
rooms                   53
ceiling_height          53
floors_total            53
living_area             47
floor                   53
is_apartment             9
studio                  53
open_plan               53
kitchen_area            44
balcony                 53
locality_name           53
airports_nearest        45
cityCenters_nearest     48
parks_around3000        48
parks_nearest           31
ponds_around3000        48
ponds_nearest           31
days_exposition         44
dtype: int64
```
```
# 53 шт. Значения редкие, удалим эти строки
df = df[df.ceiling_height < 4]
```
```
# Исключим объявления с высотой потолков более 10.3 м
df = df[df.ceiling_height < 10.3]
```
```
df['ceiling_height'].isna().sum()
0
```
Продолжим заполняит пропуски. Посмотрим их количество в остальных столбцах
```
df.isna().sum()
```
```
total_images                0
last_price                  0
total_area                  0
first_day_exposition        0
rooms                       0
ceiling_height              0
floors_total                9
living_area              1865
floor                       0
is_apartment            20771
studio                      0
open_plan                   0
kitchen_area             2224
balcony                     0
locality_name              47
airports_nearest         5521
cityCenters_nearest      5501
parks_around3000         5500
parks_nearest           15539
ponds_around3000         5500
ponds_nearest           14523
days_exposition          3153
dtype: int64
```
Число пропусков в количественных величинах, связанных с площадью:

`total_area` - 0

`living_area` - 1865

`kitchen_area` - 2224

1. вычтем из общей площади - жилую площадь
2. вычтем из общей площади - площадь кухни
3. для оставшихся пропусков применим медиану
```
df['living_area'] = df['living_area'].fillna(df['total_area'] - df['kitchen_area'])
df['kitchen_area'] = df['kitchen_area'].fillna(df['total_area'] - df['living_area'])
```
|              | было | стало |
|--------------|------|-------|
| total_area   | 0    | 0     |
| living_area  | 1865 | 1427  |
| kitchen_area | 2224 | 1427  |
```
df.total_area.describe()
```
```
count    23528.000000
mean        60.075257
std         34.863304
min         12.000000
25%         40.000000
50%         52.000000
75%         69.500000
max        900.000000
Name: total_area, dtype: float64
```
Применим функцию, которая создаст категории общей площади в разбивке по квантилям.

- до 40 м2
- до 52 м2
- до 69 м2
- более 70 м2
```
# функция по разбивке общей площади ка натегории
def total_area_quantile(total_area):
    if total_area < 40:
        return 1
    elif total_area < 52:
        return 2
    elif total_area < 69:
        return 3
    return 4
```
```
# заполним новый столбец категориями общей площади
df['total_area_quantile'] = df['total_area'].apply(total_area_quantile)
```
```
# заполним пропуски жилой площади медианными значениями
df['living_area'] = df['living_area'].fillna(df.groupby('total_area_quantile')['living_area'].transform('median'))
```
```
# заполним пропуски площади кухни путём разницы общей площади имедианного значения жилой площади
df['kitchen_area'] = df['kitchen_area'].fillna(df['total_area'] - df['living_area'])
```
```
# заменим пропуски в характеристике апартаменты на Falce
df['is_apartment'] = df['is_apartment'].fillna('Falce')
```
```
# заменим пропуски в количестве этажей на unknown
df['floors_total'] = df['floors_total'].fillna(df['floor'])
```
Ранее была создана переменная `skip`. Добавим в неё колонку с информацией после заполнения пропусков.
```
skip_after = pd.DataFrame(df.isna().sum()) \
    .rename(columns={0: 'after'})
skip['after'] = skip_after['after']
skip
```
```
before 	after
total_images 	0 	0
last_price 	0 	0
total_area 	0 	0
first_day_exposition 	0 	0
rooms 	0 	0
ceiling_height 	9195 	0
floors_total 	86 	0
living_area 	1903 	0
floor 	0 	0
is_apartment 	20924 	0
studio 	0 	0
open_plan 	0 	0
kitchen_area 	2278 	0
balcony 	11519 	0
locality_name 	49 	47
airports_nearest 	5542 	5521
cityCenters_nearest 	5519 	5501
parks_around3000 	5518 	5500
parks_nearest 	15620 	15539
ponds_around3000 	5518 	5500
ponds_nearest 	14589 	14523
days_exposition 	3181 	3153
```
```
```
```
```
```
```
```
```
```
```
```
```
```
```
```
