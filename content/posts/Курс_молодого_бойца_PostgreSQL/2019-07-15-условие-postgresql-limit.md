---
images:
  - /images/uploads/SQL3.2.jpg
title: Условие PostgreSQL LIMIT
slug: условие-postgresql-limit
date: 2019-07-15T00:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL3.2.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили об
[условии PostgreSQL **WHERE**](https://itdoxy.com/условие-postgresql-where/). Теперь мы углубимся в изучение условия
**LIMIT**, используемого для получения подмножества строк, сгенерированных запросом.

## Введение в условие LIMIT

PostgreSQL **LIMIT** — это необязательное условие оператора [**SELECT**](https://itdoxy.com/оператор-postgresql-select/),
которое возвращает подмножество строк, возвращаемых запросом.

Углубимся в синтаксис условия **LIMIT**:

```
SELECT
    *
FROM
    table_name
LIMIT n;
```

Оператор возвращает **n** строк, сгенерированных запросом. Если **n** равно нулю, то запрос вернёт пустой набор результатов.
В случае, если **n** равно **NULL**, запрос вернёт такой набор результатов, как если бы мы опустили условие **LIMIT**.

В случае если вам необходимо пропустить m строк перед возвратом **n** строк, используйте условие **OFFSET** после условия
**LIMIT**, как показано ниже:

```
SELECT
    *
FROM
    table
LIMIT n OFFSET m;
```

Первым делом оператор пропускает **m** строк, а затем возвращает **n** строк, сгенерированных запросом. Если **m** равно
нулю, оператор выведет результат, как если бы мы опустили условие **OFFSET**.

В связи с тем, что в таблице базы данных не определён порядок строк, при использовании условия **LIMIT** всегда следует
использовать условие [**ORDER BY**](https://itdoxy.com/условие-postgresql-order-by/) для управления порядком строк.
В противном случае вы получите результирующий набор, строки которого находятся в неопределённом порядке.

Неэффективным может быть использование большого значения **OFFSET**, поскольку PostgreSQL всё также должен вычислять
строки, пропущенные условием **OFFSET** внутри сервера базы данных, даже если пропущенные строки не возвращаются.

## Примеры использования PostgreSQL LIMIT

Давайте рассмотрим несколько примеров, чтобы лучше разобраться в использовании условия PostgreSQL **LIMIT**. Для наглядности
мы будем использовать таблицу **film** из [примера базы данных](https://itdoxy.com/пример-базы-данных-postgresql/).

![](https://i.imgur.com/QYPleO1.png)

В этом примере условие **LIMIT** используется, чтобы найти первые 5 фильмов, отсортированных по **film_id**:

```
SELECT
    film_id,
    title,
    release_year
FROM
    film
ORDER BY
    film_id
LIMIT 5;
```

![](https://i.imgur.com/QNRKXZB.png)

Чтобы получить 4 фильма, начиная с третьего, отсортированного с помощью **film_id**, используйте оба условия **LIMIT**
и **OFFSET**, как показано ниже:

```
SELECT
    film_id,
    title,
    release_year
FROM
    film
ORDER BY
    film_id
LIMIT 4 OFFSET 3;
```

![](https://i.imgur.com/UihKHMH.jpg)

Зачастую условие **LIMIT** используется для того, чтобы получить самые верхние и самые нижние элементы в таблице.
Например, чтобы вывести десятку самых дорогих фильмов, вам необходимо отсортировать фильмы по рейтингу проката в порядке
убывания, а также использовать условие **LIMIT**, чтобы получить первые 10 фильмов. Всё это отображает следующий запрос:

```
SELECT
    film_id,
    title,
    rental_rate
FROM
    film
ORDER BY
    rental_rate DESC
LIMIT 10;
```

![](https://i.imgur.com/HkkHxmX.jpg)

На этом ознакомление с условием **LIMIT**, позволяющим получать подмножества строк, сгенерированных запросом, подошло
к концу, а в следующий раз мы поговорим об [условии **FETCH**](https://itdoxy.com/условие-postgresql-fetch/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
