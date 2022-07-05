# Яндекс Музыка

Сравнение Москвы и Петербурга окружено мифами. Например:
 * Москва — мегаполис, подчинённый жёсткому ритму рабочей недели;
 * Петербург — культурная столица, со своими вкусами.

На данных Яндекс Музыки вы сравните поведение пользователей двух столиц.

**Цель исследования** — проверьте три гипотезы:
1. Активность пользователей зависит от дня недели. Причём в Москве и Петербурге это проявляется по-разному.
2. В понедельник утром в Москве преобладают одни жанры, а в Петербурге — другие. Так же и вечером пятницы преобладают разные жанры — в зависимости от города. 
3. Москва и Петербург предпочитают разные жанры музыки. В Москве чаще слушают поп-музыку, в Петербурге — русский рэп.

**Ход исследования**

Данные о поведении пользователей вы получите из файла `yandex_music_project.csv`. О качестве данных ничего не известно. Поэтому перед проверкой гипотез понадобится обзор данных. 

Вы проверите данные на ошибки и оцените их влияние на исследование. Затем, на этапе предобработки вы поищете возможность исправить самые критичные ошибки данных.
 
Таким образом, исследование пройдёт в три этапа:
 1. Обзор данных.
 2. Предобработка данных.
 3. Проверка гипотез.

## Обзор данных

Составьте первое представление о данных Яндекс Музыки.

**Задание 1**

Основной инструмент аналитика — `pandas`. Импортируйте эту библиотеку.
```
import pandas as pd # импорт библиотеки pandas
```
**Задание 2**

Прочитайте файл `yandex_music_project.csv` из папки `/datasets` и сохраните его в переменной `df`:
```
df = pd.read_csv('/datasets/yandex_music_project.csv') # чтение файла с данными и сохранение в df
```
**Задание 3**


Выведите на экран первые десять строк таблицы:
```
df.head(10) # получение первых 10 строк таблицы df
```
| userID |    Track |                      artist |            genre |   City |             time |      Day |           |
|-------:|---------:|----------------------------:|-----------------:|-------:|-----------------:|---------:|-----------|
|    0   | FFB692EC | Kamigata To Boots           | The Mass Missile | rock   | Saint-Petersburg | 20:28:33 | Wednesday |
|    1   | 55204538 | Delayed Because of Accident | Andreas Rönnberg | rock   | Moscow           | 14:07:09 | Friday    |
|    2   | 20EC38   | Funiculì funiculà           | Mario Lanza      | pop    | Saint-Petersburg | 20:58:07 | Wednesday |
|    3   | A3DD03C9 | Dragons in the Sunset       | Fire + Ice       | folk   | Saint-Petersburg | 08:37:09 | Monday    |
|    4   | E2DC1FAE | Soul People                 | Space Echo       | dance  | Moscow           | 08:34:34 | Monday    |
|    5   | 842029A1 | Преданная                   | IMPERVTOR        | rusrap | Saint-Petersburg | 13:09:41 | Friday    |
|    6   | 4CB90AA5 | True                        | Roman Messer     | dance  | Moscow           | 13:00:07 | Wednesday |
|    7   | F03E1C1F | Feeling This Way            | Polina Griffith  | dance  | Moscow           | 20:47:49 | Wednesday |
|    8   | 8FA1D3BE | И вновь продолжается бой    | NaN              | ruspop | Moscow           | 09:17:40 | Friday    |
|    9   | E772D5C0 | Pessimist                   | NaN              | dance  | Saint-Petersburg | 21:20:49 | Wednesday |

**Задание 4**


Одной командой получить общую информацию о таблице c помощью метода `info()`:
```
df.info() # получение общей информации о данных в таблице df
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 65079 entries, 0 to 65078
Data columns (total 7 columns):
 #   Column    Non-Null Count  Dtype 
---  ------    --------------  ----- 
 0     userID  65079 non-null  object
 1   Track     63848 non-null  object
 2   artist    57876 non-null  object
 3   genre     63881 non-null  object
 4     City    65079 non-null  object
 5   time      65079 non-null  object
 6   Day       65079 non-null  object
dtypes: object(7)
memory usage: 3.5+ MB
```
Итак, в таблице семь столбцов. Тип данных во всех столбцах — `object`.

