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
** 2.1  Переименование столбцов**

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
