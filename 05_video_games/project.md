# Описание проекта

В нашем распоряжении исторические данные о продажах компьютерных игр по всему миру за период до 2016 г. На основе имеющейся информации необходимо дать прогноз на 2017 г. Требуется выполнить предобрабоку данных, исследовательский анализ и проверку гипотез.

В ходе проводимого исследования необходимо ответить на вопрос, какую рекламную стратегию выбрать. На какие платформы, с каким жанром, с каким рейтингом ESRB и в каком регионе увеличить рекламный бюджет? Стоит ли обращать внимание на отзывы или нет? 

### 1. Откроем файл. Изучим общую информацию

```
# импортируем библиотеку pandas
import pandas as pd

# и ещё полезные библиотеки
import numpy as np
from scipy import stats as st
from matplotlib import pyplot as plt
import seaborn as sns
```
```
# сохраним данные из файла games.csv в переменную data
data = pd.read_csv('/datasets/games.csv')
```
```
# изучим общую информацию
data.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 16715 entries, 0 to 16714
Data columns (total 11 columns):
 #   Column           Non-Null Count  Dtype  
---  ------           --------------  -----  
 0   Name             16713 non-null  object 
 1   Platform         16715 non-null  object 
 2   Year_of_Release  16446 non-null  float64
 3   Genre            16713 non-null  object 
 4   NA_sales         16715 non-null  float64
 5   EU_sales         16715 non-null  float64
 6   JP_sales         16715 non-null  float64
 7   Other_sales      16715 non-null  float64
 8   Critic_Score     8137 non-null   float64
 9   User_Score       10014 non-null  object 
 10  Rating           9949 non-null   object 
dtypes: float64(6), object(5)
memory usage: 1.4+ MB
```
```
# посмотрим начало датасета и название колонок
data.head()
```
```
|   |                     Name | Platform | Year_of_Release |        Genre | NA_sales | EU_sales | JP_sales | Other_sales | Critic_Score | User_Score | Rating |
|--:|-------------------------:|---------:|----------------:|-------------:|---------:|---------:|---------:|------------:|-------------:|-----------:|-------:|
| 0 | Wii Sports               | Wii      | 2006.0          | Sports       | 41.36    | 28.96    | 3.77     | 8.45        | 76.0         | 8          | E      |
| 1 | Super Mario Bros.        | NES      | 1985.0          | Platform     | 29.08    | 3.58     | 6.81     | 0.77        | NaN          | NaN        | NaN    |
| 2 | Mario Kart Wii           | Wii      | 2008.0          | Racing       | 15.68    | 12.76    | 3.79     | 3.29        | 82.0         | 8.3        | E      |
| 3 | Wii Sports Resort        | Wii      | 2009.0          | Sports       | 15.61    | 10.93    | 3.28     | 2.95        | 80.0         | 8          | E      |
| 4 | Pokemon Red/Pokemon Blue | GB       | 1996.0          | Role-Playing | 11.27    | 8.89     | 10.22    | 1.00        | NaN          | NaN        | NaN    |
```
```
# посмотрим наличие явных дубликатов
data.duplicated().sum()
```
### 2  Предобработка данных  
**2.1  Переименование столбцов**
```
# приведём названия стобцов к нижнему регистру
data.columns = data.columns.str.lower()
```
**2.2  Преобразуйте данные в нужные типы**  
Пойдём слева направо. К первым двум столбцам замечаний нет, тип `object` оставляем. `year_of_release` преобразуем в целое число.  
Если в столбце есть пропуски, метод `astype(int)` выдаст ошибку.
```
# создадим переменную skip, которая будет содержать пропуски

skip = pd.DataFrame(data.isna().sum()) \
    .rename(columns={0: 'before'})
skip
```
```
|                 | before |
|----------------:|-------:|
|       name      | 2      |
|     platform    | 0      |
| year_of_release | 269    |
|      genre      | 2      |
|     na_sales    | 0      |
|     eu_sales    | 0      |
|     jp_sales    | 0      |
|   other_sales   | 0      |
|   critic_score  | 8578   |
|    user_score   | 6701   |
|      rating     | 6766   |
```
269 пропусков в колонке год релиза.  
Убрать эти пропуски можно тремя способами:  
- удалить
- заполнить нулями
- с помощью wikipedia вручную заполнить все значения  

