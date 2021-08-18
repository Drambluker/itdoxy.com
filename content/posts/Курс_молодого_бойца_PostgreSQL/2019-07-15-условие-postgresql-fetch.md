---
images:
  - /images/uploads/SQL3.3.jpg
title: Условие PostgreSQL FETCH
slug: условие-postgresql-fetch
date: 2019-07-15T01:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL3.3.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили об
[условии PostgreSQL **LIMIT**](https://itdoxy.com/условие-postgresql-limit/). Теперь же мы углубимся в изучение условия
**FETCH**, используемого для получения части строк, возвращаемых запросом.

## Введение в условие PostgreSQL FETCH

Чтобы ограничить количество строк, возвращаемых запросом, зачастую прибегают к условию **LIMIT**, которое широко
используется во многих системах управления реляционными базами данных, таких как MySQL, H2 и HSQLDB. Однако, условие
**LIMIT** не является стандартом SQL.

Чтобы соответствовать стандарту SQL, PostgreSQL предоставляет условие **FETCH** для получения части строк, возвращаемых
запросом. Обратите внимание, что условие **FETCH** было введено в SQL:2008.

Углубимся в синтаксис условия **FETCH**:

```
OFFSET start { ROW | ROWS }
FETCH { FIRST | NEXT } [ row_count ] { ROW | ROWS } ONLY
```

Рассмотрим запрос детальнее:

- **ROW** и **FIRST** синонимичны с **ROWS** и **NEXT** соответственно.
- Ключевое слово **start** — это целое число, которое должно быть больше или равно нуля. Изначально (при не выставленном
  значении условия **OFFSET**) оно равно нулю. В случае, если **start** больше, чем число строк в базовом наборе результатов,
  строки не возвращаются.
- Ключевое слово **row_count** больше или равно единице. Изначально, значение **row_count** равно единице.

В связи с тем, что порядок строк, хранящихся в таблице, непредсказуем, вам необходимо использовать условие **FETCH**
с условием **ORDER BY**, чтобы сделать набор результатов согласованным.

Обратите внимание, что в SQL:2008 условие **OFFSET** должно стоять перед условием **FETCH**. Однако, в PostgreSQL они
могут располагаться в любом порядке.

## Примеры использования PostgreSQL FETCH

Для наглядности будем использовать таблицу **film** из [примера базы данных](https://itdoxy.com/пример-базы-данных-postgresql/).

![](https://i.imgur.com/1AcDyRh.png)

Следующий запрос возвращает первую строку **film**, отсортированную по названию:

```
SELECT
    film_id,
    title
FROM
    film
ORDER BY
    title
FETCH FIRST ROW ONLY;
```

![](https://i.imgur.com/cuE9Wx4.png)

Что эквивалентно следующему запросу:

```
SELECT
    film_id,
    title
FROM
    film
ORDER BY
    title
FETCH FIRST 1 ROW ONLY;
```

Запрос, возвращающий первые пять фильмов, отсортированных по названию:

```
SELECT
    film_id,
    title
FROM
    film
ORDER BY
    title
FETCH FIRST 5 ROW ONLY;
```

![](https://i.imgur.com/IE8lDzN.png)

Данный запрос возвращает следующие пять фильмов, отсортированных по названию, после первых пяти фильмов, указанных выше:

```
SELECT
    film_id,
    title
FROM
    film
ORDER BY
    title
OFFSET 5 ROWS
FETCH FIRST 5 ROW ONLY;
```

![](https://i.imgur.com/MKrpCVC.png)

Рассмотрев условие PostgreSQL **FETCH**, мы можем двигаться к следующему руководству, в котором вы узнаете, как использовать
[оператор **IN**](https://itdoxy.com/оператор-postgresql-in/) в условии **WHERE** для проверки списка значений.

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
