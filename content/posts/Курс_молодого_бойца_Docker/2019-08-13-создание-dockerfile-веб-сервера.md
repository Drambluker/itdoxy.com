---
images:
  - /images/uploads/docker016.jpg
title: Создание Dockerfile веб-сервера
slug: создание-dockerfile-веб-сервера
date: 2019-08-13T01:00:00+03:00
categories:
  - Docker
tags:
  - Курс молодого бойца Docker
draft: false
---

![](/images/uploads/docker016.jpg)

Мы уже знаем как использовать Dockerfile для [сборки собственных пользовательских образов](https://itdoxy.com/сборка-файлов-docker/).
Теперь давайте рассмотрим способы создания своего образа веб-сервера, который можно использовать для создания контейнеров.

Для сборки нашего образа мы собираемся использовать веб-сервер Apache на операционной системе Ubuntu.

**Шаг 1.** Первым делом нам необходимо собрать наш Dockerfile. Воспользуемся любым текстовым редактором и создадим
Dockerfile со следующим содержимым:

```
FROM ubuntu
RUN apt-get update
RUN apt-get install –y apache2
RUN apt-get install –y apache2-utils
RUN apt-get clean
EXPOSE 80 CMD [“apache2ctl”, “-D”, “FOREGROUND”]
```

Обратим внимание на следующие моменты:

- Сначала мы создаем наш образ из базового образа Ubuntu.
- Далее мы собираемся использовать команду **RUN** для обновления всех пакетов в системе Ubuntu.
- Далее мы используем команду **RUN** для установки apache2 на наш образ.
- Затем мы используем команду **RUN** для установки необходимых пакетов apache2 для нашего образа.
- После чего мы используем команду **RUN** для очистки любых ненужных файлов из системы.
- Команда **EXPOSE** используется для предоставления порта 80 Apache в контейнере хосту Docker.
- Наконец, команда **CMD** используется для запуска apache2 в фоновом режиме.

![](https://i.imgur.com/Fzfmlpb.jpg)

Теперь, когда данные файла введены, просто сохраните его.

**Шаг 2.** Запустите команду Docker **build** для сборки Dockerfile. Сделать это можно при помощи следующего запроса:

```
sudo docker build –t=”mywebserver” .
```

Мы обозначаем наш образ как **mywebserver**. Когда образ будет собран, вы получите сообщение о том, что файл успешно создан.

![](https://i.imgur.com/6LQP1t4.jpg)

**Шаг 3.** Теперь, когда файл веб-сервера собран, настало время создать контейнер из образа. Сделать мы это можем
при помощи команды Docker **run**:

```
sudo docker run –d –p 80:80 mywebserver
```

![](https://i.imgur.com/H3srzcu.jpg)

Проясним некоторые моменты вызова команды **run**:

- Номер порта, предоставляемый контейнером, — 80. Следовательно, с помощью оператора **–p** мы сопоставляем этот номер
порта с номером порта 80 на нашем локальном хосте.
- Параметр **–d** используется для запуска контейнера в обособленном режиме. За счёт этого контейнер сможет работать
в фоновом режиме.

Если вы перейдете на порт 80 хоста Docker в своем веб-браузере, то увидите, что Apache запущен и работает.

![](https://i.imgur.com/OWkOBvP.jpg)

Источник: [Docker — Building a Web Server Docker File](https://www.tutorialspoint.com/docker/building_web_server_docker_file.htm)
