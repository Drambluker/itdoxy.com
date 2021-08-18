---
images:
  - /images/uploads/SQL2.3.jpg
title: Оператор PostgreSQL SELECT DISTINCT
slug: оператор-postgresql-select-distinct
date: 2019-07-12T00:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL2.3.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы ознакомились с условием PostgreSQL
[**ORDER BY**](https://itdoxy.com/условие-postgresql-order-by/). Теперь перейдем к изучению оператора PostgreSQL
**SELECT DISTINCT**, который предназначен для удаления дублирующихся строк из набора результатов, возвращаемых запросом.

## Введение в оператор PostgreSQL SELECT DISTINCT

Условие **DISTINCT** используется с оператором [**SELECT**](https://itdoxy.com/оператор-postgresql-select/) для удаления
повторяющихся строк из набора результатов. Условие **DISTINCT** сохраняет только одну строку для каждой группы дубликатов.
Также условие **DISTINCT** может использоваться с одним или несколькими столбцами таблицы.

Ниже приведен синтаксис оператора **SELECT DISTINCT**:

```
SELECT
  DISTINCT column_1
FROM
  table_name;
```

В этом операторе значения столбца **column_1** используются для оценки дубликата.

Если вы укажите несколько столбцом, условие **DISTINCT** будет оценивать дубликаты на основе комбинации значений этих столбцов.

```
SELECT
  DISTINCT column_1, column_2
FROM
  table_name;
```

В этом случае комбинация значений **column_1** и **column_2** будет использоваться для оценки дубликата.

PostgreSQL также предоставляет DISTINCT ON (expression) для сохранения “первой” строки каждой группы дубликатов,
используя следующий синтаксис:

```
SELECT
  DISTINCT ON (column_1) column_alias,
  column_2
FROM
  table_name
ORDER BY
  column_1,
  column_2;
```

Порядок строк, возвращаемый оператором **SELECT** непредсказуем, поэтому “первая” строка каждой группы дубликатов также
непредсказуема. Рекомендуется всегда использовать условие [**ORDER BY**](https://itdoxy.com/условие-postgresql-order-by/)
вместе с **DISTINCT ON(expression)**, чтобы сделать результирующий набор очевидным.

Обратите внимание, что выражение **DISTINCT ON** должно соответствовать крайнему левому выражению в условии **ORDER BY**.

## Примеры использования SELECT DISTINCT в PostgreSQL

Давайте создадим новую таблицу с именем **t1** и поместим в нее данные для практики использования условия **DISTINCT**.

Во-первый, воспользуйтесь следующей инструкцией для создания таблицы **t1**, которая будет состоять из трех столбцов:
**id**, **bcolor** и **fcolor**.

```
CREATE TABLE t1 (
  id serial NOT NULL PRIMARY KEY,
  bcolor VARCHAR,
  fcolor VARCHAR
);
```

Во-вторых, вставьте несколько строк в таблицу **t1** используя оператор **INSERT**:

```
INSERT INTO t1 (bcolor, fcolor)
VALUES
  ('red', 'red'),
  ('red', 'red'),
  ('red', NULL),
  (NULL, 'red'),
  ('red', 'green'),
  ('red', 'blue'),
  ('green', 'red'),
  ('green', 'blue'),
  ('green', 'green'),
  ('blue', 'red'),
  ('blue', 'green'),
  ('blue', 'blue');
```

В-третьих, запросите данные из таблицы при помощи оператора **SELECT**:

```
SELECT
  id,
  bcolor,
  fcolor
FROM
  t1;
```

![](https://i.imgur.com/FOUhl0d.png)

## PostgreSQL DISTINCT на примере одного столбца

Следующая инструкция выбирает уникальные значения столбца **bcolor** из таблицы **t1** и сортирует результирующий набор
в алфавитном порядке, используя условие [**ORDER BY**](https://itdoxy.com/условие-postgresql-order-by/).

```
SELECT
  DISTINCT bcolor
FROM
  t1
ORDER BY
  bcolor;
```

![](https://i.imgur.com/DXzvTuo.png)

## PostgreSQL DISTINCT на примере нескольких столбцов

Следующий оператор демонстрирует, как использовать условие **DISTINCT** для нескольких столбцов:

```
SELECT
  DISTINCT bcolor,
  fcolor
FROM
  t1
ORDER BY
  bcolor,
  fcolor;
```

![](https://i.imgur.com/e3rCkJH.png)

Поскольку мы указали **bcolor** и **fcolor** в операторе **SELECT DISTINCT**, PostgreSQL объединил значения, содержащиеся
в **bcolor** и **fcolor**, чтобы оценить уникальность строк.

Запрос возвращает уникальную комбинацию **bcolor** и **fcolor** из таблицы **t1**. Обратите внимание, что таблица имеет
две строки со значением **red** в **bcolor** и **fcolor**. Когда мы применили **DISTINCT** к обоим столбцам, из результирующего
набора была удалена одна строка, поскольку она является дубликатом.

## Пример PostgreSQL DISTINCT ON

Следующая инструкция сортирует набор результатов по **bcolor** и **fcolor**, а затем для каждой группы дубликатов сохраняет
первую строку в возвращенном наборе результатов.

```
SELECT
  DISTINCT ON
  (bcolor) bcolor,
  fcolor
FROM
  t1
ORDER BY
  bcolor,
  fcolor;
```

Вывод:

![](https://i.imgur.com/XQw3XTA.png)

Следующее изображение иллюстрирует процесс:

![](https://i.imgur.com/g1H9ozD.png)

В этом руководстве мы ознакомились с тем, как использовать оператор PostgreSQL **SELECT DISTINCT** для удаления
дублирующихся строк из набора результатов, возвращаемых запросом. В следующем уроке вы узнаете, как использовать условие
[**WHERE**](https://itdoxy.com/условие-postgresql-where/) для фильтрации строк, возвращаемых оператором **SELECT** в PostgreSQL.

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
