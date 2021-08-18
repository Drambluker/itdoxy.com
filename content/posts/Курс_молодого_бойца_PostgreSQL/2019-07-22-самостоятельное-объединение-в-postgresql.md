---
images:
  - /images/uploads/SQL4.4.jpg
title: Самостоятельное объединение в PostgreSQL
slug: самостоятельное-объединение-в-postgresql
date: 2019-07-22T00:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL4.4.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
о [левом объединении PostgreSQL](https://itdoxy.com/левое-объединение-в-postgresql/). Теперь же мы углубимся в изучение
самообъединения, именуемого **Self-Join**, при помощи которого мы будем сравнивать строки в одной и той же таблице.

## Введение в запрос self-join в PostgreSQL

**Self-join** — это запрос, в котором таблица объединяется сама с собой. Самообъединение необходимо для сравнения значений
в столбце строк в одной и той же таблице.

Чтобы сформировать запрос self-join, вы указываете одну и ту же таблицу дважды под разными псевдонимами, настраиваете
сравнение и исключаете случаи, когда значение будет равно самому себе.

Следующий запрос отображает, как таблица **А** объединяется сама с собой:

```
SELECT column_list
FROM A a1
INNER JOIN A b1 ON join_predicate;
```

Объединение происходит посредством условия [**INNER JOIN**](https://itdoxy.com/внутреннее-объединение-в-postgresql/).
Обратите внимание, что вы также можете использовать условия [**LEFT JOIN**](https://itdoxy.com/левое-объединение-в-postgresql/)
или **RIGHT JOIN**.

## Примеры использования запроса self-join в PostgreSQL

### 1) Пример запроса к иерархическим данным

Для наглядности воспользуемся примером таблицы.

Предположим, что у нас есть следующая структура организации:

![](https://i.imgur.com/v3Zvkrm.png)

Следующий SQL-скрипт создаёт таблицу **employee** (сотрудников) и заносит в неё некоторые данные:

```
CREATE TABLE employee (
  employee_id INT PRIMARY KEY,
  first_name VARCHAR (255) NOT NULL,
  last_name VARCHAR (255) NOT NULL,
  manager_id INT,
  FOREIGN KEY (manager_id)
  REFERENCES employee (employee_id)
  ON DELETE CASCADE
);
INSERT INTO employee (
  employee_id,
  first_name,
  last_name,
  manager_id
)
VALUES
  (1, 'Windy', 'Hays', NULL),
  (2, 'Ava', 'Christensen', 1),
  (3, 'Hassan', 'Conner', 1),
  (4, 'Anna', 'Reeves', 2),
  (5, 'Sau', 'Norman', 2),
  (6, 'Kelsie', 'Hays', 3),
  (7, 'Tory', 'Goff', 3),
  (8, 'Salley', 'Lester', 3);
```

Значение в столбце **manager_id** отображает менеджера, которому подчиняется сотрудник. Если значение в столбце **manager_id**
принимает null, значит сотрудник никому не подчиняется. Другими словами, данный сотрудник является старшим менеджером.

Чтобы определить кто и кому подчиняется, используйте следующий запрос:

```
SELECT
  e.first_name || ' ' || e.last_name employee,
  m .first_name || ' ' || m .last_name manager
FROM
  employee e
INNER JOIN employee m ON m .employee_id = e.manager_id
ORDER BY
  manager;
```

Данный запрос дважды ссылается на таблицу **employee**, сначала как сотрудник, затем как менеджер. Здесь псевдоним **e**
используется для сотрудника, а **m** для менеджера.

Предикат объединения находит пару “сотрудник/менеджер”, сопоставляя значения в столбцах **employee_id** и **manager_id**.

![](https://i.imgur.com/72Yn7wj.png)

Как вы можете видеть на скриншоте выше, старший менеджер не появился в выводе.

Чтобы включить старшего менеджера в набор результатов, используйте условие **LEFT JOIN** вместо **INNER JOIN**,
как показано в следующем запросе:

```
SELECT
  e.first_name || ' ' || e.last_name employee,
  m .first_name || ' ' || m .last_name manager
FROM
  employee e
LEFT JOIN employee m ON m .employee_id = e.manager_id
ORDER BY
  manager;
```

![](https://i.imgur.com/qS9BPq7.png)

### 2) Пример сравнения строк одной и той же таблицы

Воспользуемся таблицей **film** из [базы данных DVD-проката](https://itdoxy.com/пример-базы-данных-postgresql/):

![](https://i.imgur.com/66TKuVu.png)

Следующий запрос находит все пары фильмов одинаковой длины:

```
SELECT
  f1.title,
  f2.title,
  f1. length
FROM
  film f1
INNER JOIN film f2 ON f1.film_id <> f2.film_id
AND f1. length = f2. length;
```

![](https://i.imgur.com/kYFltlB.png)

Данный запрос объединил таблицу **film** с самой собой. Предикат объединения соответствует различным фильмам, указанным
в **film_id**, которые имеют одинаковую длину.

Итак, мы научились сравнивать строки в одной и той же таблице, используя запрос **Self-Join**, а в следующий раз мы поговорим
о [полном внешнем объединении](https://itdoxy.com/полное-внешнее-объединение-в-postgresql/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
