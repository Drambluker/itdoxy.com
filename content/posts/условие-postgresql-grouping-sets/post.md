---
title: "Условие PostgreSQL GROUPING SETS"
slug: "условие-postgresql-grouping-sets"
date: 2019-08-09T00:00:00+03:00
categories: ["Базы данных", "PostgreSQL"]
tags: ["Курс молодого бойца PostgreSQL"]
draft: false
---

![](/posts/условие-postgresql-grouping-sets/SQL7.1.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили
об [операторе PostgreSQL **EXCEPT**](https://itdoxy.com/оператор-postgresql-except/). Теперь же мы углубимся в изучение
условия **GROUPING SETS**, которое используется для генерации результирующего набора, эквивалентного тому, который
генерирует **UNION ALL** из нескольких условий **GROUP BY**.

## Настройка таблицы

Прежде чем мы начнём, для наглядности создадим таблицу **sales**.

```
CREATE TABLE sales (
    brand VARCHAR NOT NULL,
    segment VARCHAR NOT NULL,
    quantity INT NOT NULL,
    PRIMARY KEY (brand, segment)
);

INSERT INTO sales (brand, segment, quantity)
VALUES
    ('ABC', 'Premium', 100),
    ('ABC', 'Basic', 200),
    ('XYZ', 'Premium', 100),
    ('XYZ', 'Basic', 300);
```

![](https://i.imgur.com/epicOWh.png)

В таблице **sales** хранится количество продуктов, проданных по брендам или сегментам.

## Введение в условие PostgreSQL GROUPING SETS

Групповой набор — это набор столбцов, по которым осуществляется группировка. Как правило, один агрегатный запрос
определяет один групповой набор.

Например, следующий запрос определяет групповой набор бренда и сегмента. Возвращает количество продуктов проданных
по бренду и сегменту.

```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand,
    segment;
```

![](https://i.imgur.com/7fZAeTc.png)

Следующий запрос определяет количество продуктов, проданных по брендам. Он определяет групповой набор бренда:

![](https://i.imgur.com/9cLhBA4.png)

Следующий запрос определяет количество продуктов, проданных по сегментам. Он определяет групповой набор сегмента:

```
SELECT
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    segment;
```

![](https://i.imgur.com/lkgW8hc.png)

Следующий запрос определяет количество продуктов, проданных для всех брендов и сегментов. Он определяет пустой групповой
набор:

```
SELECT
    SUM (quantity)
FROM
    sales;
```

![](https://i.imgur.com/uDMwtGT.png)

Предположим, что вместо четырех наборов результатов вы хотели видеть унифицированный набор результатов с общими данными
для всех групповых наборов. Чтобы объединить все запросы выше — воспользуйтесь оператором [**UNION ALL**](https://itdoxy.com/оператор-postgresql-union/):

Поскольку оператор **UNION ALL** требует, чтобы все наборы результатов имели одинаковое количество столбцов с совместимыми
типами данных, вам необходимо настроить запросы, добавив **NULL** в список выбора каждого из них, как показано ниже:

```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand,
    segment

UNION ALL

SELECT
    brand,
    NULL,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand

UNION ALL

SELECT
    NULL,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    segment

UNION ALL

SELECT
    NULL,
    NULL,
    SUM (quantity)
FROM
    sales;
```

![](https://i.imgur.com/EqPV41Q.png)

Данный запрос сгенерировал один общий набор результатов для всех групповых наборов.

Несмотря на то, что код работает так, как вы ожидали, у него есть две основные проблемы. Первая — это очень длинный код.
Вторая же проблема связана с производительностью, так как PostgreSQL должен сканировать таблицу продаж отдельно для каждого запроса.

Чтобы сделать код более эффективным, PostgreSQL предоставляет условие **GROUPING SETS**, которое является подразделом
условия **GROUP BY**.

Итак, **GROUPING SETS** позволяет определить несколько групповых наборов в одном запросе. Синтаксис вызова выглядит
следующим образом:

```
SELECT
    c1,
    c2,
    aggregate_function(c3)
FROM
    table_name
GROUP BY
    GROUPING SETS (
        (c1, c2),
        (c1),
        (c2),
        ()
);
```

Здесь у нас есть четыре групповых набора (c1,c2), (c1), (c2), and ().

Чтобы применить этот запрос к примеру выше — используйте **GROUPING SETS** вместо оператора **UNION ALL**, как показано
ниже:

```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    GROUPING SETS (
        (brand, segment),
        (brand),
        (segment),
        ()
    );
```

![](https://i.imgur.com/knjtQIe.png)

## Функции GROUPING

Функция **GROUPING** принимает имя столбца и возвращает **0** бит, если столбец является членом текущего группового набора,
и **1** в противном случае. Обратите внимание на следующий пример:

```
SELECT
    GROUPING(brand) grouping_brand,
    GROUPING(segment) grouping_segement,
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    GROUPING SETS (
        (brand, segment),
        (brand),
        (segment),
        ()
    )
ORDER BY
    brand,
    segment;
```

![](https://i.imgur.com/QZjJwAT.png)

На этом данный урок подходит к концу, а мы переходим к изучению [PostgreSQL **CUBE**](https://itdoxy.com/подкласс-postgresql-cube/).

Источник: [PostgreSQL Tutorial from Scratch](https://itdoxy.com/оператор-postgresql-union/)
