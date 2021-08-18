---
images:
  - /images/uploads/SQL3.8.jpg
title: Псевдонимы в PostgreSQL
slug: псевдонимы-в-postgresql
date: 2019-07-17T01:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL3.8.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
об [операторе PostgreSQL **IS NULL**](https://itdoxy.com/оператор-postgresql-is-null/). Теперь же мы углубимся в изучение
псевдонимов столбцов и таблиц в PostgreSQL.

Псевдоним в PostgreSQL назначает таблице или столбцу в запросе временное имя и существует только во время выполнения запроса.

## Псевдоним столбца

Ниже показан синтаксис псевдонима столбца:

```
SELECT column_name AS alias_name
FROM table;
```

В этом синтаксисе **column_name** назначается псевдоним **alias_name**. Ключевое слово **AS** является необязательным,
поэтому вы можете пропустить его, как показано ниже:

```
SELECT column_name alias_name
FROM table;
```

Помимо имени столбца, вы можете назначить псевдоним выражению в операторе [**SELECT**](https://itdoxy.com/оператор-postgresql-select/)
следующим образом:

```
SELECT expression alias_name
FROM table;
```

Основное назначение псевдонима столбца — сделать вывод запроса более осмысленным.

## Примеры псевдонимов столбцов

Следующий запрос находит полные имена всех клиентов:

```
SELECT
    first_name || ' ' || last_name
FROM
    customer
ORDER BY
    first_name || ' ' || last_name;=
```

![](https://i.imgur.com/wdhoZ2W.png)

Чтобы сделать запрос коротким и его вывод более осмысленным, вы можете использовать псевдонимы столбцов следующим образом.

```
SELECT
    first_name || ' ' || last_name AS full_name
FROM
    customer
ORDER BY
    full_name;
```

Поскольку PostgreSQL учитывает условие [**ORDER BY**](https://itdoxy.com/условие-postgresql-order-by/) после оператора
**SELECT**, вы можете использовать псевдоним столбца в условии **ORDER BY**.

Для других условий, учитываемых до оператора **SELECT**, таких, как [**WHERE**](https://itdoxy.com/условие-postgresql-where/),
**GROUP BY** и **HAVING**, вы не можете ссылаться на псевдоним столбца внутри условия.

## Псевдоним таблицы

Ниже показан синтаксис псевдонима таблицы:

```
SELECT
    column_list
FROM
    table_name AS alias_name;
```

Подобно псевдониму столбца, ключевое слово **AS** в синтаксисе псевдонима таблицы также может быть опущено.
Обратите внимание, что вы также можете использовать псевдоним таблицы для представления.

Псевдоним таблицы имеет несколько применений.

Во-первых, если вам нужно указать имя столбца с длинным именем таблицы, вы можете использовать псевдоним таблицы,
чтобы сократить количество нажатий и сделать ваш запрос более читабельным.

Например, вместо использования следующего запроса:

```
SELECT a_very_long_table_name.column_name
FROM a_very_long_table_name;
```

Вы можете использовать псевдоним таблицы, как показано ниже:

```
SELECT t.column_name
FROM a_very_long_table_name t;
```

В этом примере **t** является псевдонимом таблицы **a_very_long_table_name**.

Практическое использование псевдонима таблицы — это когда вы запрашиваете данные из нескольких таблиц с одинаковыми
именами столбцов. В этом случае вы должны сопоставить столбцы, используя имена таблиц следующим образом:

```
SELECT table_name1.column_name,
    table_name2.column_name
FROM table_name1
INNER JOIN table_name2 ON join_predicate;
```

Чтобы сделать запрос короче, вы можете использовать псевдонимы таблиц для их имен, перечисленных в условиях **FROM**
и **INNER JOIN**:

```
SELECT t1.column_name,
    t2.column_name
FROM table_name1 t1
INNER JOIN table_name2 t2 ON join_predicate;
```

Об условии **INNER JOIN** вы узнаете в следующем уроке.

Во-вторых, когда вы объединяете таблицу с ней же самой, то есть делаете самообъединение, вы должны использовать псевдонимы
таблиц, потому что PostgreSQL не позволяет вам ссылаться на одну и ту же таблицу несколько раз в запросе.

```
SELECT
    colum_list
FROM
    table_name table_alias
INNER JOIN table_name ON join_predicate;
```

В данной статье вы узнали, как использовать псевдонимы в PostgreSQL для временного назначения нового имени столбцу или
таблице во время выполнения запроса. В следующей статье мы поговорим об [объединениях в PostgreSQL](https://itdoxy.com/объединения-в-postgresql/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
