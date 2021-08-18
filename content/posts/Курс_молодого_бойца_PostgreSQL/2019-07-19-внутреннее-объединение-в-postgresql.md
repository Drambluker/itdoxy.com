---
images:
  - /images/uploads/SQL4.2.jpg
title: Внутреннее объединение в PostgreSQL
slug: внутреннее-объединение-в-postgresql
date: 2019-07-19T00:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL4.2.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
об [объединениях в PostgreSQL](https://itdoxy.com/объединения-в-postgresql/). Теперь же мы углубимся в изучение каждого
объединения PostgreSQL по отдельности. Начнём со внутреннего объединения, именуемого INNER JOIN, при помощи которого
мы будем выбирать данные из нескольких таблиц.

## Введение в условие PostgreSQL INNER JOIN

В предыдущих уроков мы научились [выбирать данные из таблицы](https://itdoxy.com/оператор-postgresql-select/), необходимые
нам столбцы и строки, а также [сортировать результирующий набор](https://itdoxy.com/условие-postgresql-order-by/) в определённом порядке.

Настало время перейти к важнейшей концепции баз данных, называемой объединением, которая позволяет связывать данные одной
таблицы с данными других таблиц. Существует несколько видов объединений: **INNER JOIN**, **OUTER JOIN** и **self-join**.
В данном руководстве мы сфокусируемся на **INNER JOIN**.

Предположим вам необходимо получить данные из таблиц **A** и **B**. Таблица **B** содержит поле **fka**, которое относится
к первичному ключу таблицы **A**.

![](https://i.imgur.com/dYAoNVZ.png)

Чтобы получить данные из обеих таблиц, используйте условие **INNER JOIN** в вызове оператора
[SELECT](https://itdoxy.com/оператор-postgresql-select/) следующим образом:

```
SELECT
    A.pka,
    A.c1,
    B.pkb,
    B.c2
FROM
    A
INNER JOIN B ON A .pka = B.fka;
```

Чтобы объединить таблицу **A** с таблицей **B**, вам необходимо:

- Во-первых, указать столбцы обеих таблиц, из которых вы хотите выбрать данные в условии **SELECT**.
- Во-вторых, указать главную таблицу, то есть **A**, в условии **FROM**.
- В-третьих, указать таблицу, с которой вы объединяете главную таблицу, то есть **B**, в условии **INNER JOIN**. Кроме
  того, вы устанавливаете условие объединения после ключевого слова **ON**, то есть **A.pka = B.fka**.

Для каждой строки таблицы **A**, PostgreSQL анализирует таблицу **B**, чтобы проверить, существует ли какая-либо строка,
удовлетворяющая условию, то есть **A.pka = B.fka**. Если такая строка найдена, то PostgreSQL объединяет столбцы обеих
строк в одну и добавляет объединенную строку в возвращаемый набор результатов.

Первичный ключ столбца (**pka**) и внешний ключ столбца (**fka**) обычно индексируются, следовательно, PostgreSQL должен
проверять только соответствие в индексах, что делает его невероятно быстрым.

Порой, таблицы **A** и **B** могут иметь столбцы с одинаковыми именами, поэтому мы должны ссылаться на столбец
как **table_name.column_name** (имя_таблицы.имя_столбца), чтобы избежать неоднозначности. Если имя таблицы достаточно
длинное, вы можете использовать [псевдоним таблицы](https://itdoxy.com/псевдонимы-в-postgresql/), например, **tbl**,
и ссылаться на столбец как **tbl.column_name**.

Следующая диаграмма Венна иллюстрирует работу условия PostgreSQL **INNER JOIN**:

![](https://i.imgur.com/48l42Pe.jpg)

Условие **INNER JOIN** вернёт строки таблицы **A**, имеющие соответствующие строки в таблице **B**.

## Примеры использования условия PostgreSQL INNER JOIN

### Пример использования условия PostgreSQL INNER JOIN для объединения двух таблиц

В качестве примера возьмём таблицы **customer** и **payment** из [примера базы данных](https://itdoxy.com/пример-базы-данных-postgresql/).

![](https://i.imgur.com/xI2ym1W.png)

Каждый клиент может иметь как множество платежей, так и ни одного. Каждый платёж относится к одному и только одному клиенту.
Поле **customer_id** устанавливает связь между двумя таблицами.

Используйте условие **INNER JOIN** для объединения таблицы клиентов с таблицей платежей следующим образом:

```
SELECT
    customer.customer_id,
    first_name,
    last_name,
    email,
    amount,
    payment_date
FROM
    customer
INNER JOIN payment ON payment.customer_id = customer.customer_id;
```

![](https://i.imgur.com/b5Ofyis.png)

Вы можете добавить условие **ORDER BY** для сортировки набора результатов по **customer_id**, как показано ниже:

```
SELECT
    customer.customer_id,
    first_name,
    last_name,
    email,
    amount,
    payment_date
FROM
    customer
INNER JOIN payment ON payment.customer_id = customer.customer_id
ORDER BY
    customer.customer_id;
```

![](https://i.imgur.com/hfkWQdZ.png)

Для фильтрации клиентов, используйте условие **WHERE**. Следующий запрос возвращает данные об аренде для клиента
с идентификатором 2:

```
SELECT
    customer.customer_id,
    first_name,
    last_name,
    email,
    amount,
    payment_date
FROM
    customer
INNER JOIN payment ON payment.customer_id = customer.customer_id
WHERE
    customer.customer_id = 2;
```

![](https://i.imgur.com/5sGRpav.png)

### Пример использования условия PostgreSQL INNER JOIN для объединения трёх таблиц

Следующая диаграмма отображает отношения между тремя таблицами: **staff** (персонал), **payment** (платёж),
**customer** (клиент).

- Каждый сотрудник может быть связан как с множеством платежей, так и не связан вообще ни с каким.
- Каждый платёж обрабатывается одним и только одним сотрудником.

![](https://i.imgur.com/TeSW7Ww.png)

Для их объединения нам необходимо добавить второе условие **INNER JOIN** после первого **INNER JOIN** следующим образом:

```
SELECT
    customer.customer_id,
    customer.first_name customer_first_name,
    customer.last_name customer_last_name,
    customer.email,
    staff.first_name staff_first_name,
    staff.last_name staff_last_name,
    amount,
    payment_date
FROM
    customer
INNER JOIN payment ON payment.customer_id = customer.customer_id
INNER JOIN staff ON payment.staff_id = staff.staff_id;
```

![](https://i.imgur.com/1n2Q8ti.png)

Чтобы объединить более трёх таблиц, воспользуйтесь той же самой техникой.

Итак, мы рассмотрели внутреннее объединение и готовы двигаться дальше, к [левому объединению](https://itdoxy.com/левое-объединение-в-postgresql/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
