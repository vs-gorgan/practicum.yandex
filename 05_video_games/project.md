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
|   |                     Name | Platform | Year_of_Release |        Genre | NA_sales | EU_sales | JP_sales | Other_sales | Critic_Score | User_Score | Rating |
|--:|-------------------------:|---------:|----------------:|-------------:|---------:|---------:|---------:|------------:|-------------:|-----------:|-------:|
| 0 | Wii Sports               | Wii      | 2006.0          | Sports       | 41.36    | 28.96    | 3.77     | 8.45        | 76.0         | 8          | E      |
| 1 | Super Mario Bros.        | NES      | 1985.0          | Platform     | 29.08    | 3.58     | 6.81     | 0.77        | NaN          | NaN        | NaN    |
| 2 | Mario Kart Wii           | Wii      | 2008.0          | Racing       | 15.68    | 12.76    | 3.79     | 3.29        | 82.0         | 8.3        | E      |
| 3 | Wii Sports Resort        | Wii      | 2009.0          | Sports       | 15.61    | 10.93    | 3.28     | 2.95        | 80.0         | 8          | E      |
| 4 | Pokemon Red/Pokemon Blue | GB       | 1996.0          | Role-Playing | 11.27    | 8.89     | 10.22    | 1.00        | NaN          | NaN        | NaN    |
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
|   | platform |  min |  max | mean | median | games | period |
|--:|---------:|-----:|-----:|-----:|-------:|------:|-------:|
| 0 | 2600     | 1980 | 1989 | 1982 | 1982   | 133   | 9      |
| 1 | 3DO      | 1994 | 1995 | 1994 | 1995   | 3     | 1      |
| 2 | 3DS      | 2011 | 2016 | 2013 | 2013   | 520   | 5      |
| 3 | DC       | 1998 | 2008 | 1999 | 2000   | 52    | 10     |
| 4 | DS       | 1985 | 2013 | 2008 | 2008   | 2151  | 28     |
```
# посчитаем % пропусков в годе релиза
print((data.year_of_release.isna().sum()/data.year_of_release.shape *100).round(2), '%')
```
`[1.61] %`
```
# исключим строки со значением NaN в годе релиза
data = data[data['year_of_release'].notna()]
```
```
# теперь мы готовы сменить формат flat на int
data['year_of_release'] = data['year_of_release'].astype('int')
```
Изучим сведения об оценках пользователей
```
# посмотрим уникальные оценки
data.user_score.sort_values().unique()
```
```
array(['0', '0.2', '0.3', '0.5', '0.6', '0.7', '0.9', '1', '1.1', '1.2',
       '1.3', '1.4', '1.5', '1.6', '1.7', '1.8', '1.9', '2', '2.1', '2.2',
       '2.3', '2.4', '2.5', '2.6', '2.7', '2.8', '2.9', '3', '3.1', '3.2',
       '3.3', '3.4', '3.5', '3.6', '3.7', '3.8', '3.9', '4', '4.1', '4.2',
       '4.3', '4.4', '4.5', '4.6', '4.7', '4.8', '4.9', '5', '5.1', '5.2',
       '5.3', '5.4', '5.5', '5.6', '5.7', '5.8', '5.9', '6', '6.1', '6.2',
       '6.3', '6.4', '6.5', '6.6', '6.7', '6.8', '6.9', '7', '7.1', '7.2',
       '7.3', '7.4', '7.5', '7.6', '7.7', '7.8', '7.9', '8', '8.1', '8.2',
       '8.3', '8.4', '8.5', '8.6', '8.7', '8.8', '8.9', '9', '9.1', '9.2',
       '9.3', '9.4', '9.5', '9.6', '9.7', 'tbd', nan], dtype=object)
```
```
# посмотрим как распределены оценки
data.user_score.value_counts()
```
```
tbd    2376
7.8     322
8       285
8.2     276
8.3     252
       ... 
1         2
2.3       2
1.3       2
9.7       1
0         1
Name: user_score, Length: 96, dtype: int64
```
Помимо цифровых оценок, встречается tbd, т.е. рейтинг неизвестен, будет объявлен позже. Количество таких игр больше всего. У столбца `user_score` установлен тип `object`. Если изменим тип на float, потеряем ячейки с рейтингом tbd.

