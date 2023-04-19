## Данные

Таблица `books`    
Содержит данные о книгах:

- `book_id` — идентификатор книги;
- `author_id` — идентификатор автора;
- `title` — название книги;
- `num_pages` — количество страниц;
- `publication_date` — дата публикации книги;
- `publisher_id` — идентификатор издателя.

Таблица `authors`    
Содержит данные об авторах:

- `author_id` — идентификатор автора;
- `author` — имя автора.

Таблица `publishers`    
Содержит данные об издательствах:

- `publisher_id` — идентификатор издательства;
- `publisher` — название издательства;

Таблица `ratings`    
Содержит данные о пользовательских оценках книг:

- `rating_id` — идентификатор оценки;
- `book_id` — идентификатор книги;
- `username` — имя пользователя, оставившего оценку;
- `rating` — оценка книги.

Таблица `reviews`    
Содержит данные о пользовательских обзорах:

- `review_id` — идентификатор обзора;
- `book_id` — идентификатор книги;
- `username` — имя автора обзора;
- `text` — текст обзора.

## Схема данных

![cover](https://github.com/vs-gorgan/practicum.yandex/blob/main/15_SQL/db.png)

## Задача

Провести оценку результатов A/B-теста

## Используемые библиотеки

*pandas*, *datetime*, *seaborn*, *matplotlib*, *numpy*, *plotly*, *math*, *scipy*

## Проект

[Ссылка](https://github.com/vs-gorgan/practicum.yandex/blob/main/14_AB-test_final/AB-test_final.ipynb)