Согласно документации к данным:
* `userID` — идентификатор пользователя;
* `Track` — название трека;  
* `artist` — имя исполнителя;
* `genre` — название жанра;
* `City` — город пользователя;
* `time` — время начала прослушивания;
* `Day` — день недели.

Количество значений в столбцах различается. Значит, в данных есть пропущенные значения.

**Задание 5**

**Вопрос со свободной формой ответа**

В названиях колонок видны нарушения стиля:
* Строчные буквы сочетаются с прописными.
* Встречаются пробелы.

Какое третье нарушение?
```
# Не применён змеиный_регистр в слолбце "userID"
```
**Выводы**

В каждой строке таблицы — данные о прослушанном треке. Часть колонок описывает саму композицию: название, исполнителя и жанр. Остальные данные рассказывают о пользователе: из какого он города, когда он слушал музыку. 

Предварительно можно утверждать, что данных достаточно для проверки гипотез. Но встречаются пропуски в данных, а в названиях колонок — расхождения с хорошим стилем.

Чтобы двигаться дальше, нужно устранить проблемы в данных.

## Предобработка данных
Исправьте стиль в заголовках столбцов, исключите пропуски. Затем проверьте данные на дубликаты.

### Стиль заголовков

**Задание 6**

Выведите на экран названия столбцов:
```
df.columns # перечень названий столбцов таблицы df
```
```
Index(['  userID', 'Track', 'artist', 'genre', '  City  ', 'time', 'Day'], dtype='object')
```
**Задание 7**


Приведите названия в соответствие с хорошим стилем:
* несколько слов в названии запишите в «змеином_регистре»,
* все символы сделайте строчными,
* устраните пробелы.

Для этого переименуйте колонки так:
* `'  userID'` → `'user_id'`;
* `'Track'` → `'track'`;
* `'  City  '` → `'city'`;
* `'Day'` → `'day'`.
```
df.rename(columns = {'  userID': 'user_id', 'Track': 'track', '  City  ': 'city', 'Day': 'day'}, inplace = True) # переименование столбцов
```
**Задание 8**


Проверьте результат. Для этого ещё раз выведите на экран названия столбцов:
```
df.columns # проверка результатов - перечень названий столбцов
```
```
Index(['user_id', 'track', 'artist', 'genre', 'city', 'time', 'day'], dtype='object')
```
### Пропуски значений

**Задание 9**

Сначала посчитайте, сколько в таблице пропущенных значений. Для этого достаточно двух методов `pandas`:
```
df.isna().sum() # подсчёт пропусков
```
```
user_id       0
track      1231
artist     7203
genre      1198
city          0
time          0
day           0
dtype: int64
```
Не все пропущенные значения влияют на исследование. Так в `track` и `artist` пропуски не важны для вашей работы. Достаточно заменить их явными обозначениями.

Но пропуски в `genre` могут помешать сравнению музыкальных вкусов в Москве и Санкт-Петербурге. На практике было бы правильно установить причину пропусков и восстановить данные. Такой возможности нет в учебном проекте. Придётся:
* заполнить и эти пропуски явными обозначениями;
* оценить, насколько они повредят расчётам. 

 **Задание 10**

Замените пропущенные значения в столбцах `track`, `artist` и `genre` на строку `'unknown'`. Для этого создайте список `columns_to_replace`, переберите его элементы циклом `for` и для каждого столбца выполните замену пропущенных значений:
```
columns_to_replace = df.fillna(value={'track': 'unknown', 'artist': 'unknown', 'genre': 'unknown'}) # перебор названий столбцов в цикле и замена пропущенных значений на 'unknown'
```
**Задание 11**

Убедитесь, что в таблице не осталось пропусков. Для этого ещё раз посчитайте пропущенные значения.
```
columns_to_replace.isna().sum() # подсчёт пропусков
```
```
user_id    0
track      0
artist     0
genre      0
city       0
time       0
day        0
dtype: int64
```
### Дубликаты

