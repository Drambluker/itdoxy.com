---
title: "Работа Kubernetes Docker"
slug: "работа-kubernetes-docker"
date: 2019-08-29T00:00:00+03:00
categories: ["Docker"]
tags: ["Курс молодого бойца Docker"]
draft: false
---

![](/posts/работа-kubernetes-docker/docker31.jpg)

В данной главе мы рассмотрим установку Kubernetes через **kybeadm**. Это утилита, помогающая в установке Kubernetes.
Перейдём непосредственно к установке.

**Шаг 1.** Убедитесь в том, что версия вашего **Ubuntu-сервера** не ниже **16.04**.

**Шаг 2.** Убедитесь в том, что вы уже сгенерировали **ssh-ключ**, который необходим для входа по **ssh**. Ключ можно
получить воспользовавшись следующей командой:

```
ssh-keygen
```

Она сгенерирует ключ в вашей **домашней папке**, как показано ниже:

![](https://i.imgur.com/xFOXLDh.jpg)

**Шаг 3.** Далее, в зависимости от версии вашей Ubuntu, необходимо добавить соответствующий сайт в **docker.list**
для **менеджера пакетов apt**, чтобы он мог обнаруживать **пакеты Kubernetes** с сайта **kubernetes** и загружать
их соответствующим образом.

Сделать это можно с помощью следующей команды:

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -  
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main” | sudo tee /etc/apt/sources.list.d/docker.list
```

**Шаг 4.** Затем мы запускаем обновление **apt-get**, чтобы убедиться, что все пакеты загружены на сервер Ubuntu.

![](https://i.imgur.com/ojPAi4W.jpg)

**Шаг 5.** Установите пакет Docker, как описано [в предыдущих главах](https://itdoxy.com/установка-docker/).

**Шаг 6.** Теперь пришло время установить **kubernetes** путём установки следующих пакетов:

```
apt-get install –y kubelet kubeadm kubectl kubernetes-cni
```

![](https://i.imgur.com/36JIs3i.jpg)

![](https://i.imgur.com/zU6ldGF.jpg)

**Шаг 7.** После загрузки всех пакетов **kubernetes**, настало время для запуска контроллера **kubernetes**:

```
kubeadm init
```

![](https://i.imgur.com/ia0T5IQ.jpg)

После этого вы получите сообщение о том, что мастер успешно запущен и работает, и теперь узлы могут присоединиться
к кластеру.

Источник: [Docker — Working of Kubernetes](https://www.tutorialspoint.com/docker/docker_working_of_kubernetes.htm)
