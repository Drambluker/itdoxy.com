---
images:
  - /images/uploads/SQL4.7.jpg
title: Естественное объединение в PostgreSQL
slug: естественное-объединение-в-postgresql
date: 2019-07-25T01:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL4.7.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
о [перекрёстном объединении PostgreSQL](https://itdoxy.com/перекрёстное-объединение-в-postgresql/). Теперь же мы углубимся
в изучение естественного объединения, именуемого **NATURAL JOIN**, при помощи которого мы будем запрашивать данные
из двух или более таблиц.

**Естественное объединение** создаёт неявное объединение на основе одинаковых имён столбцов в объединяемых таблицах.
Обратите внимание на синтаксис PostgreSQL **NATURAL JOIN**:

```
SELECT *
FROM T1
NATURAL [INNER, LEFT, RIGHT] JOIN T2;
```

Естественное объединение может быть внутренним, левым или правым. Если вы не укажете соединение явно, например,
**INNER JOIN**, **LEFT JOIN**, **RIGHT JOIN**, то PostgreSQL по-умолчанию будет использовать **INNER JOIN**.

При использовании звёздочки (*) в списке выбора, вывод будет содержать следующие столбцы:

- Все общие столбцы в обеих таблицах, с одинаковыми именами
- Каждый столбец в первой и второй таблицах, который не является общим столбцом

## Пример использования PostgreSQL NATURAL JOIN

Чтобы продемонстрировать работу естественного объединения в PostgreSQL, создадим две таблицы: **categories** и **products**
с помощью оператора **CREATE TABLE**:

```
CREATE TABLE categories (
  category_id serial PRIMARY KEY,
  category_name VARCHAR (255) NOT NULL
);

CREATE TABLE products (
  product_id serial PRIMARY KEY,
  product_name VARCHAR (255) NOT NULL,
  category_id INT NOT NULL,
  FOREIGN KEY (category_id) REFERENCES categories (category_id)
);
```

Каждая категория может иметь как множество продуктов, принадлежащих к одной и только одной категории, так и ни одного.
Столбец **category_id** в таблице **products** — это внешний ключ, который ссылается на первичный ключ таблицы этой категории.
К тому же, **category_id** — это общий столбец, который мы будем использовать для естественного объединения.

Воспользуемся операторами **INSERT**, чтобы вставить некоторые примеры данных в таблицы **categories** и **products**:

```
INSERT INTO categories (category_name)
VALUES
  ('Smart Phone'),
  ('Laptop'),
  ('Tablet');

INSERT INTO products (product_name, category_id)
VALUES
  ('iPhone', 1),
  ('Samsung Galaxy', 1),
  ('HP Elite', 2),
  ('Lenovo Thinkpad', 2),
  ('iPad', 3),
  ('Kindle Fire', 3);
```

Следующий запрос использует условие **NATURAL JOIN** для объединения таблицы **products** с таблицей **categories**:

```
SELECT
*
FROM
products
NATURAL JOIN categories;
```

```
category_id | product_id |  product_name   | category_name
------------+------------+-----------------+---------------
          1 |          1 | iPhone          | Smart Phone
          1 |          2 | Samsung Galaxy  | Smart Phone
          2 |          3 | HP Elite        | Laptop
          2 |          4 | Lenovo Thinkpad | Laptop
          3 |          5 | iPad            | Tablet
          3 |          6 | Kindle Fire     | Tablet
(6 rows)
```

Запрос выше эквивалентен следующему запросу, использующему условие **INNER JOIN**:

```
SELECT
  *
FROM
  products
INNER JOIN categories USING (category_id);
```

Удобство **NATURAL JOIN** заключается в том, что он не требует указывать условие объединения, поскольку он использует
неявное условие объединения на основе общего столбца.

Тем не менее следует избегать использования **NATURAL JOIN**, когда это возможно, потому что иногда это может привести
к неожиданному результату.

Предположим, что у нас есть таблицы **city** и **country**. Обе таблицы имеют одинаковый столбец **country_id**, поэтому
для объединения этих таблицы мы можем воспользоваться **NATURAL JOIN** следующим образом:

```
SELECT
  *
FROM
  city
NATURAL JOIN country;
```

```
country_id | last_update | city_id | city | country
-----------+-------------+---------+------+---------
(0 rows)
```

Запрос вернёт пустой набор результатов.

Причиной тому является то, что обе таблицы также имеют общий столбец **last_update**, который не может быть использован
для объединения. Однако условие **NATURAL JOIN** используется в столбце **last_update**.

На этом мы заканчиваем главу, посвящённую объединениям, и переходим к [группировке данных](https://itdoxy.com/условие-postgresql-group-by/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
