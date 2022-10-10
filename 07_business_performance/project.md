# Описание проекта

Компания, занимающаяся разработкой развлекательного приложения Procrastinate Pro+ последние несколько месяцев компания терпит убытки, несмотря на огромные вложения в рекламу.

**Цель:**  провести маркетинговый анализ, разобраться в причинах и помочь компании выйти в плюс.

В нашем распоряжении данные о пользователях, привлечённых с 1 мая по 27 октября 2019 года:

- лог сервера с данными об их посещениях,
- выгрузка их покупок за этот период,
- рекламные расходы.

**План работы** изучить:

- откуда приходят пользователи и какими устройствами они пользуются,
- сколько стоит привлечение пользователей из различных рекламных каналов;
- сколько денег приносит каждый клиент,
- когда расходы на привлечение клиента окупаются,
- какие факторы мешают привлечению клиентов.

### 1. Загрузите данные и подготовьте их к анализу

Загрузите данные о визитах, заказах и рекламных расходах из CSV-файлов в переменные.

**Пути к файлам**

- визиты: `/datasets/visits_info_short.csv`. [Скачать датасет](https://code.s3.yandex.net/datasets/visits_info_short.csv);
- заказы: `/datasets/orders_info_short.csv`. [Скачать датасет](https://code.s3.yandex.net/datasets/orders_info_short.csv);
- расходы: `/datasets/costs_info_short.csv`. [Скачать датасет](https://code.s3.yandex.net/datasets/costs_info_short.csv).

Изучите данные и выполните предобработку. Есть ли в данных пропуски и дубликаты? Убедитесь, что типы данных во всех колонках соответствуют сохранённым в них значениям. Обратите внимание на столбцы с датой и временем.
```
# импортируем библиотеки
import pandas as pd
import numpy as np
from datetime import datetime, timedelta
from matplotlib import pyplot as plt
```
```
# загрузим данные
visits, orders, costs = (
    pd.read_csv('/datasets/visits_info_short.csv'),  # информация о посещениях сайта
    pd.read_csv('/datasets/orders_info_short.csv'),  # информация о заказах
    pd.read_csv('/datasets/costs_info_short.csv'),  # информацию о расходах на рекламу
)
```
**Изучим данные и выполним предобработку.**   
Проверим пропуски, наличие явных дубликатов и типы данных в колонках   
Начнём с переменной, содержащей журнал сессий
```
visits.head(5)
```
|   |      User Id |        Region |  Device | Channel |       Session Start |         Session End |
|--:|-------------:|--------------:|--------:|--------:|--------------------:|--------------------:|
| 0 | 981449118918 | United States | iPhone  | organic | 2019-05-01 02:36:01 | 2019-05-01 02:45:01 |
| 1 | 278965908054 | United States | iPhone  | organic | 2019-05-01 04:46:31 | 2019-05-01 04:47:35 |
| 2 | 590706206550 | United States | Mac     | organic | 2019-05-01 14:09:25 | 2019-05-01 15:32:08 |
| 3 | 326433527971 | United States | Android | TipTop  | 2019-05-01 00:29:59 | 2019-05-01 00:54:25 |
| 4 | 349773784594 | United States | Mac     | organic | 2019-05-01 03:33:35 | 2019-05-01 03:57:40 |
```
visits.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 309901 entries, 0 to 309900
Data columns (total 6 columns):
 #   Column         Non-Null Count   Dtype 
---  ------         --------------   ----- 
 0   User Id        309901 non-null  int64 
 1   Region         309901 non-null  object
 2   Device         309901 non-null  object
 3   Channel        309901 non-null  object
 4   Session Start  309901 non-null  object
 5   Session End    309901 non-null  object
dtypes: int64(1), object(5)
memory usage: 14.2+ MB
```
```
visits.isna().sum()
```
```
User Id          0
Region           0
Device           0
Channel          0
Session Start    0
Session End      0
dtype: int64
```
```
visits.duplicated().sum()
```
`0`

Посмотрим переменную с заказами
```
orders.head(5)
```
|   |      User Id |            Event Dt | Revenue |
|--:|-------------:|--------------------:|--------:|
| 0 | 188246423999 | 2019-05-01 23:09:52 | 4.99    |
| 1 | 174361394180 | 2019-05-01 12:24:04 | 4.99    |
| 2 | 529610067795 | 2019-05-01 11:34:04 | 4.99    |
| 3 | 319939546352 | 2019-05-01 15:34:40 | 4.99    |
| 4 | 366000285810 | 2019-05-01 13:59:51 | 4.99    |
```
orders.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 40212 entries, 0 to 40211
Data columns (total 3 columns):
 #   Column    Non-Null Count  Dtype  
---  ------    --------------  -----  
 0   User Id   40212 non-null  int64  
 1   Event Dt  40212 non-null  object 
 2   Revenue   40212 non-null  float64
dtypes: float64(1), int64(1), object(1)
memory usage: 942.6+ KB
```
```
orders.isna().sum()
```
```
User Id     0
Event Dt    0
Revenue     0
dtype: int64
```
```
orders.duplicated().sum()
```
`0`

Посмотрим переменную с расходами на рекламу
```
costs.head(5)
```
|   |         dt |  Channel | costs |
|--:|-----------:|---------:|------:|
| 0 | 2019-05-01 | FaceBoom | 113.3 |
| 1 | 2019-05-02 | FaceBoom | 78.1  |
| 2 | 2019-05-03 | FaceBoom | 85.8  |
| 3 | 2019-05-04 | FaceBoom | 136.4 |
| 4 | 2019-05-05 | FaceBoom | 122.1 |
```
costs.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1800 entries, 0 to 1799
Data columns (total 3 columns):
 #   Column   Non-Null Count  Dtype  
---  ------   --------------  -----  
 0   dt       1800 non-null   object 
 1   Channel  1800 non-null   object 
 2   costs    1800 non-null   float64
dtypes: float64(1), object(2)
memory usage: 42.3+ KB
```
```
costs.isna().sum()
```
```
dt         0
Channel    0
costs      0
dtype: int64
```
```
costs.duplicated().sum()
```
`0`

Пропусков и явных дубликатов не обнаружено. Переименуем колонки и установим верный тип данных
```
# приведём наименование колонок к принятому формату
visits.columns = ['user_id', 'region', 'device', 'channel', 'session_start', 'session_end']
orders.columns = ['user_id', 'event_dt', 'revenue']
costs.rename(columns = {'Channel' : 'channel'}, inplace = True) 
```
*Второй способ получить змеиный регистр* `visits.columns = visits.columns.str.lower().str.replace(' ', '_')`
```
# изменим формат данных, содержащие дату и время
visits['session_start'] = pd.to_datetime(visits['session_start'])
visits['session_end'] = pd.to_datetime(visits['session_end'])
orders['event_dt'] = pd.to_datetime(orders['event_dt'])
costs['dt'] = pd.to_datetime(costs['dt']).dt.date
```
**Проверим полученные данные на наличие ошибок и несоответствий.**

Заказчик сообщил, что для анализа нам выгружены данные за период с 1 мая по 27 октября 2019 года.   
Проверим это.
```
print('min дата журнала визитов', visits['session_start'].min())
print('max дата журнала визитов', visits['session_start'].max())
print()
print('min дата журнала заказов', orders['event_dt'].min())
print('max дата журнала заказов', orders['event_dt'].max())
print()
print('min дата журнала расходов', costs['dt'].min())
print('max дата журнала расходов', costs['dt'].max())
```
min дата журнала визитов 2019-05-01 00:00:41   
max дата журнала визитов 2019-10-31 23:59:23

min дата журнала заказов 2019-05-01 00:28:11   
max дата журнала заказов 2019-10-31 23:56:56

min дата журнала расходов 2019-05-01   
max дата журнала расходов 2019-10-27

Всё верно. журнал выгружен корректно.

Посмотрим наличие аномалий в численных данных

```
# методом describe посмотрим распределение платежей по квантилям
orders['revenue'].describe()
```
```
count    40212.000000
mean         5.370608
std          3.454208
min          4.990000
25%          4.990000
50%          4.990000
75%          4.990000
max         49.990000
Name: revenue, dtype: float64
```
```
# сгруппируем данные по платежам
pd.DataFrame(
    orders.groupby('revenue')['user_id']
    .count()
    .reset_index()
)
```
|   | revenue | user_id |
|--:|--------:|--------:|
| 0 | 4.99    | 38631   |
| 1 | 5.99    | 780     |
| 2 | 9.99    | 385     |
| 3 | 19.99   | 204     |
| 4 | 49.99   | 212     |

Чаще всего пользователи оплачивают дешевые сервисы. На дорогие пиходится меньше всего платежей.   
Аномалий в журнале покупок не выявлено.
```
# методом describe посмотрим распределение инвестицый в рекламу по квантилям
costs['costs'].describe()
```
```
count    1800.000000
mean       58.609611
std       107.740223
min         0.800000
25%         6.495000
50%        12.285000
75%        33.600000
max       630.000000
Name: costs, dtype: float64
```
```
# построим диаграмму размаха
costs.boxplot('costs', by='channel', figsize=(16, 6));
```
![изображение](https://user-images.githubusercontent.com/104757775/194817972-637e1058-4db0-418d-8ebe-85af1607ab9a.png)

Нулей и отрицательных значений в журнале расходов на рекламу нет. Однако, сразу выбиваются два канала.   
**FaceBoom**, у которого самая высокая минимальная стоимость платежа и **TipTop**, который является самым дорогим.

Вывод:   
Получены эталонные данные, которые не содержат дубликаты и пропуски. Представленные для анализа данные соответствуют заявленному периоду. Расходы на два рекламных канала заметно превышают остальные.

### 3. Задайте функции для расчёта и анализа LTV, ROI, удержания и конверсии.

Разрешается использовать функции, с которыми вы познакомились в теоретических уроках.

Это функции для вычисления значений метрик:

- `get_profiles()` — для создания профилей пользователей,
- `get_retention()` — для подсчёта Retention Rate,
- `get_conversion()` — для подсчёта конверсии,
- `get_ltv()` — для подсчёта LTV.

А также функции для построения графиков:

- `filter_data()` — для сглаживания данных,
- `plot_retention()` — для построения графика Retention Rate,
- `plot_conversion()` — для построения графика конверсии,
- `plot_ltv_roi` — для визуализации LTV и ROI.
```
# функция для создания пользовательских профилей

def get_profiles(sessions, orders, ad_costs):

    # находим параметры первых посещений
    profiles = (
        sessions.sort_values(by=['user_id', 'session_start'])
        .groupby('user_id')
        .agg(
            {
                'session_start': 'first',
                'channel': 'first',
                'device': 'first',
                'region': 'first',
            }
        )
        .rename(columns={'session_start': 'first_ts'})
        .reset_index()
    )

    # для когортного анализа определяем дату первого посещения
    # и первый день месяца, в который это посещение произошло
    profiles['dt'] = profiles['first_ts'].dt.date
    profiles['month'] = profiles['first_ts'].astype('datetime64[M]')

    # добавляем признак платящих пользователей
    profiles['payer'] = profiles['user_id'].isin(orders['user_id'].unique())

    # считаем количество уникальных пользователей
    # с одинаковыми источником и датой привлечения
    new_users = (
        profiles.groupby(['dt', 'channel'])
        .agg({'user_id': 'nunique'})
        .rename(columns={'user_id': 'unique_users'})
        .reset_index()
    )

    # объединяем траты на рекламу и число привлечённых пользователей
    ad_costs = ad_costs.merge(new_users, on=['dt', 'channel'], how='left')

    # делим рекламные расходы на число привлечённых пользователей
    ad_costs['acquisition_cost'] = ad_costs['costs'] / ad_costs['unique_users']

    # добавляем стоимость привлечения в профили
    profiles = profiles.merge(
        ad_costs[['dt', 'channel', 'acquisition_cost']],
        on=['dt', 'channel'],
        how='left',
    )

    # стоимость привлечения органических пользователей равна нулю
    profiles['acquisition_cost'] = profiles['acquisition_cost'].fillna(0)

    return profiles
```
```
# функция для расчёта удержания

def get_retention(
    profiles,
    sessions,
    observation_date,
    horizon_days,
    dimensions=[],
    ignore_horizon=False,
):

    # добавляем столбец payer в передаваемый dimensions список
    dimensions = ['payer'] + dimensions

    # исключаем пользователей, не «доживших» до горизонта анализа
    last_suitable_acquisition_date = observation_date
    if not ignore_horizon:
        last_suitable_acquisition_date = observation_date - timedelta(
            days=horizon_days - 1
        )
    result_raw = profiles.query('dt <= @last_suitable_acquisition_date')

    # собираем «сырые» данные для расчёта удержания
    result_raw = result_raw.merge(
        sessions[['user_id', 'session_start']], on='user_id', how='left'
    )
    result_raw['lifetime'] = (
        result_raw['session_start'] - result_raw['first_ts']
    ).dt.days

    # функция для группировки таблицы по желаемым признакам
    def group_by_dimensions(df, dims, horizon_days):
        result = df.pivot_table(
            index=dims, columns='lifetime', values='user_id', aggfunc='nunique'
        )
        cohort_sizes = (
            df.groupby(dims)
            .agg({'user_id': 'nunique'})
            .rename(columns={'user_id': 'cohort_size'})
        )
        result = cohort_sizes.merge(result, on=dims, how='left').fillna(0)
        result = result.div(result['cohort_size'], axis=0)
        result = result[['cohort_size'] + list(range(horizon_days))]
        result['cohort_size'] = cohort_sizes
        return result

    # получаем таблицу удержания
    result_grouped = group_by_dimensions(result_raw, dimensions, horizon_days)

    # получаем таблицу динамики удержания
    result_in_time = group_by_dimensions(
        result_raw, dimensions + ['dt'], horizon_days
    )

    # возвращаем обе таблицы и сырые данные
    return result_raw, result_grouped, result_in_time 
```
```
# функция для расчёта конверсии

def get_conversion(
    profiles,
    purchases,
    observation_date,
    horizon_days,
    dimensions=[],
    ignore_horizon=False,
):

    # исключаем пользователей, не «доживших» до горизонта анализа
    last_suitable_acquisition_date = observation_date
    if not ignore_horizon:
        last_suitable_acquisition_date = observation_date - timedelta(
            days=horizon_days - 1
        )
    result_raw = profiles.query('dt <= @last_suitable_acquisition_date')

    # определяем дату и время первой покупки для каждого пользователя
    first_purchases = (
        purchases.sort_values(by=['user_id', 'event_dt'])
        .groupby('user_id')
        .agg({'event_dt': 'first'})
        .reset_index()
    )

    # добавляем данные о покупках в профили
    result_raw = result_raw.merge(
        first_purchases[['user_id', 'event_dt']], on='user_id', how='left'
    )

    # рассчитываем лайфтайм для каждой покупки
    result_raw['lifetime'] = (
        result_raw['event_dt'] - result_raw['first_ts']
    ).dt.days

    # группируем по cohort, если в dimensions ничего нет
    if len(dimensions) == 0:
        result_raw['cohort'] = 'All users' 
        dimensions = dimensions + ['cohort']

    # функция для группировки таблицы по желаемым признакам
    def group_by_dimensions(df, dims, horizon_days):
        result = df.pivot_table(
            index=dims, columns='lifetime', values='user_id', aggfunc='nunique'
        )
        result = result.fillna(0).cumsum(axis = 1)
        cohort_sizes = (
            df.groupby(dims)
            .agg({'user_id': 'nunique'})
            .rename(columns={'user_id': 'cohort_size'})
        )
        result = cohort_sizes.merge(result, on=dims, how='left').fillna(0)
        # делим каждую «ячейку» в строке на размер когорты
        # и получаем conversion rate
        result = result.div(result['cohort_size'], axis=0)
        result = result[['cohort_size'] + list(range(horizon_days))]
        result['cohort_size'] = cohort_sizes
        return result

    # получаем таблицу конверсии
    result_grouped = group_by_dimensions(result_raw, dimensions, horizon_days)

    # для таблицы динамики конверсии убираем 'cohort' из dimensions
    if 'cohort' in dimensions: 
        dimensions = []

    # получаем таблицу динамики конверсии
    result_in_time = group_by_dimensions(
        result_raw, dimensions + ['dt'], horizon_days
    )

    # возвращаем обе таблицы и сырые данные
    return result_raw, result_grouped, result_in_time
```
```
# функция для расчёта LTV и ROI

def get_ltv(
    profiles,
    purchases,
    observation_date,
    horizon_days,
    dimensions=[],
    ignore_horizon=False,
):

    # исключаем пользователей, не «доживших» до горизонта анализа
    last_suitable_acquisition_date = observation_date
    if not ignore_horizon:
        last_suitable_acquisition_date = observation_date - timedelta(
            days=horizon_days - 1
        )
    result_raw = profiles.query('dt <= @last_suitable_acquisition_date')
    # добавляем данные о покупках в профили
    result_raw = result_raw.merge(
        purchases[['user_id', 'event_dt', 'revenue']], on='user_id', how='left'
    )
    # рассчитываем лайфтайм пользователя для каждой покупки
    result_raw['lifetime'] = (
        result_raw['event_dt'] - result_raw['first_ts']
    ).dt.days
    # группируем по cohort, если в dimensions ничего нет
    if len(dimensions) == 0:
        result_raw['cohort'] = 'All users'
        dimensions = dimensions + ['cohort']

    # функция группировки по желаемым признакам
    def group_by_dimensions(df, dims, horizon_days):
        # строим «треугольную» таблицу выручки
        result = df.pivot_table(
            index=dims, columns='lifetime', values='revenue', aggfunc='sum'
        )
        # находим сумму выручки с накоплением
        result = result.fillna(0).cumsum(axis=1)
        # вычисляем размеры когорт
        cohort_sizes = (
            df.groupby(dims)
            .agg({'user_id': 'nunique'})
            .rename(columns={'user_id': 'cohort_size'})
        )
        # объединяем размеры когорт и таблицу выручки
        result = cohort_sizes.merge(result, on=dims, how='left').fillna(0)
        # считаем LTV: делим каждую «ячейку» в строке на размер когорты
        result = result.div(result['cohort_size'], axis=0)
        # исключаем все лайфтаймы, превышающие горизонт анализа
        result = result[['cohort_size'] + list(range(horizon_days))]
        # восстанавливаем размеры когорт
        result['cohort_size'] = cohort_sizes

        # собираем датафрейм с данными пользователей и значениями CAC, 
        # добавляя параметры из dimensions
        cac = df[['user_id', 'acquisition_cost'] + dims].drop_duplicates()

        # считаем средний CAC по параметрам из dimensions
        cac = (
            cac.groupby(dims)
            .agg({'acquisition_cost': 'mean'})
            .rename(columns={'acquisition_cost': 'cac'})
        )

        # считаем ROI: делим LTV на CAC
        roi = result.div(cac['cac'], axis=0)

        # удаляем строки с бесконечным ROI
        roi = roi[~roi['cohort_size'].isin([np.inf])]

        # восстанавливаем размеры когорт в таблице ROI
        roi['cohort_size'] = cohort_sizes

        # добавляем CAC в таблицу ROI
        roi['cac'] = cac['cac']

        # в финальной таблице оставляем размеры когорт, CAC
        # и ROI в лайфтаймы, не превышающие горизонт анализа
        roi = roi[['cohort_size', 'cac'] + list(range(horizon_days))]

        # возвращаем таблицы LTV и ROI
        return result, roi

    # получаем таблицы LTV и ROI
    result_grouped, roi_grouped = group_by_dimensions(
        result_raw, dimensions, horizon_days
    )

    # для таблиц динамики убираем 'cohort' из dimensions
    if 'cohort' in dimensions:
        dimensions = []

    # получаем таблицы динамики LTV и ROI
    result_in_time, roi_in_time = group_by_dimensions(
        result_raw, dimensions + ['dt'], horizon_days
    )

    return (
        result_raw,  # сырые данные
        result_grouped,  # таблица LTV
        result_in_time,  # таблица динамики LTV
        roi_grouped,  # таблица ROI
        roi_in_time,  # таблица динамики ROI
    )
```
Зададим функции визуализации метрик
```
# функция для сглаживания фрейма

def filter_data(df, window):
    # для каждого столбца применяем скользящее среднее
    for column in df.columns.values:
        df[column] = df[column].rolling(window).mean() 
    return df 
```
```
# функция для визуализации удержания

def plot_retention(retention, retention_history, horizon, window=7):

    # задаём размер сетки для графиков
    plt.figure(figsize=(15, 10))

    # исключаем размеры когорт и удержание первого дня
    retention = retention.drop(columns=['cohort_size', 0])
    # в таблице динамики оставляем только нужный лайфтайм
    retention_history = retention_history.drop(columns=['cohort_size'])[
        [horizon - 1]
    ]

    # если в индексах таблицы удержания только payer,
    # добавляем второй признак — cohort
    if retention.index.nlevels == 1:
        retention['cohort'] = 'All users'
        retention = retention.reset_index().set_index(['cohort', 'payer'])

    # в таблице графиков — два столбца и две строки, четыре ячейки
    # в первой строим кривые удержания платящих пользователей
    ax1 = plt.subplot(2, 2, 1)
    retention.query('payer == True').droplevel('payer').T.plot(
        grid=True, ax=ax1
    )
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('Удержание платящих пользователей')

    # во второй ячейке строим кривые удержания неплатящих
    # вертикальная ось — от графика из первой ячейки
    ax2 = plt.subplot(2, 2, 2, sharey=ax1)
    retention.query('payer == False').droplevel('payer').T.plot(
        grid=True, ax=ax2
    )
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('Удержание неплатящих пользователей')

    # в третьей ячейке — динамика удержания платящих
    ax3 = plt.subplot(2, 2, 3)
    # получаем названия столбцов для сводной таблицы
    columns = [
        name
        for name in retention_history.index.names
        if name not in ['dt', 'payer']
    ]
    # фильтруем данные и строим график
    filtered_data = retention_history.query('payer == True').pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax3)
    plt.xlabel('Дата привлечения')
    plt.title(
        'Динамика удержания платящих пользователей на {}-й день'.format(
            horizon
        )
    )

    # в чётвертой ячейке — динамика удержания неплатящих
    ax4 = plt.subplot(2, 2, 4, sharey=ax3)
    # фильтруем данные и строим график
    filtered_data = retention_history.query('payer == False').pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax4)
    plt.xlabel('Дата привлечения')
    plt.title(
        'Динамика удержания неплатящих пользователей на {}-й день'.format(
            horizon
        )
    )
    
    plt.tight_layout()
    plt.show() 
```
```
# функция для визуализации конверсии

def plot_conversion(conversion, conversion_history, horizon, window=7):

    # задаём размер сетки для графиков
    plt.figure(figsize=(15, 5))

    # исключаем размеры когорт
    conversion = conversion.drop(columns=['cohort_size'])
    # в таблице динамики оставляем только нужный лайфтайм
    conversion_history = conversion_history.drop(columns=['cohort_size'])[
        [horizon - 1]
    ]

    # первый график — кривые конверсии
    ax1 = plt.subplot(1, 2, 1)
    conversion.T.plot(grid=True, ax=ax1)
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('Конверсия пользователей')

    # второй график — динамика конверсии
    ax2 = plt.subplot(1, 2, 2, sharey=ax1)
    columns = [
        # столбцами сводной таблицы станут все столбцы индекса, кроме даты
        name for name in conversion_history.index.names if name not in ['dt']
    ]
    filtered_data = conversion_history.pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax2)
    plt.xlabel('Дата привлечения')
    plt.title('Динамика конверсии пользователей на {}-й день'.format(horizon))

    plt.tight_layout()
    plt.show() 
```
```
# функция для визуализации LTV и ROI

def plot_ltv_roi(ltv, ltv_history, roi, roi_history, horizon, window=7):

    # задаём сетку отрисовки графиков
    plt.figure(figsize=(20, 10))

    # из таблицы ltv исключаем размеры когорт
    ltv = ltv.drop(columns=['cohort_size'])
    # в таблице динамики ltv оставляем только нужный лайфтайм
    ltv_history = ltv_history.drop(columns=['cohort_size'])[[horizon - 1]]

    # стоимость привлечения запишем в отдельный фрейм
    cac_history = roi_history[['cac']]

    # из таблицы roi исключаем размеры когорт и cac
    roi = roi.drop(columns=['cohort_size', 'cac'])
    # в таблице динамики roi оставляем только нужный лайфтайм
    roi_history = roi_history.drop(columns=['cohort_size', 'cac'])[
        [horizon - 1]
    ]

    # первый график — кривые ltv
    ax1 = plt.subplot(2, 3, 1)
    ltv.T.plot(grid=True, ax=ax1)
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('LTV')

    # второй график — динамика ltv
    ax2 = plt.subplot(2, 3, 2, sharey=ax1)
    # столбцами сводной таблицы станут все столбцы индекса, кроме даты
    columns = [name for name in ltv_history.index.names if name not in ['dt']]
    filtered_data = ltv_history.pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax2)
    plt.xlabel('Дата привлечения')
    plt.title('Динамика LTV пользователей на {}-й день'.format(horizon))

    # третий график — динамика cac
    ax3 = plt.subplot(2, 3, 3, sharey=ax1)
    # столбцами сводной таблицы станут все столбцы индекса, кроме даты
    columns = [name for name in cac_history.index.names if name not in ['dt']]
    filtered_data = cac_history.pivot_table(
        index='dt', columns=columns, values='cac', aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax3)
    plt.xlabel('Дата привлечения')
    plt.title('Динамика стоимости привлечения пользователей')

    # четвёртый график — кривые roi
    ax4 = plt.subplot(2, 3, 4)
    roi.T.plot(grid=True, ax=ax4)
    plt.axhline(y=1, color='red', linestyle='--', label='Уровень окупаемости')
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('ROI')

    # пятый график — динамика roi
    ax5 = plt.subplot(2, 3, 5, sharey=ax4)
    # столбцами сводной таблицы станут все столбцы индекса, кроме даты
    columns = [name for name in roi_history.index.names if name not in ['dt']]
    filtered_data = roi_history.pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax5)
    plt.axhline(y=1, color='red', linestyle='--', label='Уровень окупаемости')
    plt.xlabel('Дата привлечения')
    plt.title('Динамика ROI пользователей на {}-й день'.format(horizon))

    plt.tight_layout()
    plt.show() 
```
### 3. Исследовательский анализ данных

- Составьте профили пользователей. Определите минимальную и максимальную даты привлечения пользователей.
- Выясните, из каких стран пользователи приходят в приложение и на какую страну приходится больше всего платящих пользователей. Постройте таблицу, отражающую количество пользователей и долю платящих из каждой страны.
- Узнайте, какими устройствами пользуются клиенты и какие устройства предпочитают платящие пользователи. Постройте таблицу, отражающую количество пользователей и долю платящих для каждого устройства.
- Изучите рекламные источники привлечения и определите каналы, из которых пришло больше всего платящих пользователей. Постройте таблицу, отражающую количество пользователей и долю платящих для каждого канала привлечения.

После каждого пункта сформулируйте выводы.

**Составьте профили пользователей. Определите минимальную и максимальную даты привлечения пользователей.**
```
# получаем профили пользователей
profiles = get_profiles(visits, orders, costs)
profiles.head(5)
```
|   |  user_id |            first_ts |    channel | device |        region |         dt |      month | payer | acquisition_cost |
|--:|---------:|--------------------:|-----------:|-------:|--------------:|-----------:|-----------:|------:|-----------------:|
| 0 | 599326   | 2019-05-07 20:58:57 | FaceBoom   | Mac    | United States | 2019-05-07 | 2019-05-01 | True  | 1.088172         |
| 1 | 4919697  | 2019-07-09 12:46:07 | FaceBoom   | iPhone | United States | 2019-07-09 | 2019-07-01 | False | 1.107237         |
| 2 | 6085896  | 2019-10-01 09:58:33 | organic    | iPhone | France        | 2019-10-01 | 2019-10-01 | False | 0.000000         |
| 3 | 22593348 | 2019-08-22 21:35:48 | AdNonSense | PC     | Germany       | 2019-08-22 | 2019-08-01 | False | 0.988235         |
| 4 | 31989216 | 2019-10-02 00:07:44 | YRabbit    | iPhone | United States | 2019-10-02 | 2019-10-01 | False | 0.230769         |
```
# посмотрим типы данных
profiles.info()
```
```
<class 'pandas.core.frame.DataFrame'>
Int64Index: 150008 entries, 0 to 150007
Data columns (total 9 columns):
 #   Column            Non-Null Count   Dtype         
---  ------            --------------   -----         
 0   user_id           150008 non-null  int64         
 1   first_ts          150008 non-null  datetime64[ns]
 2   channel           150008 non-null  object        
 3   device            150008 non-null  object        
 4   region            150008 non-null  object        
 5   dt                150008 non-null  object        
 6   month             150008 non-null  datetime64[ns]
 7   payer             150008 non-null  bool          
 8   acquisition_cost  150008 non-null  float64       
dtypes: bool(1), datetime64[ns](2), float64(1), int64(1), object(4)
memory usage: 10.4+ MB
```
```
# для колонки dt установим тип datetime
profiles['dt'] = pd.to_datetime(profiles['dt'])
```
```
# определим минимальную и максимальную даты привлечения пользователей
min_analysis_date = profiles['dt'].min()
max_analysis_date = profiles['dt'].max() 
print('Минимальная дата привлечения', min_analysis_date)
print('Максимальная дата привлечения', max_analysis_date)
```
Минимальная дата привлечения 2019-05-01 00:00:00   
Максимальная дата привлечения 2019-10-27 00:00:00

Для анализа получили данные почти за пол года.

**Выясните, из каких стран пользователи приходят в приложение и на какую страну приходится больше всего платящих пользователей. Постройте таблицу, отражающую количество пользователей и долю платящих из каждой страны.**
```
# Определим страну на которую приходится больше всего платящих пользователей
country = pd.DataFrame(
    profiles.groupby('region')['user_id'].count()
    .reset_index()                  
    .merge(profiles.query('payer == True')
          .groupby('region')['payer'].count()
          .reset_index())
    .rename(columns={'user_id': 'all_users', 'payer': 'buyer'})
    .sort_values(by='buyer', ascending=False)
)
country['%'] = (country['buyer'] / country['all_users']*100).round(2)
country
```
|   |        region | all_users | buyer |    % |
|--:|--------------:|----------:|------:|-----:|
| 3 | United States | 100002    | 6902  | 6.90 |
| 2 | UK            | 17575     | 700   | 3.98 |
| 0 | France        | 17450     | 663   | 3.80 |
| 1 | Germany       | 14981     | 616   | 4.11 |

Больше всего пользователей приложения из **United States**. Они же составляют большую долю платящих пользователей.

**Узнайте, какими устройствами пользуются клиенты и какие устройства предпочитают платящие пользователи. Постройте таблицу, отражающую количество пользователей и долю платящих для каждого устройства.**
```
device = pd.DataFrame(
    profiles.groupby('device')['user_id'].count()
    .reset_index()                  
    .merge(profiles.query('payer == True')
          .groupby('device')['payer'].count()
          .reset_index())
    .rename(columns={'user_id': 'all_users', 'payer': 'buyer'})
    .sort_values(by='buyer', ascending=False)
)
device['%'] = (device['buyer'] / device['all_users']*100).round(2)
device
```
|   |  device | all_users | buyer |    % |
|--:|--------:|----------:|------:|-----:|
| 3 | iPhone  | 54479     | 3382  | 6.21 |
| 0 | Android | 35032     | 2050  | 5.85 |
| 1 | Mac     | 30042     | 1912  | 6.36 |
| 2 | PC      | 30455     | 1537  | 5.05 |

Чаще всего клиенты используют **iPhone**. В разрезе устройств большая доля платящих пользователей приходится на **Mac**. Однако, разница доли платящих пользователей незначительная.

**Изучите рекламные источники привлечения и определите каналы, из которых пришло больше всего платящих пользователей. Постройте таблицу, отражающую количество пользователей и долю платящих для каждого канала привлечения.**
```
channel = pd.DataFrame(
    profiles.groupby('channel')['user_id'].count()
    .reset_index()                  
    .merge(profiles.query('payer == True')
          .groupby('channel')['payer'].count()
          .reset_index())
    .rename(columns={'user_id': 'all_users', 'payer': 'buyer'})
    .sort_values(by='buyer', ascending=False)
)
channel['cannel_%'] = (channel['buyer'] / channel['all_users']*100).round(2)
channel
```
|    |            channel | all_users | buyer | cannel_% |
|---:|-------------------:|----------:|------:|---------:|
|  1 | FaceBoom           | 29144     | 3557  | 12.20    |
|  6 | TipTop             | 19561     | 1878  | 9.60     |
| 10 | organic            | 56439     | 1160  | 2.06     |
|  7 | WahooNetBanner     | 8553      | 453   | 5.30     |
|  0 | AdNonSense         | 3880      | 440   | 11.34    |
|  5 | RocketSuperAds     | 4448      | 352   | 7.91     |
|  2 | LeapBob            | 8553      | 262   | 3.06     |
|  4 | OppleCreativeMedia | 8605      | 233   | 2.71     |
|  9 | lambdaMediaAds     | 2149      | 225   | 10.47    |
|  8 | YRabbit            | 4312      | 165   | 3.83     |
|  3 | MediaTornado       | 4364      | 156   | 3.57     |

Больше всего платящих пользователей приносят каналы **FaceBoom** и **TipTop**. **Organic** показывает много платящих пользователей, хотя их доля мала от бщего числа пользователей. Каналы **AdNonSense** и **lambdaMediaAds** приводят не много пользователей, однако доля платящих более 1/10.
```
# для одинаковых вычеслений разных данных можно использовать функцию:

# задаем датасет и столбец по которому будем считать, еще задаем сам признак по умолчанию:
def payer_share(df, column, column_to_cal = 'payer'): 
    result = (df
         .groupby(column)
             
         # обрати, пожалуйста, внимание, что мы можем сразу задать название колонок:
         .agg(total_users = (column_to_cal,'count'),
              payer_users = (column_to_cal,'sum'),
              payer_sahre = (column_to_cal,'mean'))
         .reset_index()
         .sort_values(by=('payer_sahre'), ascending=False)
             
         # пример того, как можно подсчитать среднее в одно действие при создании сводной таблицы,
         # для этого используем assign() и ламбду функцию:
         .assign(payer_sahre_2 = lambda x : x['payer_users'] / x['total_users'])   
         # т.е. выше мы для каждой строки считаем отношение платящих к общему кол-ву для каждой строки.            
            )
    return result

payer_share(profiles, 'channel').style.format({'payer_sahre': '{:.1%}', 'payer_sahre_2': '{:.1%}'})
```
|    |       channel      | total_users | payer_users | payer_sahre | payer_sahre_2 |
|:--:|:------------------:|:-----------:|:-----------:|:-----------:|:-------------:|
|  1 | FaceBoom           | 29144       | 3557        | 12.2%       | 12.2%         |
|  0 | AdNonSense         | 3880        | 440         | 11.3%       | 11.3%         |
|  9 | lambdaMediaAds     | 2149        | 225         | 10.5%       | 10.5%         |
|  6 | TipTop             | 19561       | 1878        | 9.6%        | 9.6%          |
|  5 | RocketSuperAds     | 4448        | 352         | 7.9%        | 7.9%          |
|  7 | WahooNetBanner     | 8553        | 453         | 5.3%        | 5.3%          |
|  8 | YRabbit            | 4312        | 165         | 3.8%        | 3.8%          |
|  3 | MediaTornado       | 4364        | 156         | 3.6%        | 3.6%          |
|  2 | LeapBob            | 8553        | 262         | 3.1%        | 3.1%          |
|  4 | OppleCreativeMedia | 8605        | 233         | 2.7%        | 2.7%          |
| 10 | organic            | 56439       | 1160        | 2.1%        | 2.1%          |
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
```
```
```
```
```
```
```
```
