---
images:
  - /images/uploads/SQL7.2.jpg
title: Подкласс PostgreSQL CUBE
slug: подкласс-postgresql-cube
date: 2019-08-13T00:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL7.2.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
об [условии PostgreSQL **GROUPING SETS**](https://itdoxy.com/условие-postgresql-grouping-sets/). Теперь же мы углубимся
в изучение подкласса **CUBE**, который используется для создания нескольких групповых наборов.

## Введение в подкласс PostgreSQL CUBE

PostgreSQL **CUBE** является подклассом условия [**GROUP BY**](https://itdoxy.com/условие-postgresql-group-by/)
и позволяет генерировать несколько групповых наборов.

Групповой набор — это набор столбцов, по которым осуществляется группировка. Для получения дополнительной информации
о группировках, советуем изучить [следующее руководство](https://itdoxy.com/условие-postgresql-grouping-sets/).

Запрос ниже отображает синтаксис вызова подкласса **CUBE**:

```
SELECT
    c1,
    c2,
    c3,
    aggregate (c4)
FROM
    table_name
GROUP BY
    CUBE (c1, c2, c3);
```

Детальнее рассмотрим данный вызов:

- Во-первых, мы указываем подкласс **CUBE** в условии [**GROUP BY**](https://itdoxy.com/условие-postgresql-group-by/)
оператора [**SELECT**](https://itdoxy.com/оператор-postgresql-select/).
- Во-вторых, в списке выбора мы указываем столбцы (измерения или столбцы измерений), которые вам необходимо проанализировать,
а также агрегированные выражения функций.
- В-третьих, в условии **GROUP BY** мы указываем столбцы измерения в скобках подкласса **CUBE**.

Запрос генерирует все возможные групповые наборы на основе столбцов измерения, указанных в **CUBE**.

Использование подкласса **CUBE** — это простой способ позволяющий определить несколько групповых наборов, поэтому
следующие условия эквивалентны:

```
CUBE(c1,c2,c3)

GROUPING SETS (
    (c1,c2,c3),
    (c1,c2),
    (c1,c3),
    (c2,c3),
    (c1),
    (c2),
    (c3),
    ()
)
```

В общем, если количество столбцов, указанное в **CUBE**, равно **n**, то у вас будет **2^n** комбинаций.

Чтобы уменьшить количество вычисляемых агрегатов, PostgreSQL позволяет вам выполнить частичный запрос **CUBE**.
Ниже показан синтаксис:

```
SELECT
    c1,
    c2,
    c3,
    aggregate (c4)
FROM
    table_name
GROUP BY
    c1,
    CUBE (c1, c2);
```

## Примеры использования подкласса PostgreSQL CUBE

Для наглядности мы воспользуемся таблицей **sales** из [урока по **GROUPING SETS**](https://itdoxy.com/условие-postgresql-grouping-sets/).

![](https://i.imgur.com/Q5B7jIW.png)

Следующий запрос использует подкласс **CUBE** для создания нескольких групповых наборов:

```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    CUBE (brand, segment)
ORDER BY
    brand,
    segment;
```

Пример вывода:

![](https://i.imgur.com/Y6954vd.png)

Следующий запрос выполняет частичный запрос **CUBE**:

```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand,
    CUBE (segment)
ORDER BY
    brand,
    segment;
```

![](https://i.imgur.com/RHUJQ9x.png)

На этом мы заканчиваем изучение подкласса **CUBE** и переходим к подклассу **ROLLUP**.

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
