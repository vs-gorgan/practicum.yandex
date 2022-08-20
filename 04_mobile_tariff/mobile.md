# Описание проекта

Вы аналитик компании «Мегалайн» — федерального оператора сотовой связи. Клиентам предлагают два тарифных плана: «Смарт» и «Ультра». Чтобы скорректировать рекламный бюджет, коммерческий департамент хочет понять, какой тариф приносит больше денег.

Вам предстоит сделать предварительный анализ тарифов на небольшой выборке клиентов. В вашем распоряжении данные 500 пользователей «Мегалайна»: кто они, откуда, каким тарифом пользуются, сколько звонков и сообщений каждый отправил за 2018-й год. Нужно проанализировать поведение клиентов и сделать вывод — какой тариф лучше.

### Откройте файл с данными и изучите общую информацию

**1. Откройте файл `/datasets/calls.csv`, сохраните датафрейм в переменную `calls`.**

```
calls = pd.read_csv('/datasets/calls.csv')
```
**2. Выведим первые 5 строк датафрейма `calls`.**
```
calls.head(5)
```
|   |     id |  call_date | duration | user_id |
|--:|-------:|-----------:|---------:|--------:|
| 0 | 1000_0 | 2018-07-25 | 0.00     | 1000    |
| 1 | 1000_1 | 2018-08-17 | 0.00     | 1000    |
| 2 | 1000_2 | 2018-06-11 | 2.85     | 1000    |
| 3 | 1000_3 | 2018-09-21 | 13.80    | 1000    |
| 4 | 1000_4 | 2018-12-15 | 5.18     | 1000    |