**Задание 12**

Посчитайте явные дубликаты в таблице одной командой:
```
columns_to_replace.duplicated().sum() # подсчёт явных дубликатов
```
```
3826
```
**Задание 13**

Вызовите специальный метод `pandas`, чтобы удалить явные дубликаты:
```
columns_to_replace = columns_to_replace.drop_duplicates() # удаление явных дубликатов
```
**Задание 14**

Ещё раз посчитайте явные дубликаты в таблице — убедитесь, что полностью от них избавились:
```
columns_to_replace.duplicated().sum() # проверка на отсутствие дубликатов
```
```
0
```
Теперь избавьтесь от неявных дубликатов в колонке `genre`. Например, название одного и того же жанра может быть записано немного по-разному. Такие ошибки тоже повлияют на результат исследования.

**Задание 15**

Выведите на экран список уникальных названий жанров, отсортированный в алфавитном порядке. Для этого:
1. извлеките нужный столбец датафрейма; 
2. примените к нему метод сортировки;
3. для отсортированного столбца вызовите метод, который вернёт уникальные значения из столбца.
```
# Просмотр уникальных названий жанров
df_genre = columns_to_replace['genre'] # извлеките нужный столбец датафрейма
df_genre = df_genre.sort_values() # примените к нему метод сортировки
df_genre = df_genre.unique() # для отсортированного столбца вызовите метод, который вернёт уникальные значения из столбца
print(df_genre)
```
```
['acid' 'acoustic' 'action' 'adult' 'africa' 'afrikaans' 'alternative'
 'alternativepunk' 'ambient' 'americana' 'animated' 'anime' 'arabesk'
 'arabic' 'arena' 'argentinetango' 'art' 'audiobook' 'author' 'avantgarde'
 'axé' 'baile' 'balkan' 'beats' 'bigroom' 'black' 'bluegrass' 'blues'
 'bollywood' 'bossa' 'brazilian' 'breakbeat' 'breaks' 'broadway'
 'cantautori' 'cantopop' 'canzone' 'caribbean' 'caucasian' 'celtic'
 'chamber' 'chanson' 'children' 'chill' 'chinese' 'choral' 'christian'
 'christmas' 'classical' 'classicmetal' 'club' 'colombian' 'comedy'
 'conjazz' 'contemporary' 'country' 'cuban' 'dance' 'dancehall' 'dancepop'
 'dark' 'death' 'deep' 'deutschrock' 'deutschspr' 'dirty' 'disco' 'dnb'
 'documentary' 'downbeat' 'downtempo' 'drum' 'dub' 'dubstep' 'eastern'
 'easy' 'electronic' 'electropop' 'emo' 'entehno' 'epicmetal' 'estrada'
 'ethnic' 'eurofolk' 'european' 'experimental' 'extrememetal' 'fado'
 'fairytail' 'film' 'fitness' 'flamenco' 'folk' 'folklore' 'folkmetal'
 'folkrock' 'folktronica' 'forró' 'frankreich' 'französisch' 'french'
 'funk' 'future' 'gangsta' 'garage' 'german' 'ghazal' 'gitarre' 'glitch'
 'gospel' 'gothic' 'grime' 'grunge' 'gypsy' 'handsup' "hard'n'heavy"
 'hardcore' 'hardstyle' 'hardtechno' 'hiphop' 'historisch' 'holiday'
 'horror' 'house' 'hymn' 'idm' 'independent' 'indian' 'indie' 'indipop'
 'industrial' 'inspirational' 'instrumental' 'international' 'irish' 'jam'
 'japanese' 'jazz' 'jewish' 'jpop' 'jungle' 'k-pop' 'karadeniz' 'karaoke'
 'kayokyoku' 'korean' 'laiko' 'latin' 'latino' 'leftfield' 'local'
 'lounge' 'loungeelectronic' 'lovers' 'malaysian' 'mandopop' 'marschmusik'
 'meditative' 'mediterranean' 'melodic' 'metal' 'metalcore' 'mexican'
 'middle' 'minimal' 'miscellaneous' 'modern' 'mood' 'mpb' 'muslim'
 'native' 'neoklassik' 'neue' 'new' 'newage' 'newwave' 'nu' 'nujazz'
 'numetal' 'oceania' 'old' 'opera' 'orchestral' 'other' 'piano' 'podcasts'
 'pop' 'popdance' 'popelectronic' 'popeurodance' 'poprussian' 'post'
 'posthardcore' 'postrock' 'power' 'progmetal' 'progressive' 'psychedelic'
 'punjabi' 'punk' 'quebecois' 'ragga' 'ram' 'rancheras' 'rap' 'rave'
 'reggae' 'reggaeton' 'regional' 'relax' 'religious' 'retro' 'rhythm'
 'rnb' 'rnr' 'rock' 'rockabilly' 'rockalternative' 'rockindie' 'rockother'
 'romance' 'roots' 'ruspop' 'rusrap' 'rusrock' 'russian' 'salsa' 'samba'
 'scenic' 'schlager' 'self' 'sertanejo' 'shanson' 'shoegazing' 'showtunes'
 'singer' 'ska' 'skarock' 'slow' 'smooth' 'soft' 'soul' 'soulful' 'sound'
 'soundtrack' 'southern' 'specialty' 'speech' 'spiritual' 'sport'
 'stonerrock' 'surf' 'swing' 'synthpop' 'synthrock' 'sängerportrait'
 'tango' 'tanzorchester' 'taraftar' 'tatar' 'tech' 'techno' 'teen'
 'thrash' 'top' 'traditional' 'tradjazz' 'trance' 'tribal' 'trip'
 'triphop' 'tropical' 'türk' 'türkçe' 'ukrrock' 'urban' 'uzbek' 'variété'
 'vi' 'videogame' 'vocal' 'western' 'world' 'worldbeat' 'ïîï'
 'электроника' nan]
```
**Выводы**

