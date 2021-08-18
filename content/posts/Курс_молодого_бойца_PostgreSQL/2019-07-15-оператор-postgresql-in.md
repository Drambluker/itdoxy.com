---
images:
  - /images/uploads/SQL3.4.jpg
title: Оператор PostgreSQL IN
slug: оператор-postgresql-in
date: 2019-07-15T02:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL3.4.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили об
[условии PostgreSQL **FETCH**](https://itdoxy.com/условие-postgresql-fetch/). Теперь же мы углубимся в изучение оператора
**IN**, используемого в условии [**WHERE**](https://itdoxy.com/условие-postgresql-where/) для проверки списка значений.

## Синтаксис оператора PostgreSQL IN

Как уже было сказано ранее, оператор **IN** используется в условии [**WHERE**](https://itdoxy.com/условие-postgresql-where/),
чтобы проверить, соответствует ли заданное значение какому-либо значению в списке значений.

Синтаксис оператора **IN** выглядит следующим образом:

```
value IN (value1,value2,...)
```

Выражение возвращает _true_, если значение соответствует какому-либо значению в списке, то есть _value1_ и _value2_.
Список значений может быть списком чисел или строк или результирующим набором оператора **SELECT**, как показано в запросе ниже:

```
value IN (SELECT value FROM tbl_name);
```

Оператор внутри скобок называется **подзапросом**, который представляет собой запрос, вложенный в другой запрос.

## Примеры использования оператора PostgreSQL IN

Предположим, что вам необходимо узнать информацию об аренде клиентов под номерами 1 и 2, вы можете использовать оператор
**IN** в условии **WHERE**, как показано ниже:

```
SELECT
  customer_id,
  rental_id,
  return_date
FROM
  rental
WHERE
  customer_id IN (1, 2)
ORDER BY
  return_date DESC;
```

![](https://i.imgur.com/0mraHKA.png)

Используйте знак равенства (=) или оператор **OR**, чтобы переписать запрос, описанный выше, следующим образом:

```
SELECT
  rental_id,
  customer_id,
  return_date
FROM
  rental
WHERE
  customer_id = 1
OR customer_id = 2
ORDER BY
  return_date DESC;
```

Запрос, использующий оператор **IN**, короче и читабельней нежели запрос, использующий знак равенства и оператор **OR**.
К тому же, PostgreSQL выполняет запрос с оператором **IN** намного быстрее, чем тот же запрос, который использует список
операторов **OR**.

## Оператор PostgreSQL NOT IN

Вы можете объединить оператор **IN** с оператором **NOT**, чтобы выбрать строки, значения которых не совпадают со значениями
из списка. Следующий запрос найдёт информацию об аренде клиентов, номера которых не 1 или 2:

```
SELECT
  customer_id,
  rental_id,
  return_date
FROM
  rental
WHERE
  customer_id NOT IN (1, 2);
```

![](https://i.imgur.com/h7mSmNm.png)

Вы также можете изменить написание оператора **NOT IN**, используя знак неравенства (<>) и оператор **AND**,
как показано ниже:

```
SELECT
  customer_id,
  rental_id,
  return_date
FROM
  rental
WHERE
  customer_id <> 1
AND customer_id <> 2;
```

Запрос возвращает тот же вывод, что и при использовании оператора **NOT IN**.

## Оператор PostgreSQL с подзапросом

Следующий запрос возвращает список идентификаторов клиентов, дата возврата арендованного имущества которых
соответствует 2005-05-27:

```
SELECT
  customer_id
FROM
  rental
WHERE
  CAST (return_date AS DATE) = '2005-05-27';
```

![](https://i.imgur.com/7SJu3QD.png)

Вы можете использовать список идентификаторов клиентов в качестве входных данных для оператора **IN**, как показано ниже:

```
SELECT
  first_name,
  last_name
FROM
  customer
WHERE
  customer_id IN (
    SELECT
      customer_id
    FROM
      rental
    WHERE
      CAST (return_date AS DATE) = '2005-05-27'
  );
```

![](https://i.imgur.com/kT65cX1.png)

На этом мы заканчиваем ознакомление с оператором PostgreSQL **IN** и продолжаем двигаться дальше. Далее речь пойдёт об
[операторе **BETWEEN**](https://itdoxy.com/оператор-postgresql-between/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
