---
images:
  - /images/uploads/SQL4.5.jpg
title: Полное внешнее объединение в PostgreSQL
slug: полное-внешнее-объединение-в-postgresql
date: 2019-07-23T00:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL4.5.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
о [самообъединении PostgreSQL](https://itdoxy.com/самостоятельное-объединение-в-postgresql/). Теперь же мы углубимся
в изучение полного внешнего объединения, именуемого **FULL OUTER JOIN**, при помощи которого мы будем запрашивать данные
из двух или более таблиц.

## Введение в оператор PostgreSQL FULL OUTER JOIN

Предположим, что вам необходимо выполнить полное внешнее объединение двух таблиц: **A** и **B**. Следующий запрос
отображает синтаксис вызова оператора **FULL OUTER JOIN**:

```
SELECT * FROM A
FULL [OUTER] JOIN B on A.id = B.id;
```

Ключевое слово **OUTER** не является обязательным.

**FULL OUTER JOIN** объединяет результаты левого и правого объединений. Если строки в объединённой таблице не совпадают,
полное внешнее объединение устанавливает значения **NULL** для каждого столбца таблицы, в которой отсутствует совпадающая
строка. Для совпадающих строк в результирующий набор включается одна строка, которая содержит столбцы, заполненные данными
из обеих объединённых таблиц.

Следующая диаграмма Венна отображает, как работает оператор **FULL OUTER JOIN**:

![](https://i.imgur.com/YeKTGw6.png)

Вывод включает в себя как совпадающие, так и несовпадающие строки из обеих таблиц.

## Пример использования оператора PostgreSQL FULL OUTER JOIN PostgreSQL

Первым делом, для наглядности, мы создадим две новые таблицы: сотрудники и отделы.

```
CREATE TABLE
IF NOT EXISTS departments (
    department_id serial PRIMARY KEY,
    department_name VARCHAR (255) NOT NULL
);

CREATE TABLE
IF NOT EXISTS employees (
    employee_id serial PRIMARY KEY,
    employee_name VARCHAR (255),
    department_id INTEGER
);
```

Каждый отдел может иметь как множество работников, так и ни одного, принадлежащего либо не принадлежащего к какому-либо отделу.

Воспользуемся оператором **INSERT**, чтобы занести данные в таблицы **departments** (отделы) и **employees** (сотрудники).

```
INSERT INTO departments (department_name)
VALUES
    ('Sales'),
    ('Marketing'),
    ('HR'),
    ('IT'),
    ('Production');

INSERT INTO employees (
    employee_name,
    department_id
)
VALUES
    ('Bette Nicholson', 1),
    ('Christian Gable', 1),
    ('Joe Swank', 2),
    ('Fred Costner', 3),
    ('Sandra Kilmer', 4),
    ('Julia Mcqueen', NULL);
```

Затем запросим данные из таблиц **departments** и **employees**:

```
# SELECT * FROM departments;
department_id | department_name
--------------+-----------------
            1 | Sales
            2 | Marketing
            3 | HR
            4 | IT
            5 | Production
(5 rows)
```

```
# SELECT * FROM employees;
employee_id |  employee_name  | department_id
------------+-----------------+---------------
          1 | Bette Nicholson |             1
          2 | Christian Gable |             1
          3 | Joe Swank       |             2
          4 | Fred Costner    |             3
          5 | Sandra Kilmer   |             4
          6 | Julia Mcqueen   |
(6 rows)
```

После чего используем оператор **FULL OUTER JOIN** для запроса данных из обеих таблиц **employees** и **departments**.

```
SELECT
    employee_name,
    department_name
FROM
    employees e
FULL OUTER JOIN departments d ON d.department_id = e.department_id;
```

Набор результатов включает в себя каждого сотрудника, принадлежащего отделу, и каждый отдел, содержащий сотрудника.
Кроме того, он включает каждого сотрудника, который не принадлежит ни одному отделу, и каждый отдел, который не содержит
ни одного сотрудника.

```
employee_name   | department_name
----------------+-----------------
Bette Nicholson | Sales
Christian Gable | Sales
Joe Swank       | Marketing
Fred Costner    | HR
Sandra Kilmer   | IT
Julia Mcqueen   | NULL
NULL            | Production
(7 rows)
```

Чтобы найти отдел, который не имеет в подчинении какого-либо сотрудника, используйте условие **WHERE** следующим образом:

```
SELECT
    employee_name,
    department_name
FROM
    employees e
FULL OUTER JOIN departments d ON d.department_id = e.department_id
WHERE
    employee_name IS NULL;
```

```
employee_name | department_name
--------------+-----------------
NULL          | Production
(1 row)
```

Из вывода видно, что отдел **Production** не содержит каких-либо сотрудников.

Чтобы найти сотрудника, который не принадлежит к какому-либо отделу, в условии **WHERE** мы проверяем **department_name**
на значение **NULL** следующим образом:

```
SELECT
    employee_name,
    department_name
FROM
    employees e
FULL OUTER JOIN departments d ON d.department_id = e.department_id
WHERE
    department_name IS NULL;
```

```
employee_name | department_name
--------------+-----------------
Julia Mcqueen | NULL
(1 row)
```

Как мы видим, **Julia Mcqueen** не принадлежит какому-либо отделу.

На этом мы заканчиваем ознакомление с полным внешним объединением в PostgreSQL и переходим к изучению
[перекрёстного объединения](https://itdoxy.com/перекрёстное-объединение-в-postgresql/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
