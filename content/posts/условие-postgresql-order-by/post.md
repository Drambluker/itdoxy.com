---
title: "Условие PostgreSQL ORDER BY"
slug: "условие-postgresql-order-by"
date: 2019-07-11T02:00:00+03:00
categories: ["Базы данных", "PostgreSQL"]
tags: ["Курс молодого бойца PostgreSQL"]
draft: false
---

![](/posts/условие-postgresql-order-by/SQL2.2.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы ознакомились
с [оператором PostgreSQL **SELECT**](https://itdoxy.com/оператор-postgresql-select/). Настало время перейти к изучению
условия PostrgeSQL **ORDER BY**, позволяющего отсортировать набор результатов, возвращаемых оператором **SELECT**.

## Введение в условие PostgreSQL ORDER BY

Когда вы запрашиваете данные из таблицы, PostgreSQL возвращает строки в том порядке, в котором они были занесены в таблицу.
Чтобы отсортировать порядок результатов, используйте условие **ORDER BY** в операторе [SELECT](https://itdoxy.com/оператор-postgresql-select/).

Как уже говорилось ранее, условие **ORDER BY** позволяет вам сортировать строки, возвращаемые оператором **SELECT**,
в порядке возрастания или убывания на основе указанных критериев.

Ниже описан синтаксис условия **ORDER BY**:

```
SELECT
  column_1,
  column_2
FROM
  tbl_name
ORDER BY
  column_1 ASC,
  column_2 DESC;
```

Давайте детальнее рассмотрим условие **ORDER BY**:

- Во-первых, укажите столбец, который вы хотите отсортировать в условии **ORDER BY**. Если вы сортируете набор результатов
  по нескольким столбцам, используйте запятую для разделения между двумя столбцами.
- Во-вторых, используйте **ASC** для сортировки набора результатов по-возрастанию и **DESC** по-убыванию. Если вы оставите
  это поле пустым, то условие **ORDER BY** будет использовать **ASC** по-умолчанию.

Теперь рассмотрим несколько примеров использования условия PostgreSQL **ORDER BY**.

## Примеры использования PostgreSQL ORDER BY

Для демонстрации мы возьмём таблицу **customer** из [примера базы данных](https://itdoxy.com/пример-базы-данных-postgresql/).

![](https://www.postgresqltutorial.com/wp-content/uploads/2019/05/customer.png)

Следующий запрос сортирует покупателей по имени в порядке возрастания (в данном случае по алфавиту А-Я):

```
SELECT
  first_name,
  last_name
FROM
  customer
ORDER BY
  first_name ASC;
```

![](https://www.postgresqltutorial.com/wp-content/uploads/2019/12/PostgreSQL-ORDER-BY-one-column-example.png)

Из-за того, что по-умолчанию используется **ASC**, вы можете не упоминать его в вызове оператора.

Если вам необходимо отсортировать покупателей по фамилии в порядке убывания (по алфавиту Я-А), вы можете использовать
ключевое слово **DESC**, как показано в следующем запросе ниже:

```
SELECT
  first_name,
  last_name
FROM
  customer
ORDER BY
  last_name DESC;
```

![](https://www.postgresqltutorial.com/wp-content/uploads/2019/12/PostgreSQL-ORDER-BY-one-column-desc-example.png)

Если вы хотите сначала отсортировать клиентов по имени в порядке возрастания, а затем отсортировать результирующий набор
по фамилии в порядке убывания, используйте следующий запрос:

```
SELECT
  first_name,
  last_name
FROM
  customer
ORDER BY
  first_name ASC,
  last_name DESC;
```

![](https://www.postgresqltutorial.com/wp-content/uploads/2019/12/PostgreSQL-ORDER-BY-multiple-columns.png)

Обратите внимание, что стандарт SQL позволяет сортировать строки только по столбцам, которые появляются в условии **SELECT**.
Однако, PostgreSQL позволяет сортировать строки по столбцам, которые даже не отображаются в списке выбора.

Рекомендуется следовать стандарту SQL, чтобы сделать ваш код гибким к изменениям, которые могут произойти в следующем
обновлении PostgreSQL.

Итак, мы изучили условие **ORDER BY**, позволяющее нам отсортировать набор результатов, возвращаемых оператором **SELECT**,
и можем двигаться к следующему — [**SELECT DISTINCT**](https://itdoxy.com/оператор-postgresql-select-distinct/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
