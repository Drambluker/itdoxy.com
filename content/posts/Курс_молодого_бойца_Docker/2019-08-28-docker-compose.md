---
images:
  - /images/uploads/docker28.jpg
title: Docker Compose
slug: docker-compose
date: 2019-08-28T00:00:00+03:00
categories:
  - Docker
tags:
  - Курс молодого бойца Docker
draft: false
---

![](/images/uploads/docker28.jpg)

**Docker Compose** предназначен для запуска нескольких контейнеров как одной службы. Например, предположим, что у вас
есть приложение, которое требует NGINX и MySQL, вы можете создать один файл, который будет запускать оба контейнера
как службу без необходимости запускать каждый из них по отдельности.

В данной главе мы узнаем, как начать работу с Docker Compose. Затем мы рассмотрим, как можно получить простой сервис
с MySQL и NGINX и запустить его с помощью Docker Compose.

## Установка Docker Compose

Для запуска Docker Compose вам необходимо выполнить следующие шаги.

**Шаг 1.** Загрузите необходимые файлы из **github** с помощью следующей команды:

```
curl -L "https://github.com/docker/compose/releases/download/1.10.0-rc2/dockercompose
    -$(uname -s) -$(uname -m)" -o /home/demo/docker-compose
```

Команда выше загрузит последнюю версию Docker Compose, которая на момент написания (перевода 😎) этой статьи была **1.10.0-rc2**.
После чего он \[Docker Compose] сохранится по пути **/home/demo/**.

![](https://i.imgur.com/Kgdwm2J.jpg)

**Шаг 2.** Затем нам необходимо предоставить **расширенные полномочия** для загруженного файла Docker Compose с помощью
следующей команды:

```
chmod +x /home/demo/docker-compose
```

![](https://i.imgur.com/gAP6ssr.jpg)

После чего мы можем воспользоваться следующей команды, чтобы увидеть версию **compose**:

```
docker-compose version
```

- **version** — выводит информацию о версии Docker Compose.

Вывод вернёт текущую версию Docker Compose.

### Пример

В следующем примере показано, как получить версию **docker-compose**.

```
sudo ./docker-compose -version
```

Пример вывода:

![](https://i.imgur.com/EPvOqq8.jpg)

## Создание собственного файла Docker-Compose

Теперь создадим наш первый файл Docker Compose. Все файлы Docker Compose являются файлами YAML. Вы можете создать его
с помощью текстового редактора. Итак, выполните следующую команду, чтобы создать файл **compose**:

```
sudo vim docker-compose.yml
```

![](https://i.imgur.com/oU2lfdM.jpg)

Детальнее рассмотрим содержимое данного файла:

- Ключевые слова **database** и **web** используются для определения двух отдельных служб. Первое будет работать с нашей
базой данных **mysql**, а второе будет нашим веб-сервером **nginx**.
- Ключевое слово **image** используется для указания образа из Docker Hub для наших контейнеров **mysql** и **nginx**.
- Для базы данных мы используем ключевое слово **ports**, чтобы указать порты, которые должны быть выставлены для **mysql**.
- Наконец, мы также указываем переменные окружения для **mysql**, которые необходимы для запуска **mysql**.

Теперь давайте запустим наш файл Docker Compose используя следующую команду:

```
sudo ./docker-compose up
```

Эта команда возьмет файл **docker-compose.yml** в вашем локальном каталоге и начнет сборку контейнеров.

![](https://i.imgur.com/57LwC6B.jpg)

После запуска образы начнут автоматически загружаться, а контейнеры запускаться.

![](https://i.imgur.com/FhkLo0H.jpg)

И когда вы выполните **docker ps**, сможете увидеть, что контейнеры действительно работают.

![](https://i.imgur.com/6vEFL9P.jpg)

Источник: [Docker — Compose](https://www.tutorialspoint.com/docker/docker_compose.htm)