Предобработка обнаружила три проблемы в данных:

- нарушения в стиле заголовков,
- пропущенные значения,
- дубликаты — явные и неявные.

Вы исправили заголовки, чтобы упростить работу с таблицей. Без дубликатов исследование станет более точным.

Пропущенные значения вы заменили на `'unknown'`. Ещё предстоит увидеть, не повредят ли исследованию пропуски в колонке `genre`.

Теперь можно перейти к проверке гипотез. 
## Проверка гипотез
### Сравнение поведения пользователей двух столиц
Первая гипотеза утверждает, что пользователи по-разному слушают музыку в Москве и Санкт-Петербурге. Проверьте это предположение по данным о трёх днях недели — понедельнике, среде и пятнице. Для этого:

* Разделите пользователей Москвы и Санкт-Петербурга.
* Сравните, сколько треков послушала каждая группа пользователей в понедельник, среду и пятницу.
**Задание 18**

Для тренировки сначала выполните каждый из расчётов по отдельности. 

Оцените активность пользователей в каждом городе. Сгруппируйте данные по городу и посчитайте прослушивания в каждой группе.
```
# Подсчёт прослушиваний в каждом городе

df_msk = df[df['city'] == 'Moscow'] # получим датафрейм пользователей в Москве
df_msk_1 = len(df_msk[df_msk['day'] == 'Monday']) # число треков в понедельник
df_msk_3 = len(df_msk[df_msk['day'] == 'Wednesday']) # число треков в среду
df_msk_5 = len(df_msk[df_msk['day'] == 'Friday']) # число треков в пятницу

df_spb = df[df['city'] == 'Saint-Petersburg'] # получим датафрейм пользователей в Санкт-Петербурге
df_spb_1 = len(df_spb[df_spb['day'] == 'Monday']) # число треков в понедельник
df_spb_3 = len(df_spb[df_spb['day'] == 'Wednesday']) # число треков в среду
df_spb_5 = len(df_spb[df_spb['day'] == 'Friday']) # число треков в пятницу

week = [
    ['Monday', df_msk_1, df_spb_1, df_msk_1 + df_spb_1],
    ['Wednesday', df_msk_3, df_spb_3, df_msk_3 + df_spb_3],
    ['Friday', df_msk_5, df_spb_5, df_msk_5 + df_spb_5],
    ['3 days', df_msk_1 + df_msk_3 + df_msk_5, df_spb_1 + df_spb_3 + df_spb_5, len(df)],
]

city = ['day', 'msk', 'spb', '2_city']

df_city = pd.DataFrame(data=week, columns=city) # датафрейм по двум городам
display(df_city)
```
|   |       day |   msk |   spb | 2_city |
|--:|----------:|------:|------:|-------:|
| 0 | Monday    | 16715 | 5982  | 22697  |
| 1 | Wednesday | 11755 | 7478  | 19233  |
| 2 | Friday    | 16890 | 6259  | 23149  |
| 3 | 3 days    | 45360 | 19719 | 65079  |
В Москве прослушиваний больше, чем в Петербурге. Из этого не следует, что московские пользователи чаще слушают музыку. Просто самих пользователей в Москве больше.

