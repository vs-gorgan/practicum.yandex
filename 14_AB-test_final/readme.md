## Данные

`[ab_project_marketing_events.csv](https://code.s3.yandex.net/datasets/ab_project_marketing_events.csv)     
[final_ab_new_users.csv](https://code.s3.yandex.net/datasets/final_ab_new_users.csv)    
[final_ab_events.csv](https://code.s3.yandex.net/datasets/final_ab_events.csv)    
[final_ab_participants.csv](https://code.s3.yandex.net/datasets/final_ab_participants.csv)    

`/datasets/ab_project_marketing_events.csv` — календарь маркетинговых событий на 2020 год;     
    Структура файла:
      
- `name` — название маркетингового события;
- `regions` — регионы, в которых будет проводиться рекламная кампания;
- `start_dt` — дата начала кампании;
- `finish_dt` — дата завершения кампании.

`/datasets/final_ab_new_users.csv` — все пользователи, зарегистрировавшиеся в интернет-магазине в период с 7 по 21 декабря 2020 года;
    Структура файла:
- `user_id` — идентификатор пользователя;
- `first_date` — дата регистрации;
- `region` — регион пользователя;
- `device` — устройство, с которого происходила регистрация.

`/datasets/final_ab_events.csv` — все события новых пользователей в период с 7 декабря 2020 по 4 января 2021 года;    
    Структура файла:
- `user_id` — идентификатор пользователя;
- `event_dt` — дата и время события;
- `event_name` — тип события;
- `details` — дополнительные данные о событии. Например, для покупок, purchase, в этом поле хранится стоимость покупки в долларах.

`/datasets/final_ab_participants.csv` — таблица участников тестов.    
    Структура файла:
- `user_id` — идентификатор пользователя;
- `ab_test` — название теста;
- `group` — группа пользователя.

## Задача

Провести оценку результатов A/B-теста

## Используемые библиотеки

*pandas*, *datetime*, *seaborn*, *matplotlib*, *numpy*, *plotly*, *math*, *scipy*

## Проект

[Ссылка](https://github.com/vs-gorgan/practicum.yandex/blob/main/14_AB-test_final/AB-test_final.ipynb)
