---
title: "Настройка ASP.Net Docker"
slug: "настройка-asp-net-docker"
date: 2019-08-27T01:00:00+03:00
categories: ["Docker"]
tags: ["Курс молодого бойца Docker"]
draft: false
---

![](/posts/настройка-asp-net-docker/docker25.jpg)

ASP.Net — это стандартная среда веб-разработки, предоставляемая Microsoft для разработки серверных приложений. Поскольку
ASP.Net — это популярная среда веб-разработки, Docker также обеспечил поддержку ASP.Net.

Мы рассмотрим различные шаги для настройки и запуска контейнера Docker для ASP.Net.

## Предисловие

Для запуска ASP.Net необходимо выполнить следующие шаги.

**Шаг 1.** Поскольку ASP.Net может выполняться только в системах Windows, сначала необходимо убедиться, что у вас
установлена Windows 10 или Windows Server 2016.

**Шаг 2.** Затем убедитесь, что в вашей системе Windows установлены Hyper-V и Containers. Чтобы их установить, вы можете
включить или отключить функции Windows. Убедитесь в том, что опции Hyper-V и Containers отмечены, после чего нажмите
кнопку OK.

![](https://i.imgur.com/DUtdyZC.jpg)

Система может потребовать перезагрузку после данной операции.

**Шаг 3.** Следующим шагом вам необходимо использовать следующую команду **powershell**, чтобы установить версию Docker
**1.13.0c4**. Следующая команда загрузит её и сохранит во временном расположении.

```
Invoke-WebRequest "https://test.docker.com/builds/Windows/x86_64/docker-1.13.0-
    rc4.zip" -OutFile "$env:TEMP\docker-1.13.0-rc4.zip" –UseBasicParsing
```

![](https://i.imgur.com/AbPOJOB.jpg)

**Шаг 4.** Затем необходимо извлечь содержимое архива с помощью следующей команды **powershell**.

```
Expand-Archive -Path "$env:TEMP\docker-1.13.0-rc4.zip" -DestinationPath $env:ProgramFiles
```

![](https://i.imgur.com/htEdYsW.jpg)

**Шаг 5.** После чего вам необходимо добавить **Dockerfile** в переменную окружения с помощью следующей команды **powershell**.

```
$env:path += ";$env:ProgramFiles\Docker"
```

**Шаг 6.** Далее, зарегистрируйте демон-сервис Docker при помощи следующей команды **powershell**.

```
dockerd --register-service
```

**Шаг 7.** Наконец, вы можете запустить **docker daemon** при помощи следующей команды:

```
Start-Service Docker
```

Воспользуйтесь командой **docker version** в **powershell**, чтобы убедиться в том, что **docker daemon** работает.

![](https://i.imgur.com/irLVPWb.jpg)

## Установка контейнера ASP.Net

**Шаг 1.** Первым делом извлеките образ из Docker Hub. Когда вы войдёте в Docker Hub — воспользуйтесь поиском, чтобы найти
образ **Microsoft/aspnet**, как показано ниже. Просто вбейте **asp** в строке поиска и нажмите на ссылку **Microsoft/aspnet**,
которая будет выведена в результатах поиска.

![](https://i.imgur.com/dRvBxmy.jpg)

**Шаг 2.** Вы увидите команду Docker **pull** для ASP.Net в деталях хранилища Docker Hub.

![](https://i.imgur.com/zaewChN.jpg)

**Шаг 3.** Перейдите к хосту Docker и запустите команду Docker **pull** для образа **microsoft/aspnet**. Обратите внимание,
что образ довольно большой, около 4.2 Гб.

![](https://i.imgur.com/Gq4v0l6.jpg)

**Шаг 4.** Теперь перейдите [по следующему адресу](https://github.com/Microsoft/aspnet-docker) и загрузите весь Git репозиторий.

**Шаг 5.** Создайте папку под названием **App** на диске **C**. После чего скопируйте содержимое папки **4.6.2/sample**
на диск **C**. Перейдите в **Dockerfile** и введите следующую команду:

```
docker build –t aspnet-site-new –build-arg site_root=/
```

Детальнее рассмотрим некоторые части запроса:

- Он создаёт новый образ под названием **aspnet-site-new** из **Dockerfile**.
- Корневой путь установлен к папке **localpath**.

![](https://i.imgur.com/cF0pN2k.jpg)

**Шаг 6.** Теперь пришло время запускать контейнер. Сделать это можно при помощи следующей команды:

```
docker run –d –p 8000:80 –name my-running-site-new aspnet-site-new
```

![](https://i.imgur.com/fwoRtgX.jpg)

**Шаг 7.** Теперь у вас будет запущен IIS в контейнере Docker. Чтобы узнать IP-адрес контейнера Docker, воспользуйтесь
командой Docker **inspect**, как показано ниже:

![](https://i.imgur.com/fVcYBaz.jpg)

Источник: [Docker — Setting ASP.Net](https://www.tutorialspoint.com/docker/docker_setting_asp.net.htm)