**Задание 19**

Теперь сгруппируйте данные по дню недели и посчитайте прослушивания в понедельник, среду и пятницу. Учтите, что в данных есть информация о прослушиваниях только за эти дни.
```
display(df_city) # Подсчёт прослушиваний в каждый из трёх дней
```
В среднем пользователи из двух городов менее активны по средам. Но картина может измениться, если рассмотреть каждый город в отдельности.
**Задание 20**


Вы видели, как работает группировка по городу и по дням недели. Теперь напишите функцию, которая объединит два эти расчёта.

Создайте функцию `number_tracks()`, которая посчитает прослушивания для заданного дня и города. Ей понадобятся два параметра:
* день недели,
* название города.

В функции сохраните в переменную строки исходной таблицы, у которых значение:
  * в колонке `day` равно параметру `day`,
  * в колонке `city` равно параметру `city`.

Для этого примените последовательную фильтрацию с логической индексацией (или сложные логические выражения в одну строку, если вы уже знакомы с ними).

Затем посчитайте значения в столбце `user_id` получившейся таблицы. Результат сохраните в новую переменную. Верните эту переменную из функции.
```
# <создание функции number_tracks()>
def number_tracks(day, city): # Объявляется функция с двумя параметрами: day, city.

# В переменной track_list сохраняются те строки таблицы df, для которых 
# значение в столбце 'day' равно параметру day и одновременно значение
# в столбце 'city' равно параметру city (используйте последовательную фильтрацию
# с помощью логической индексации или сложные логические выражения в одну строку, если вы уже знакомы с ними).
    track_list=df[(df['day'] == day) & (df['city'] == city)]

# В переменной track_list_count сохраняется число значений столбца 'user_id',
# рассчитанное методом count() для таблицы track_list.
    track_list_count = track_list['user_id'].count()
    return track_list_count # Функция возвращает число - значение track_list_count.


# Функция для подсчёта прослушиваний для конкретного города и дня.
# С помощью последовательной фильтрации с логической индексацией она 
# сначала получит из исходной таблицы строки с нужным днём,
# затем из результата отфильтрует строки с нужным городом,
# методом count() посчитает количество значений в колонке user_id. 
# Это количество функция вернёт в качестве результата
```
**Задание 21**

Вызовите `number_tracks()` шесть раз, меняя значение параметров — так, чтобы получить данные для каждого города в каждый из трёх дней.
```In[] number_tracks('Monday', 'Moscow') # количество прослушиваний в Москве по понедельникам```
Out[] 16715
In[] number_tracks('Monday', 'Saint-Petersburg') # количество прослушиваний в Санкт-Петербурге по понедельникам
Out[] 5982
In[] number_tracks('Wednesday', 'Moscow') # количество прослушиваний в Москве по средам
Out[] 11755
In[] number_tracks('Wednesday', 'Saint-Petersburg') # количество прослушиваний в Санкт-Петербурге по средам
Out[] 7478
In[] number_tracks('Friday', 'Moscow') # количество прослушиваний в Москве по пятницам
Out[] 16890
In[] number_tracks('Friday', 'Saint-Petersburg') # количество прослушиваний в Санкт-Петербурге по пятницам
Out[] 6259
```
