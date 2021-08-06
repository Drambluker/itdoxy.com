---
title: "Оператор PostgreSQL INTERSECT"
slug: "оператор-postgresql-intersect"
date: 2019-08-01T00:00:00+03:00
categories: ["Базы данных", "PostgreSQL"]
tags: ["Курс молодого бойца PostgreSQL"]
draft: false
---

![](/posts/оператор-postgresql-intersect/SQL6.2.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
об [операторе PostgreSQL **UNION**](https://itdoxy.com/оператор-postgresql-union/). Теперь же мы углубимся в изучение
оператора **INTERSECT**, при помощи которого мы будем объединять двух или более операторов **SELECT** в один набор результатов.

## Введение в оператор PostgreSQL INTERSECT

Подобно операторам **UNION** и **EXCEPT**, оператор PostgreSQL **INTERSECT** объединяет наборы результатов двух или более
операторов [**SELECT**](https://itdoxy.com/оператор-postgresql-select/) в один.

Следующая диаграмма отображает окончательный набор результатов, созданный оператором **INTERSECT**. Непосредственно
конечный набор результатов представлен желтой областью, где круг A пересекается с кругом B.

![](https://i.imgur.com/lVs5zAa.png)

Ниже показан синтаксис вызова оператора **INTERSECT**:

```
SELECT
    column_list
FROM
    A
INTERSECT
SELECT
    column_list
FROM
    B;
```

Чтобы использовать оператор **INTERSECT**, столбцы, которые появляются в операторах **SELECT**, должны соответствовать
следующим правилам:

- Количество столбцов и их порядок в предложениях **SELECT** должны быть одинаковыми.
- Типы данных столбцов должны быть совместимы.

## Примеры использования оператора PostgreSQL INTERSECT

Давайте создадим несколько таблиц для наглядности.

Следующий оператор **CREATE TABLE** создаёт три таблицы: **employees**, **keys** и **hipos**.

```
CREATE TABLE employees (
    employee_id serial PRIMARY KEY,
    employee_name VARCHAR (255) NOT NULL
);

CREATE TABLE keys (
    employee_id INT PRIMARY KEY,
    effective_date DATE NOT NULL,
    FOREIGN KEY (employee_id) REFERENCES employees (employee_id)
);

CREATE TABLE hipos (
    employee_id INT PRIMARY KEY,
    effective_date DATE NOT NULL,
    FOREIGN KEY (employee_id) REFERENCES employees (employee_id)
);
```

В таблице **employees** хранятся основные данные сотрудников. В таблице **keys** хранятся ключевые сотрудники,
а в **hipos** — сотрудники с высоким потенциалом и высокой эффективностью.

Следующие операторы **INSERT** вставляют некоторые данные в таблицы **employees**, **keys** и **hipos**:

```
INSERT INTO employees (employee_name)
VALUES
    ('Joyce Edwards'),
    ('Diane Collins'),
    ('Alice Stewart'),
    ('Julie Sanchez'),
    ('Heather Morris'),
    ('Teresa Rogers'),
    ('Doris Reed'),
    ('Gloria Cook'),
    ('Evelyn Morgan'),
    ('Jean Bell');

INSERT INTO keys
VALUES
    (1, '2000-02-01'),
    (2, '2001-06-01'),
    (5, '2002-01-01'),
    (7, '2005-06-01');

INSERT INTO hipos
VALUES
    (9, '2000-01-01'),
    (2, '2002-06-01'),
    (5, '2006-06-01'),
    (10, '2005-06-01');
```

Следующий запрос возвращает ключевых сотрудников из таблицы **keys**:

```
SELECT
    employee_id
FROM
    keys;
```

```
 employee_id
-------------
           1
           2
           5
           7
(4 rows)
```

Ниже представлен запрос, возвращающий список **employee_id** из таблицы **hipos**:

```
SELECT
    employee_id
FROM hipos;
```

```
 employee_id
-------------
           9
           2
           5
          10
(4 rows)
```

Чтобы получить сотрудников, которые являются одновременно и ключевыми и “hipo”, используем следующий запрос:

```
SELECT
    employee_id
FROM
    keys
INTERSECT
SELECT
    employee_id
FROM
    hipos;
```

```
 employee_id
-------------
           5
           2
(2 rows)
```

Результирующий набор показывает, что сотрудники с ID 2 и 5 удовлетворяют данному условию.

Чтобы отсортировать набор результатов, возвращаемый оператором **INTERSECT**, вам необходимо поместить условие
[**ORDER BY**](https://itdoxy.com/условие-postgresql-order-by/) в конец оператора, но никак не в конец каждого оператора
**SELECT**. Ниже представлен такой запрос:

```
SELECT
    employee_id
FROM
    keys
INTERSECT
SELECT
    employee_id
FROM
    hipos
ORDER BY employee_id;
```

```
 employee_id
-------------
           2
           5
(2 rows)
```

На этом мы заканчиваем изучение оператора PostgreSQL **INTERSECT** и переходим к изучению [оператора **EXCEPT**](https://itdoxy.com/оператор-postgresql-except/),
использовать который мы будем для возврата строк в первом запросе, не отображающихся в выходных данных второго запроса.

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
