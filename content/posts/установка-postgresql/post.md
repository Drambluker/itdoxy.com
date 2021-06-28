---
title: "Установка PostgreSQL"
slug: "установка-postgresql"
date: 2019-07-10T00:00:00+03:00
categories: ["Базы данных", "PostgreSQL"]
tags: ["Курс молодого бойца PostgreSQL"]
draft: false
---

![](/posts/что-такое-postgresql/11.jpg)

Данная статья относится к циклу статей, посвященных PostgreSQL. В предыдущей статье мы узнали о [PostgreSQL](https://itdoxy.com/что-такое-postgresql/)
и функциях, которые выделяют PostgreSQL среди других систем управления базами данных. Теперь мы покажем вам, как установить
PostgreSQL для изучения и практики в вашей локальной системе.

PostgreSQL был разработан для UNIX-подобных платформ, однако он также был разработан переносным. Это означает, что
PostgreSQL может работать и на других платформах, таких как Mac OS X, Solaris и Windows.

Начиная с версии 8.0, PostgreSQL предлагает установщик для операционных систем Windows, который делает процесс установки
простым и быстрым. В целях разработки мы установим PostgreSQL версии 11.3 на Windows 10.

Необходимо пройти три этапа для того, чтобы установить PostgreSQL:

1. Загрузить установщик PostgreSQL для Windows
2. Установить PostgreSQL
3. Убедиться в правильной установке

## Загрузка установщика PostgreSQL для Windows

В первую очередь, вам нужно перейти на страницу загрузки [PostgreSQL на EnterpriseDB](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads).

Затем, нажать на ссылку для скачивания, как показано ниже:

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Download-PostgreSQL.png)

Загрузка установщика займет всего несколько минут.

## Установка PostgreSQL

Шаг 1. Дважды щелкните по исполняемому файлу, появится мастер установки, где вы сможете выбрать различные параметры,
которые вы хотели бы видеть в PostgreSQL.

Шаг 2. Нажмите кнопку **Next**

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-1.png)

Шаг 3. Укажите папку для установки, выберите свою собственную или оставьте папку по умолчанию, предложенную установщиком
PostgreSQL, и нажмите кнопку **Next**.

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-2.png)

Шаг 4. Выберите компоненты для установки и нажмите кнопку **Next**

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-3.png)

Шаг 5. Выберите каталог базы данных для хранения информации. Оставьте его по умолчанию или выберите свой и нажмите **Next**.

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-4.png)

Шаг 6. Введите пароль для суперпользователя базы данных (postgres)

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-5.png)

Шаг 7. Введите порт для PostgreSQL. Убедитесь, что никакие другие приложения не используют этот порт. Оставьте его
по умолчанию, если вы не уверены.

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-6.png)

Шаг 8. Выберите язык по умолчанию, используемый базой данных, и нажмите **Next**.

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-7.png)

Шаг 9. PostgreSQL готов к установке. Нажмите кнопку **Next**, чтобы начать установку.

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-8.png)

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-9.png)

Установка может занять несколько минут.

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-10.png)

Шаг 10. Нажмите кнопку **Finish**, чтобы завершить установку PostgreSQL.

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-12-Windows-Step-11.png)

## Проверка установки

Есть несколько способов проверки установки. Вы можете попытаться подключиться к серверу базы данных PostgreSQL из любого
клиентского приложения, например: psql и phAdmin.

Быстрее всего проверить установку можно через программу psql.

Сначала нажмите на значок psql, чтобы запустить его. Откроется командная строка окна psql.

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-psql.png)

Затем, введите всю необходимую информацию, такую как сервер, база данных, порт, имя пользователя и пароль. Чтобы принять
значение по умолчанию, нажмите **Enter**. Обратите внимание, что вы должны предоставить пароль, который вы ввели
при установке PostgreSQL.

Затем, введите команду **_SELECT version();_** вы увидите следующий результат:

![](https://www.postgresqltutorial.com/wp-content/uploads/2020/07/Install-PostgreSQL-psql-verification.png)

Поздравляем! Вы успешно установили сервер базы данных PostgreSQL в своей локальной системе. В следующей статье мы изучим
[различные способы подключения к серверу баз данных PostgreSQL](https://itdoxy.com/подключение-к-серверу-базы-данных-postgresql/).

Источник: [PostgreSQL Tutorial from Scratch](http://www.postgresqltutorial.com/)