Для принятия решения посмотрим какие данные есть в колонке `year_of_release`
```
# выведем отсортированные по возрастанию уникальные значения
data.year_of_release.sort_values().unique()
```
```
array([1980., 1981., 1982., 1983., 1984., 1985., 1986., 1987., 1988.,
       1989., 1990., 1991., 1992., 1993., 1994., 1995., 1996., 1997.,
       1998., 1999., 2000., 2001., 2002., 2003., 2004., 2005., 2006.,
       2007., 2008., 2009., 2010., 2011., 2012., 2013., 2014., 2015.,
       2016.,   nan])
```
```
# число уникальных значений
len(data.year_of_release.sort_values().unique())
```
`38`
```
# число релизов игр каждый год
data.year_of_release.value_counts()
```
```
2008.0    1427
2009.0    1426
2010.0    1255
2007.0    1197
2011.0    1136
2006.0    1006
2005.0     939
2002.0     829
2003.0     775
2004.0     762
2012.0     653
2015.0     606
2014.0     581
2013.0     544
2016.0     502
2001.0     482
1998.0     379
2000.0     350
1999.0     338
1997.0     289
1996.0     263
1995.0     219
1994.0     121
1993.0      62
1981.0      46
1992.0      43
1991.0      41
1982.0      36
1986.0      21
1989.0      17
1983.0      17
1987.0      16
1990.0      16
1988.0      15
1984.0      14
1985.0      14
1980.0       9
Name: year_of_release, dtype: int64
```
```
# построим гистограмму
data.year_of_release.hist(bins = 37)
```
![изображение](https://user-images.githubusercontent.com/104757775/191006212-d2c97d96-9607-45ae-adfa-821cb489deb5.png)

В полученном датасете преимущественно игры с 2005 по 2011 г.  
**2.3  Заполним пропуски**  
Заполнить пропуски ни медианным ни средним значением нельзя. Попробуем год релиза связать с игровой платформой.
```
# создадим переменную game_count, которая содержит платформу и число вышедших игр 
game_count = data.groupby('platform', as_index=False) \
    .agg({'na_sales': 'count'}) \
    .rename(columns={'na_sales': 'games'})
```
```
# создадим переменную year, которая будет содержать данные о годах релиза игр для каждой игровой платформы
year = data.groupby('platform', as_index=False) \
    .agg({'year_of_release': ('min', 'max', 'mean', 'median')})

# избавимся от мультииндекса
year.columns = year.columns.get_level_values(0)

# вещественные числа преобразуем в целые
year = year.astype({'year_of_release':'int'},errors='ignore')
```
```
# переименуем колонки
new_col = ['platform', 'min', 'max', 'mean', 'median']
year.columns = new_col
```
```
# объеденим два источника 
year = year.merge(game_count)
year['period'] = year['max'] - year['min']
year.sort_values(by='period', ascending=False)
year.head()
```
```
|   | platform |  min |  max | mean | median | games | period |
|--:|---------:|-----:|-----:|-----:|-------:|------:|-------:|
| 0 | 2600     | 1980 | 1989 | 1982 | 1982   | 133   | 9      |
| 1 | 3DO      | 1994 | 1995 | 1994 | 1995   | 3     | 1      |
| 2 | 3DS      | 2011 | 2016 | 2013 | 2013   | 520   | 5      |
| 3 | DC       | 1998 | 2008 | 1999 | 2000   | 52    | 10     |
| 4 | DS       | 1985 | 2013 | 2008 | 2008   | 2151  | 28     |
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
```
```
