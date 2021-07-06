---
title: "Оператор PostgreSQL BETWEEN"
slug: "оператор-postgresql-between"
date: 2019-07-16T00:00:00+03:00
categories: ["Базы данных", "PostgreSQL"]
tags: ["Курс молодого бойца PostgreSQL"]
draft: false
---

![](/posts/оператор-postgresql-between/SQL3.5.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили об
[операторе PostgreSQL **IN**](https://itdoxy.com/оператор-postgresql-in/). Теперь же мы углубимся в изучение оператора
**BETWEEN**, используемого для сопоставления значения с диапазоном значений.

## Введение в оператор PostgreSQL BETWEEN

Оператор **BETWEEN** используется для сопоставления значения с диапазоном значений. Рассмотрим синтаксис вызова
оператора **BETWEEN**:

```
value BETWEEN low AND high;
```

Если значение больше или равно значения **low** и меньше или равно значения **high** — выражение вернёт _true_,
в противном случае, — _false_.

Вы можете переписать оператор **BETWEEN** используя операторы больше или равно (>=) и меньше или равно (<=),
как показано в запросе ниже:

```
value >= low and value <= high
```

Если вы хотите проверить вышло ли значение за допустимый диапазон, используйте оператор **NOT** вместе с оператором
**BETWEEN** следующим образом:

```
value NOT BETWEEN low AND high;
```

Следующее выражение аналогично выражению, использующему операторы **NOT** и **BETWEEN**:

```
value < low OR value > high
```

Зачастую оператор **BETWEEN** используется в условии **WHERE** операторов [**SELECT**](https://itdoxy.com/оператор-postgresql-select/),
**INSERT**, **UPDATE** или **DELETE**.

## Примеры использования оператора BETWEEN

В качестве примера возьмём таблицу **payment** из [примера базы данных](https://itdoxy.com/пример-базы-данных-postgresql/).

![](https://i.imgur.com/BvrHMiI.png)

Следующий запрос выбирает платеж, сумма которого составляет от 8 до 9 (USD):

```
SELECT
    customer_id,
    payment_id,
    amount
FROM
    payment
WHERE
    amount BETWEEN 8
AND 9;
```

![](https://i.imgur.com/HQeFiXN.png)

Чтобы получить платежи, сумма которых не находится в диапазоне от 8 до 9, используйте следующий запрос:

```
SELECT
    customer_id,
    payment_id,
    amount
FROM
    payment
WHERE
    amount NOT BETWEEN 8
AND 9;
```

![](https://i.imgur.com/C3wGpCO.png)

Если вы хотите сравнить значение с диапазоном дат, вы должны использовать литерал даты в формате ISO 8601, т.е ГГГГ-ММ-ДД.
Например, чтобы получить платёж, дата платежа которого находится в диапазоне от 2007-02-07 до 2007-02-15, используйте следующий запрос:

```
SELECT
    customer_id,
    payment_id,
    amount,
    payment_date
FROM
    payment
WHERE
    payment_date BETWEEN '2007-02-07'
AND '2007-02-15';
```

![](https://i.imgur.com/i9Kns8k.png)

Итак, мы научились использовать оператор PostgreSQL **BETWEEN** для выбора значения в диапазоне значений. В следующий раз
мы расскажем вам об [операторе PostgreSQL **LIKE**](https://itdoxy.com/оператор-postgresql-like/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
