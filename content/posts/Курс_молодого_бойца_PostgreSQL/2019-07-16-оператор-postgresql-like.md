---
images:
  - /images/uploads/SQL3.6.jpg
title: Оператор PostgreSQL LIKE
slug: оператор-postgresql-like
date: 2019-07-16T01:00:00+03:00
categories:
  - Базы данных
  - PostgreSQL
tags:
  - Курс молодого бойца PostgreSQL
draft: false
---

![](/images/uploads/SQL3.6.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы говорили об
[операторе PostgreSQL **BETWEEN**](https://itdoxy.com/оператор-postgresql-between/). Теперь же мы углубимся в изучение
операторов PostgreSQL **LIKE** и **ILIKE**, необходимых для запроса данных с использованием метода сопоставления с образцом.

## Введение в оператор PostgreSQL LIKE

Предположим, что менеджер магазина просит вас найти клиента, имя которого точно не помнит. Единственное в чём он уверен —
имя начинается с **Jen**. Как вы найдёте нужного клиента? Вы можете прибегнуть к таблице **customer** и проверить
по столбцу имён, есть ли в нём кто-нибудь чьё имя начинается с **Jen**. Однако, это немного утомительно, потому что
в таблице **customer** много строк.

К счастью, вы можете использовать оператор PostgreSQL **LIKE**, как показано в следующем запросе:

```
SELECT
    first_name,
    last_name
FROM
    customer
WHERE
    first_name LIKE 'Jen%';
```

![](https://i.imgur.com/JBkvFtE.png)

Обратите внимание, что условие **WHERE** содержит специальное выражение: **first_name**, оператор **LIKE** и строку,
содержащую знак процента (%), называемое _паттерном_.

Запрос возвращает строки, значения которых в первом столбце имён начинаются с **Jen** и могут сопровождаться любой
последовательностью символов. Этот метод называется методом сопоставления с образцом.

Вы создаёте паттерн объединяя строку с подстановочными знаками и используя операторы **LIKE** и **NOT LIKE**, чтобы найти
совпадения. PostgreSQL предоставляет два подстановочных знака:

- Процент (%) для сопоставления любого количества символов.
- Нижнее подчёркивание (_) для сопоставления любого единичного символа.

Синтаксис вызова оператора PostgreSQL **LIKE** выглядит следующим образом:

```
string LIKE pattern
```

Выражение возвращает **true** если строка совпадает с паттерном, в противном случае — **false**.

Вы можете объединить оператор **LIKE** с оператором **NOT**, как показано ниже:

```
string NOT LIKE pattern
```

Выражение возвращает **true**, если оператор **LIKE** возвращает **false** и наоборот.

Если паттерн не содержит подстановочных знаков, оператор **LIKE** выступает в роли оператора равенства (=).

## Примеры использования метода сопоставления с образцом в PostgreSQL

### Примеры использования оператора PostgreSQL LIKE

Обратите внимание на следующий пример:

```
SELECT
    'foo' LIKE 'foo', -- true
    'foo' LIKE 'f%', -- true
    'foo' LIKE '_o_', -- true
    'bar' LIKE 'b_'; -- false
```

Как это работает.

- Первое выражение возвращает **true**, так как паттерн **foo** не содержит подстановочных знаков, из-за чего оператор
  **LIKE** выступает в роли оператора равенства (=).
- Второе выражение возвращает **true**, так как оно соответствует любой строке, начинающейся с буквы **f**,
  сопровождающейся любым количеством символов.
- Третье выражение возвращает **true**, так как паттерн (**\_o\_**) соответствует любой строке, начинающейся с любого
  единичного символа, сопровождающейся буквой **o** и оканчивающейся любым единичным символом.
- Четвёртое выражение возвращает **false**, так как паттерн **b_** начинается с буквы **b** и оканчивается единичным символом.

Вы можете использовать подстановочный знак в начале и/или в конце паттерна. Например, следующий запрос возвращает клиентов,
имена которых содержат строку **er**, т.е **Jenifer**, **Kimberly** и так далее.

```
SELECT
    first_name,
    last_name
FROM
    customer
WHERE
    first_name LIKE '%er%'
```

![](https://i.imgur.com/NNg8b46.png)

Вы можете объединить знак процента (%) со знаком подчёркивания (_), чтобы получился следующий паттерн:

```
SELECT
    first_name,
    last_name
FROM
    customer
WHERE
    first_name LIKE '_her%';
```

![](https://i.imgur.com/yX9eCGs.png)

Выражение соответствует клиенту, чьё имя начинается с любого единичного символа, сопровождается литеральной строкой **her**
и оканчивается любым количеством символов.

### Примеры использования оператора PostgreSQL NOT LIKE

Следующий запрос возвращает клиента, чьё имя не начинается с **Jen**:

```
SELECT
    first_name,
    last_name
FROM
    customer
WHERE
    first_name NOT LIKE 'Jen%';
```

![](https://i.imgur.com/mvzC1vl.png)

Обратите внимание, что мы использовали оператор **NOT LIKE** в условии **WHERE**.

## Разновидности оператора PostgreSQL LIKE

PostgreSQL предоставляет оператор **ILIKE**, выступающий в роли оператора **LIKE**. К тому же, оператор **ILIKE**
сопоставляет значение без учета регистра. Приведём пример:

```
SELECT
    first_name,
    last_name
FROM
    customer
WHERE
    first_name ILIKE 'BAR%';
```

![](https://i.imgur.com/76dDR63.png)

Паттерн **BAR%** сопоставляет любую строку, которая начинается с **BAR**, **Bar**, **BaR** и так далее. Если же в этом
случае вы будете использовать оператор **LIKE**, запрос не вернёт ни строчки.

PostgreSQL также предоставляет и другие операторы, которые действуют как **LIKE**, **NOT LIKE**, **ILIKE** и **NOT ILIKE**:

- ~~ эквивалентно **LIKE**
- \~~* эквивалентно **ILIKE**
- !~~ эквивалентно **NOT LIKE**
- !~~* эквивалентно **NOT ILIKE**

На этом мы заканчиваем изучение оператора PostgreSQL **LIKE**, позволяющего запросить данные с использованием метода
сопоставления с образцом. Далее речь пойдёт об операторе PostgreSQL [**IS NULL**](https://itdoxy.com/оператор-postgresql-is-null/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