Посмотрим у каких игр рейтинг tbd.
```
data.query('user_score == "tbd"')
```
|       |                           name | platform | year_of_release |      genre | na_sales | eu_sales | jp_sales | other_sales | critic_score | user_score | rating |
|------:|-------------------------------:|---------:|----------------:|-----------:|---------:|---------:|---------:|------------:|-------------:|-----------:|-------:|
|  119  | Zumba Fitness                  | Wii      | 2010            | Sports     | 3.45     | 2.59     | 0.0      | 0.66        | NaN          | tbd        | E      |
|  301  | Namco Museum: 50th Anniversary | PS2      | 2005            | Misc       | 2.08     | 1.35     | 0.0      | 0.54        | 61.0         | tbd        | E10+   |
|  520  | Zumba Fitness 2                | Wii      | 2011            | Sports     | 1.51     | 1.03     | 0.0      | 0.27        | NaN          | tbd        | T      |
|  645  | uDraw Studio                   | Wii      | 2010            | Misc       | 1.65     | 0.57     | 0.0      | 0.20        | 71.0         | tbd        | E      |
|  718  | Just Dance Kids                | Wii      | 2010            | Misc       | 1.52     | 0.54     | 0.0      | 0.18        | NaN          | tbd        | E      |
|  ...  | ...                            | ...      | ...             | ...        | ...      | ...      | ...      | ...         | ...          | ...        | ...    |
| 16695 | Planet Monsters                | GBA      | 2001            | Action     | 0.01     | 0.00     | 0.0      | 0.00        | 67.0         | tbd        | E      |
| 16697 | Bust-A-Move 3000               | GC       | 2003            | Puzzle     | 0.01     | 0.00     | 0.0      | 0.00        | 53.0         | tbd        | E      |
| 16698 | Mega Brain Boost               | DS       | 2008            | Puzzle     | 0.01     | 0.00     | 0.0      | 0.00        | 48.0         | tbd        | E      |
| 16704 | Plushees                       | DS       | 2008            | Simulation | 0.01     | 0.00     | 0.0      | 0.00        | NaN          | tbd        | E      |
| 16706 | Men in Black II: Alien Escape  | GC       | 2003            | Shooter    | 0.01     | 0.00     | 0.0      | 0.00        | NaN          | tbd        | T      |
```
data.query('user_score == "tbd"').groupby('jp_sales')['jp_sales'].count()
```
```
jp_sales
0.00    2294
0.01      16
0.02      11
0.03      11
0.04       5
0.05       7
0.06       6
0.08       2
0.09       3
0.10       1
0.11       2
0.12       2
0.13       2
0.14       1
0.15       1
0.16       2
0.17       1
0.18       2
0.20       1
0.25       2
0.26       1
0.31       1
0.47       1
0.86       1
Name: jp_sales, dtype: int64
```
У игр с пользовательской оценкой tbd почти нет продаж в Японии
```
# изменим тип столбца оценок пользователей на float
data['user_score'] = pd.to_numeric(data['user_score'], errors='coerce')
```
```
# теперь можно строить гистограмму
data.user_score.hist(bins=10)
```
![изображение](https://user-images.githubusercontent.com/104757775/191007382-f20a030a-79a3-4a6a-9cbd-240d16c8aa2a.png)

```
# столбец с оценкой критиков имеет тип float. Сразу построим гистограмму
data.critic_score.hist(bins=10)
```
![изображение](https://user-images.githubusercontent.com/104757775/191007534-71e2a0a8-be72-4e40-8c1e-c34d8ff3aec9.png)

Полученные гистограммы похожи. Методом describe оценим распределение оценок.
```
data.critic_score.describe()
```
```
count    7983.000000
mean       68.994363
std        13.920060
min        13.000000
25%        60.000000
50%        71.000000
75%        79.000000
max        98.000000
Name: critic_score, dtype: float64
```
```
data.user_score.describe()
```
```
count    7463.000000
mean        7.126330
std         1.499447
min         0.000000
25%         6.400000
50%         7.500000
75%         8.200000
max         9.700000
Name: user_score, dtype: float64
```
Критики используют 100 бальную систему оценки, пользователи - 10-ти. Видно, что пользовательские оценки немого выше оценок критиков. 
```
# удалим два пропуска в колонке Имя.
# вместо dropna() оставим те строки, в которых нет NaN
data = data[data['name'].notna()]
```
Остаётся 6766 пропусков в колонке rating. Имеющихся данных не достаточно, чтоб на основании них заполнить эти пропуски. Поставим заглушке для пропусков.

```
# установим заглушку для пропусков в рейтинге
data.rating = data.rating.fillna('unknown')
```
Ранее была создана переменная skip. Добавим в неё колонку с информацией после заполнения пропусков.
```
# через промежуточную переменную skip_after получим столбец after
skip_after = pd.DataFrame(data.isna().sum()) \
    .rename(columns={0: 'after'})
skip['after'] = skip_after['after']
skip
```
|                 | before | after |
|----------------:|-------:|------:|
|       name      | 2      | 0     |
|     platform    | 0      | 0     |
| year_of_release | 269    | 0     |
|      genre      | 2      | 0     |
|     na_sales    | 0      | 0     |
|     eu_sales    | 0      | 0     |
|     jp_sales    | 0      | 0     |
|   other_sales   | 0      | 0     |
|   critic_score  | 8578   | 8461  |
|    user_score   | 6701   | 8981  |
|      rating     | 6766   | 0     |

**2.4  Суммарные продажи во всех регионах**

Создадим колонку `total_sales`, в которую поместим сумму продаж по всем регионам
```
data['total_sales'] = data[['na_sales','eu_sales','jp_sales', 'other_sales']].sum(axis = 1)
```
```
# Посмотрим, что у нас осталось
temp = data.copy() 
list_c = ['name', 'platform', 'year_of_release', 'genre', 'critic_score', 'user_score', 'rating']
print(temp.info())
for col_l in list_c:
  print('-'* 25)
  print(col_l, temp[col_l].unique())
  print(col_l,': кол-во NaN',temp[col_l].isna().sum(),
        ', процент NaN', round(temp[col_l].isna().sum()/len(temp)*100, 2),'%')
```
```
<class 'pandas.core.frame.DataFrame'>
Int64Index: 16444 entries, 0 to 16714
Data columns (total 12 columns):
 #   Column           Non-Null Count  Dtype  
---  ------           --------------  -----  
 0   name             16444 non-null  object 
 1   platform         16444 non-null  object 
 2   year_of_release  16444 non-null  int64  
 3   genre            16444 non-null  object 
 4   na_sales         16444 non-null  float64
 5   eu_sales         16444 non-null  float64
 6   jp_sales         16444 non-null  float64
 7   other_sales      16444 non-null  float64
 8   critic_score     7983 non-null   float64
 9   user_score       7463 non-null   float64
 10  rating           16444 non-null  object 
 11  total_sales      16444 non-null  float64
dtypes: float64(7), int64(1), object(4)
memory usage: 1.6+ MB
None
-------------------------
name ['Wii Sports' 'Super Mario Bros.' 'Mario Kart Wii' ...
 'Woody Woodpecker in Crazy Castle 5' 'LMA Manager 2007'
 'Haitaka no Psychedelica']
name : кол-во NaN 0 , процент NaN 0.0 %
-------------------------
platform ['Wii' 'NES' 'GB' 'DS' 'X360' 'PS3' 'PS2' 'SNES' 'GBA' 'PS4' '3DS' 'N64'
 'PS' 'XB' 'PC' '2600' 'PSP' 'XOne' 'WiiU' 'GC' 'GEN' 'DC' 'PSV' 'SAT'
 'SCD' 'WS' 'NG' 'TG16' '3DO' 'GG' 'PCFX']
platform : кол-во NaN 0 , процент NaN 0.0 %
-------------------------
year_of_release [2006 1985 2008 2009 1996 1989 1984 2005 1999 2007 2010 2013 2004 1990
 1988 2002 2001 2011 1998 2015 2012 2014 1992 1997 1993 1994 1982 2016
 2003 1986 2000 1995 1991 1981 1987 1980 1983]
year_of_release : кол-во NaN 0 , процент NaN 0.0 %
-------------------------
genre ['Sports' 'Platform' 'Racing' 'Role-Playing' 'Puzzle' 'Misc' 'Shooter'
 'Simulation' 'Action' 'Fighting' 'Adventure' 'Strategy']
genre : кол-во NaN 0 , процент NaN 0.0 %
-------------------------
critic_score [76. nan 82. 80. 89. 58. 87. 91. 61. 97. 95. 77. 88. 83. 94. 93. 85. 86.
 98. 96. 90. 84. 73. 74. 78. 92. 71. 72. 68. 62. 49. 67. 81. 66. 56. 79.
 70. 59. 64. 75. 60. 63. 69. 50. 25. 42. 44. 55. 48. 57. 29. 47. 65. 54.
 20. 53. 37. 38. 33. 52. 30. 32. 43. 45. 51. 40. 46. 39. 34. 41. 36. 31.
 27. 35. 26. 19. 28. 23. 24. 21. 17. 13.]
critic_score : кол-во NaN 8461 , процент NaN 51.45 %
-------------------------
user_score [8.  nan 8.3 8.5 6.6 8.4 8.6 7.7 6.3 7.4 8.2 9.  7.9 8.1 8.7 7.1 3.4 5.3
 4.8 3.2 8.9 6.4 7.8 7.5 2.6 7.2 9.2 7.  7.3 4.3 7.6 5.7 5.  9.1 6.5 8.8
 6.9 9.4 6.8 6.1 6.7 5.4 4.  4.9 4.5 9.3 6.2 4.2 6.  3.7 4.1 5.8 5.6 5.5
 4.4 4.6 5.9 3.9 3.1 2.9 5.2 3.3 4.7 5.1 3.5 2.5 1.9 3.  2.7 2.2 2.  9.5
 2.1 3.6 2.8 1.8 3.8 0.  1.6 9.6 2.4 1.7 1.1 0.3 1.5 0.7 1.2 2.3 0.5 1.3
 0.2 0.6 1.4 0.9 1.  9.7]
user_score : кол-во NaN 8981 , процент NaN 54.62 %
-------------------------
rating ['E' 'unknown' 'M' 'T' 'E10+' 'K-A' 'AO' 'EC' 'RP']
rating : кол-во NaN 0 , процент NaN 0.0 %
```
### 3  Исследовательский анализ данных  
**3.1  Посмотрите, сколько игр выпускалось в разные годы. Важны ли данные за все периоды?**
```
# число релизов игр за каждый год
# ранее мы выводили этот список, но теперь в нём заполнены пропуски
data.year_of_release.value_counts()
```
```
2008    1427
2009    1426
2010    1255
2007    1197
2011    1136
2006    1006
2005     939
2002     829
2003     775
2004     762
2012     653
2015     606
2014     581
2013     544
2016     502
2001     482
1998     379
2000     350
1999     338
1997     289
1996     263
1995     219
1994     121
1993      60
1981      46
1992      43
1991      41
1982      36
1986      21
1983      17
1989      17
1990      16
1987      16
1988      15
1985      14
1984      14
1980       9
Name: year_of_release, dtype: int64
```
```
# построим гистограмму
data.year_of_release.hist(ec="white", grid=False).set_title('Число выпускаемых игр');
```
![изображение](https://user-images.githubusercontent.com/104757775/191008451-8cf40424-6fc7-4787-b1ca-9c65cb1e3790.png)

Исследуемый датафрейм демонстрирует ежегодный рост числа выпускаемых игр. Снижение количества игр после 2011 г. скорее всего связано с тем, что в представленном файле содержатся не все данные об играх.  

Согласно <code>[статьи](https://www.igromania.ru/news/48878/Polzovateli_sostavlyayut_spisok_vseh_kogda-libo_vypuschennyh_igr.html?ysclid=l7dnc0vzi1157470999)</code> по состоянию на 2014 г. вышло 43 811 игр. Нам для анализа предоставлено 16 715 игр.

Полагаю, что для анализа интересен период с 1994 г. Ранее игр выпускалось совсем мало.

**3.2  Посмотрите, как менялись продажи по платформам. Выберите платформы с наибольшими суммарными продажами и постройте распределение по годам. За какой характерный срок появляются новые и исчезают старые платформы?**

Для выполнения задачи будем использовать второй датафрейм `year`
```
# создадим переменную, которя будет содержать число проданных копий для каждой платформы
platform = data.groupby('platform')['total_sales'].sum().reset_index()
```
```
# добавим у year колонку с числом проданных копий
year = year.merge(platform)
```
```
# посмотрим топ 5 по сумме проданных копий, в млн
year.sort_values(by='total_sales', ascending=False).head()
```
|    | platform |  min |  max | mean | median | games | period | total_sales |
|---:|---------:|-----:|-----:|-----:|-------:|------:|-------:|------------:|
| 16 | PS2      | 2000 | 2011 | 2004 | 2005   | 2161  | 11     | 1233.56     |
| 28 | X360     | 2005 | 2016 | 2009 | 2010   | 1262  | 11     | 961.24      |
| 17 | PS3      | 2006 | 2016 | 2010 | 2011   | 1331  | 10     | 931.34      |
| 26 | Wii      | 2006 | 2016 | 2008 | 2009   | 1320  | 10     | 891.18      |
|  4 | DS       | 1985 | 2013 | 2008 | 2008   | 2151  | 28     | 802.78      |
```
# построим график продаж для PS2 по годам
data.query('platform == "PS2"') \
    .pivot_table(index = 'year_of_release', values = 'total_sales', aggfunc = 'sum') \
    .plot.bar(y='total_sales', grid=True);
plt.title('Платформа PS2')
```
![изображение](https://user-images.githubusercontent.com/104757775/191008759-7cfd37ce-e2a1-490f-9475-8c66f0a1759c.png)

```
# построим график продаж для X360 по годам
data.query('platform == "X360"') \
    .pivot_table(index = 'year_of_release', values = 'total_sales', aggfunc = 'sum') \
    .plot.bar(y='total_sales', grid=True);
plt.title('Платформа X360')
```
![изображение](https://user-images.githubusercontent.com/104757775/191008815-16c9be39-b851-4125-b674-86d143983f87.png)

```
# построим график продаж для PS3 по годам
data.query('platform == "PS3"') \
    .pivot_table(index = 'year_of_release', values = 'total_sales', aggfunc = 'sum') \
    .plot.bar(y='total_sales', grid=True);
plt.title('Платформа PS3')
```
![изображение](https://user-images.githubusercontent.com/104757775/191008864-219dd16e-e2c3-48ef-8fdf-e3573fed53b3.png)

```
# построим график продаж для Wii по годам
data.query('platform == "Wii"') \
    .pivot_table(index = 'year_of_release', values = 'total_sales', aggfunc = 'sum') \
    .plot.bar(y='total_sales', grid=True);
plt.title('Платформа Wii')
```
![изображение](https://user-images.githubusercontent.com/104757775/191008909-bb7be9a1-c112-4afd-9677-3c2c687a7175.png)

```
# построим график продаж для DS по годам
data.query('platform == "DS"') \
    .pivot_table(index = 'year_of_release', values = 'total_sales', aggfunc = 'sum') \
    .plot.bar(y='total_sales', grid=True);
plt.title('Платформа DS')
```
![изображение](https://user-images.githubusercontent.com/104757775/191008964-64d7075e-100d-4334-b3b1-3bc641ac872b.png)

```
# построим график продаж для топ 5 платформ по годам
data.query('platform == ["PS2", "X360", "PS3", "Wii", "DS"]') \
    .pivot_table(index = 'year_of_release', columns ='platform', values = 'total_sales', aggfunc = 'sum') \
    .plot(grid=True, figsize=(15, 5));
plt.xlim([1999, 2016])
plt.xticks([2000, 2005, 2010, 2015])
plt.title('Динамика продаж')
```
![изображение](https://user-images.githubusercontent.com/104757775/191009036-d2b4f5c2-cac4-4a05-b108-b49024f8c6c6.png)

График продаж у пяти самых популярных платформ показал, что у всех разная динамика роста и спада популярности.

Однако, можно заметить общий период жизни платформы ~10 лет.

**3.3  Возьмите данные за соответствующий актуальный период. Актуальный период определите самостоятельно в результате исследования предыдущих вопросов. Основной фактор — эти данные помогут построить прогноз на 2017 год.**  
Чтобы построить прогноз на 2017 год, создадим переменную содержащею данные за актуальный период. Актуальным будем считать данные за последние 3 года.
```
data_actual = data.query('year_of_release > 2013')
```
```
# построим график продаж
data_actual \
    .pivot_table(index = 'year_of_release', columns ='platform', values = 'total_sales', aggfunc = 'sum') \
    .plot(grid=True, figsize=(5, 10));
plt.xlim([2014, 2016])
plt.xticks([2014, 2015, 2016])
plt.title('Динамика продаж')
```
![изображение](https://user-images.githubusercontent.com/104757775/191010176-a8d9f0c9-a080-4cec-872d-f1f640c6bf42.png)

Почти все платформы показывают снижение объёма продаж. Или у нас не полные данные за 2016 год или все ждут выход **Xbox Series S** и **PlayStation 5**.

Платформа `PC` стабильна за последние 3 года, значит в 2017 году покажет суммарный объём продаж ~10 млн копий.

Поддержка `PlayStation Vita` прекратится в 2019 году, вероятно, суммарный объём продаж будет будет схожий с `PC` ~10 млн копий.

Платформа `WiiU` последние 2 года показывает подение продаж. В связи с тем, что в январе 2017 года прекратится поддержка, полагаю, что объём продаж составит не более 2 млн копий.

**3.4  Не учитывайте в работе данные за предыдущие годы.**

Создада переменная `data_actual`. Далее будеи=м работать с ней.  

**3.5  Какие платформы лидируют по продажам, растут или падают? Выберите несколько потенциально прибыльных платформ.**

Предыдущий график показал, что объём продаж у `PS4` и `XOne` упал в 2016 г. Однако, они остаются лидерами.  
Потенциально прибыльными считаю те платформы, которые сделали наибольшее число продаж менее чем за 3 года
```
# выведем топ 3 таких платформ
year.query('period < 4').sort_values(by='total_sales', ascending=False).head(3)
```
|    | platform |  min |  max | mean | median | games | period | total_sales |
|---:|---------:|-----:|-----:|-----:|-------:|------:|-------:|------------:|
| 18 | PS4      | 2013 | 2016 | 2015 | 2015   | 392   | 3      | 314.14      |
| 30 | XOne     | 2013 | 2016 | 2014 | 2015   | 247   | 3      | 159.32      |
| 22 | SCD      | 1993 | 1994 | 1993 | 1994   | 6     | 1      | 1.86        |

Лучшие продажи у `PS4` и `XOne`.

**3.6  Постройте график «ящик с усами» по глобальным продажам игр в разбивке по платформам. Опишите результат.**  

Для построения ящика с усами вызовим boxplot. По оси Y - число проданных копий, по X - игровые платформы.
```
data_actual.boxplot('total_sales', by='platform', figsize=(12, 6));
```
![изображение](https://user-images.githubusercontent.com/104757775/191010817-3faa00d0-8df9-47c8-89ae-e075ea09d56b.png)

```
# установим лимит о оси Y
data_actual.boxplot('total_sales', by='platform', figsize=(12, 6)).set_ylim(0, 1);
```
![изображение](https://user-images.githubusercontent.com/104757775/191010875-e2796b63-e3b7-49a5-9818-089cd408dc06.png)

У игровых платформ выпущено различное число игр, однако, видно, что медианное значение числа копий не превышает 0.5 млн

Если рассматривать выбросы в диаграмме размаха, можно увидеть платформы с числом продаж более 10 млн копий игр: `3DS`, `PS3`, `PS4`, `X360`.

**3.7  Посмотрите, как влияют на продажи внутри одной популярной платформы отзывы пользователей и критиков. Постройте диаграмму рассеяния и посчитайте корреляцию между отзывами и продажами. Сформулируйте выводы.**

Определим популярную платформу
```
# наибольшее число игр
year.query('min > 2011').sort_values(by = 'games').tail(1)
```
|    | platform |  min |  max | mean | median | games | period | total_sales |
|---:|---------:|-----:|-----:|-----:|-------:|------:|-------:|------------:|
| 18 | PS4      | 2013 | 2016 | 2015 | 2015   | 392   | 3      | 314.14      |
```
# наибольшее число продаж
year.query('min > 2011').sort_values(by = 'total_sales').tail(1)
```
|    | platform |  min |  max | mean | median | games | period | total_sales |
|---:|---------:|-----:|-----:|-----:|-------:|------:|-------:|------------:|
| 18 | PS4      | 2013 | 2016 | 2015 | 2015   | 392   | 3      | 314.14      |

Определено однозначно, **PS4** - самая популярная платформа.
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