**3.** Выведите основную информацию для датафрейма `calls` с помощью метода `info()`.**
```
calls.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 202607 entries, 0 to 202606
Data columns (total 4 columns):
 #   Column     Non-Null Count   Dtype  
---  ------     --------------   -----  
 0   id         202607 non-null  object 
 1   call_date  202607 non-null  object 
 2   duration   202607 non-null  float64
 3   user_id    202607 non-null  int64  
dtypes: float64(1), int64(1), object(2)
memory usage: 6.2+ MB

```
**4. С помощью метода `hist()` выведите гистограмму для столбца с продолжительностью звонков.**
```
calls.duration.hist()
```
![изображение](https://user-images.githubusercontent.com/104757775/185745744-5514dbf1-1f7f-4280-9126-6bac866fff05.png)

**5. Откройте файл `/datasets/internet.csv`, сохраните датафрейм в переменную `sessions`.**
```
sessions = pd.read_csv('/datasets/internet.csv')
```
**6. Выведите первые 5 строк датафрейма `sessions`.**
```
sessions.head(5)
```
|   | Unnamed: 0 |     id | mb_used | session_date | user_id |
|--:|-----------:|-------:|--------:|-------------:|--------:|
| 0 | 0          | 1000_0 | 112.95  | 2018-11-25   | 1000    |
| 1 | 1          | 1000_1 | 1052.81 | 2018-09-07   | 1000    |
| 2 | 2          | 1000_2 | 1197.26 | 2018-06-25   | 1000    |
| 3 | 3          | 1000_3 | 550.27  | 2018-08-22   | 1000    |
| 4 | 4          | 1000_4 | 302.56  | 2018-09-24   | 1000    |

**7. Выведите основную информацию для датафрейма sessions с помощью метода `info()`.**
```
sessions.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 149396 entries, 0 to 149395
Data columns (total 5 columns):
 #   Column        Non-Null Count   Dtype  
---  ------        --------------   -----  
 0   Unnamed: 0    149396 non-null  int64  
 1   id            149396 non-null  object 
 2   mb_used       149396 non-null  float64
 3   session_date  149396 non-null  object 
 4   user_id       149396 non-null  int64  
dtypes: float64(1), int64(2), object(2)
memory usage: 5.7+ MB
```
**8. С помощью метода `hist()` выведите гистограмму для столбца с количеством потраченных мегабайт.**
```
sessions.mb_used.hist()
```
![изображение](https://user-images.githubusercontent.com/104757775/185745836-f6cb6b36-eb99-44a9-bfa3-8fbaa387b20b.png)

**9.  Откройте файл `/datasets/messages.csv`, сохраните датафрейм в переменную `messages`.**
```
messages = pd.read_csv('/datasets/messages.csv')
```
**10. Выведите первые 5 строк датафрейма `messages`.**
```
messages.head(5)
```
|   |     id | message_date | user_id |
|--:|-------:|-------------:|--------:|
| 0 | 1000_0 | 2018-06-27   | 1000    |
| 1 | 1000_1 | 2018-10-08   | 1000    |
| 2 | 1000_2 | 2018-08-04   | 1000    |
| 3 | 1000_3 | 2018-06-16   | 1000    |
| 4 | 1000_4 | 2018-12-05   | 1000    |

**11. Выведите основную информацию для датафрейма `messages` с помощью метода `info()`.** 
```
messages.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 123036 entries, 0 to 123035
Data columns (total 3 columns):
 #   Column        Non-Null Count   Dtype 
---  ------        --------------   ----- 
 0   id            123036 non-null  object
 1   message_date  123036 non-null  object
 2   user_id       123036 non-null  int64 
dtypes: int64(1), object(2)
memory usage: 2.8+ MB

```

**12. Откройте файл `/datasets/tariffs.csv`, сохраните датафрейм в переменную `tariffs`.**
```
tariffs = pd.read_csv('/datasets/tariffs.csv')
```
**13. Выведите весь датафрейм `tariffs`.**
```
tariffs
```
|   | messages_included | mb_per_month_included | minutes_included | rub_monthly_fee | rub_per_gb | rub_per_message | rub_per_minute | tariff_name |
|--:|------------------:|----------------------:|-----------------:|----------------:|-----------:|----------------:|---------------:|------------:|
| 0 | 50                | 15360                 | 500              | 550             | 200        | 3               | 3              | smart       |
| 1 | 1000              | 30720                 | 3000             | 1950            | 150        | 1               | 1              | ultra       |

**14. Выведите основную информацию для датафрейма `tariffs` с помощью метода `info()`.**
```
tariffs.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 2 entries, 0 to 1
Data columns (total 8 columns):
 #   Column                 Non-Null Count  Dtype 
---  ------                 --------------  ----- 
 0   messages_included      2 non-null      int64 
 1   mb_per_month_included  2 non-null      int64 
 2   minutes_included       2 non-null      int64 
 3   rub_monthly_fee        2 non-null      int64 
 4   rub_per_gb             2 non-null      int64 
 5   rub_per_message        2 non-null      int64 
 6   rub_per_minute         2 non-null      int64 
 7   tariff_name            2 non-null      object
dtypes: int64(7), object(1)
memory usage: 256.0+ bytes
```
**15. Откройте файл `/datasets/users.csv`, сохраните датафрейм в переменную `users`.**
```
users = pd.read_csv('/datasets/users.csv')
```
**16. Выведите первые 5 строк датафрейма `users`.**
```
users.head(5)
```
|   | user_id | age | churn_date |        city | first_name | last_name |   reg_date | tariff |
|--:|--------:|----:|-----------:|------------:|-----------:|----------:|-----------:|-------:|
| 0 | 1000    | 52  | NaN        | Краснодар   | Рафаил     | Верещагин | 2018-05-25 | ultra  |
| 1 | 1001    | 41  | NaN        | Москва      | Иван       | Ежов      | 2018-11-01 | smart  |
| 2 | 1002    | 59  | NaN        | Стерлитамак | Евгений    | Абрамович | 2018-06-17 | smart  |
| 3 | 1003    | 23  | NaN        | Москва      | Белла      | Белякова  | 2018-08-17 | ultra  |
| 4 | 1004    | 68  | NaN        | Новокузнецк | Татьяна    | Авдеенко  | 2018-05-14 | ultra  |

**17.  Выведите основную информацию для датафрейма `users` с помощью метода `info()`.**
```
users.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 500 entries, 0 to 499
Data columns (total 8 columns):
 #   Column      Non-Null Count  Dtype 
---  ------      --------------  ----- 
 0   user_id     500 non-null    int64 
 1   age         500 non-null    int64 
 2   churn_date  38 non-null     object
 3   city        500 non-null    object
 4   first_name  500 non-null    object
 5   last_name   500 non-null    object
 6   reg_date    500 non-null    object
 7   tariff      500 non-null    object
dtypes: int64(2), object(6)
memory usage: 31.4+ KB
```
### 2 .Подготовьте данные

**18.  Приведите столбцы:**

- `reg_date` из таблицы `users`
- `churn_date` из таблицы `users`
- `call_date` из таблицы `calls`
- `message_date` из таблицы `messages`
- `session_date` из таблицы `sessions`

к новому типу с помощью метода `to_datetime()`.
```
users['reg_date'] = pd.to_datetime(users['reg_date'], format='%Y-%m-%d') # обработка столбца reg_date

users['churn_date'] = pd.to_datetime(users['churn_date'], format='%Y-%m-%d') # обработка столбца churn_date

calls['call_date'] = pd.to_datetime(calls['call_date'], format='%Y-%m-%d')# обработка столбца call_date

messages['message_date'] = pd.to_datetime(messages['message_date'], format='%Y-%m-%d') # обработка столбца message_date

sessions['session_date'] = pd.to_datetime(sessions['session_date'], format='%Y-%m-%d')# обработка столбца session_date
```
**19. В данных вы найдёте звонки с нулевой продолжительностью. Это не ошибка: нулями обозначены пропущенные звонки, поэтому их не нужно удалять.**

Однако в столбце `duration` датафрейма `calls` значения дробные. Округлите значения столбца `duration` вверх с помощью метода `numpy.ceil()` и приведите столбец `duration` к типу `int`.

```
import numpy as np

# округление значений столбца duration с помощью np.ceil() и приведение типа к int
calls['duration'] = np.ceil(calls['duration']).astype('int')
```
```
```
```
```
```
```
```
```
```
```
```
```
```
