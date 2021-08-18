---
images:
  - /images/uploads/SQL6.3.jpg
title: Оператор PostgreSQL EXCEPT
slug: оператор-postgresql-except
date: 2019-08-05T01:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL6.3.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
об [операторе PostgreSQL **INTERSECT**](https://itdoxy.com/оператор-postgresql-intersect/). Теперь же мы углубимся
в изучение оператора **EXCEPT**, при помощи которого мы будем возвращать строки из первого запроса, которые не отображаются
в выводе второго.

## Введение в оператор PostgreSQL EXCEPT

Подобно операторам [**UNION**](https://itdoxy.com/оператор-postgresql-union/) и [**INTERSECT**](https://itdoxy.com/оператор-postgresql-intersect/),
оператор **EXCEPT** возвращает строки, сравнивая результирующие наборы двух или более запросов.

Оператор **EXCEPT** возвращает отдельные строки из первого (левого) запроса, которые не содержатся в выводе второго
(правого) запроса. Следующий запрос отображает синтаксис вызова оператора **EXCEPT**:

```
SELECT column_list
FROM A
WHERE condition_a
EXCEPT
SELECT column_list
FROM B
WHERE condition_b;
```

Для объединения запросов при помощи оператора **EXCEPT**, вы должны соблюдать следующие правила:

- Количество столбцов и их порядок должны быть одинаковыми в двух запросах.
- Типы данных соответствующих столбцов должны быть совместимы.

Следующая диаграмма Венна отображает результат работы оператора **EXCEPT**, который применяется к таблицам **A** и **B**:

![](https://i.imgur.com/4b3fFdY.png)

## Пример использования оператора PostgreSQL EXCEPT

Обратим внимание на таблицы **film** и **inventory** из [примера базы данных](https://itdoxy.com/пример-базы-данных-postgresql/).

Следующий запрос возвращает фильмы из таблицы **film**:

```
SELECT
    film_id,
    title
FROM
    film
ORDER BY
    title;
```

![](https://i.imgur.com/Bv1XEWa.png)

Следующий запрос возвращает фильмы, которые есть в прокате:

```
SELECT
    distinct inventory.film_id,
    title
FROM
    inventory
INNER JOIN film ON film.film_id = inventory.film_id
ORDER BY title;
```

![](https://i.imgur.com/JoJmv6B.png)

Оба запроса возвращают набор результатов, который состоит из двух столбцов: **film_id** и **title**.

Чтобы получить фильмы, которых нет в прокате, используйте оператора **EXCEPT** следующим образом:

```
SELECT
    film_id,
    title
FROM
    film
EXCEPT
    SELECT
        DISTINCT inventory.film_id,
        title
    FROM
        inventory
    INNER JOIN film ON film.film_id = inventory.film_id
ORDER BY title;
```

![](https://i.imgur.com/M3lLdQf.png)

Обратите внимание, что мы поместили условие [**ORDER BY**](https://itdoxy.com/условие-postgresql-order-by/) в конец
оператора, чтобы отсортировать фильмы по их названиям. Если вы поместите условие **ORDER BY** в каждый запрос, окончательный
результат может быть не отсортирован, поскольку каждый запрос будет сортировать набор результатов по столбцу заголовка,
и после этого оператор **EXCEPT** будет применен к обоим запросам.

На этом мы завершаем изучение главы, посвящённой выполнению операций над множествами, и двигаемся дальше, к группировке наборов.

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
