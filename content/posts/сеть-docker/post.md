---
title: "Сеть Docker"
slug: "сеть-docker"
date: 2019-08-16T00:00:00+03:00
categories: ["Docker"]
tags: ["Курс молодого бойца Docker"]
draft: false
---

![](/posts/сеть-docker/docker20.jpg)

Docker заботится о сетевых аспектах, так что контейнеры могут коммуницировать друг с другом, а также с хостом Docker.
Если вы пропишите **ifconfig** на хосте Docker, то увидите Ethernet адаптер Docker, который создаётся во время установки
Docker на хост Docker.

![](https://i.imgur.com/frNeyhf.jpg)

Это мост между хостом Docker и хостом Linux. Теперь давайте детальнее рассмотрим некоторые команды, связанные с сетью в Docker.

## Перечисление всех сетей Docker

Команда ниже может быть использована для вывода списка всех сетей на хосте Docker:

```
docker network ls
```

Данная команда выведет все сети на хосте Docker.

### Пример

```
sudo docker network ls
```

Пример вывода:

![](https://i.imgur.com/LDODhtP.jpg)

## Проверка сети Docker

Если вы хотите увидеть более подробную информацию о сети, связанной с Docker, вы можете использовать команду Docker
**network inspect**.

```
docker network inspect networkname
```

- **networkname** — это имя сети, которую необходимо проверить.

Вывод вернёт подробную информацию о сети.

### Пример

```
sudo docker network inspect bridge
```

Пример вывода:

![](https://i.imgur.com/Q9OohTo.jpg)

Теперь давайте запустим контейнер и посмотрим, что произойдёт, когда мы снова проверим сеть. Воспользуемся следующей
командой:

```
sudo docker run –it ubuntu:latest /bin/bash
```

![](https://i.imgur.com/lhvzP0M.jpg)

Теперь, если мы проверим имя нашей сети с помощью следующей команды, мы увидим, что контейнер подключился к мосту:

```
sudo docker network inspect bridge
```

![](https://i.imgur.com/Cc2N8jw.jpg)

## Создание своей собственной новой сети

Можно создать сеть в Docker перед запуском контейнеров с помощью следующей команды:

```
docker network create –-driver drivername name
```

- **drivername** — имя сетевого драйвера.
- **name** — имя сети.

Вывод вернёт ID сети.

### Пример

```
sudo docker network create –-driver bridge new_nw
```

Пример вывода:

![](https://i.imgur.com/MkgtPZ6.jpg)

Вы можете подключить новую сеть во время запуска контейнера с помощью следующей команды:

```
sudo docker run –it –network=new_nw ubuntu:latest /bin/bash
```

![](https://i.imgur.com/dLT6bq3.jpg)

И теперь, когда вы проверите сеть с помощью следующей команды, вы увидите, что контейнер подключён к сети:

```
sudo docker network inspect new_nw
```

![](https://i.imgur.com/8sujOid.jpg)

Источник: [Docker — Networking](https://www.tutorialspoint.com/docker/docker_networking.htm)
