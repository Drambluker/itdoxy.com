---
title: "Docker Hub"
slug: "docker-hub"
date: 2019-07-29T00:00:00+03:00
categories: ["Docker"]
tags: ["Курс молодого бойца Docker"]
draft: false
---

![](/posts/docker-hub/docker3.jpg)

Docker Hub — это облачная служба реестра, позволяющая загружать образы Docker, созданные другими людьми. Вы также можете загрузить свои собственные образы, созданные в Docker. В этой главе мы научимся использовать образ Jenkins Docker из Docker Hub.

[Официальный сайт Docker](https://www.docker.com/community-edition#/add_ons)

**Шаг 1.** Первым делом — зарегистрируйтесь в Docker Hub.

![](https://i.imgur.com/Y4KI7Dz.jpg)

**Шаг 2.** После чего войдите в свой аккаунт Docker Hub.

![](https://i.imgur.com/GkoMuDW.jpg)

**Шаг 3.** Затем обратимся к поиску и найдём нужный нам образ — Jenkins.

![](https://i.imgur.com/GJ4x3pL.jpg)

**Шаг 4.** Если вы прокрутите на данной странице вниз, то увидите команду Docker **pull**, которая используется для загрузки
образа Jenkins на локальный сервер (Ubuntu).

![](https://i.imgur.com/AZX5FNh.jpg)

**Шаг 5.** Теперь перейдите на сервер Ubuntu и пропишите следующую команду:

```
sudo docker pull jenkins
```

![](https://i.imgur.com/FKtTSXm.jpg)

Для запуска Jenkins, вам необходимо выполнить команду ниже:

```
sudo docker run -p 8080:8080 -p 50000:50000 jenkins
```

Поясним некоторые момента вызова команды **sudo**:

- **sudo** — используется для обеспечения выполнения команды  с root-правами.
- **jenkins** — это имя образа, который мы хотим загрузить с Docker Hub и установить на наш сервер Ubuntu.
- **-p** — используется для сопоставления номера порта внутреннего образа Docker с нашим основным сервером Ubuntu, чтобы мы могли соответствующим образом получить доступ к контейнеру.

![](https://i.imgur.com/kLgj3j3.jpg)

В конечном итоге, Jenkins будет успешно работать в качестве контейнера на компьютере с Ubuntu.

Источник: [Docker — Hub](https://www.tutorialspoint.com/docker/docker_hub.htm)
