---
title: "Условие PostgreSQL HAVING"
slug: "условие-postgresql-having"
date: 2019-07-27T00:00:00+03:00
categories: ["Базы данных", "PostgreSQL"]
tags: ["Курс молодого бойца PostgreSQL"]
draft: false
---

![](/posts/условие-postgresql-having/SQL5.2.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
об [условии PostgreSQL **GROUP BY**](https://itdoxy.com/условие-postgresql-group-by/). Теперь же мы углубимся в изучение
условия **HAVING**, при помощи которого мы будем исключать группы строк, которые не удовлетворяют указанному условию.

## Введение в условие PostgreSQL HAVING

Зачастую мы используем условие **HAVING** совместно с условием **GROUP BY**, чтобы отфильтровать строки, которые
не удовлетворяют указанному условию.

Следующий запрос отображает синтаксис вызова условия **HAVING**:

```
SELECT
    column_1,
    aggregate_function (column_2)
FROM
    tbl_name
GROUP BY
    column_1
HAVING
    condition;
```

Предложение **HAVING** устанавливает условие для групп, состоящих из строк, созданных предложением **GROUP BY** после
его \[предложения] применения, тогда как предложение **WHERE** устанавливает условие для отдельных строк перед применением
условия **GROUP BY**. В этом и состоит главное отличие условия **HAVING** от условия **WHERE**.

В PostgreSQL вы можете использовать условие **HAVING** без условия **GROUP BY**. В таком случае, условие **HAVING** обратит
запрос в одну группу. К тому же, список **SELECT** и условие **HAVING** могут ссылаться только на столбцы из агрегатных
функций. Данный тип запроса возвращает одну строку, если условие в предложении **HAVING** принимает значение true, или
нулевую строку, если оно равно false.

## Примеры использования условия PostgreSQL HAVING

Воспользуемся таблицей payment из [примера базы данных](https://itdoxy.com/пример-базы-данных-postgresql/).

![](https://i.imgur.com/rgmcTTu.png)

## Пример использования условия PostgreSQL HAVING с агрегатной функцией SUM()

Следующий запрос получает общую сумму платежей каждого клиента при помощи условия **GROUP BY**:

```
SELECT
    customer_id,
    SUM (amount)
FROM
    payment
GROUP BY
    customer_id;
```

![](https://i.imgur.com/a37MrvK.png)

Вы можете применить условие **HAVING**, чтобы выбрать только клиентов, потративших сумму превышающую 200, как показано в запросе ниже:

```
SELECT
    customer_id,
    SUM (amount)
FROM
    payment
GROUP BY
    customer_id
HAVING
    SUM (amount) > 200;
```

![](https://i.imgur.com/BHquv1H.png)

## Пример использования условия PostgreSQL HAVING с агрегатной функцией COUNT()

Обратите внимание на таблицу **customer**.

![](https://i.imgur.com/NH3vPq6.png)

Следующий запрос возвращает количество покупателей в каждом магазине:

```
SELECT
    store_id,
    COUNT (customer_id)
FROM
    customer
GROUP BY
    store_id
```

![](https://i.imgur.com/cRXE04p.png)

Вы можете использовать условие **HAVING**, чтобы выбрать магазин с клиентской базой превышающей 300 покупателей:

```
SELECT
    store_id,
    COUNT (customer_id)
FROM
    customer
GROUP BY
    store_id
HAVING
    COUNT (customer_id) > 300;
```

![](https://i.imgur.com/VSoBW7b.png)

На этом мы заканчиваем изучение группировки данных и переходим к выполнению операций над множествами. Изучение этой главы
мы начнём с [оператора PostgreSQL **UNION**](https://itdoxy.com/оператор-postgresql-union/), необходимого для объединения
наборов результатов нескольких запросов в один.

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
