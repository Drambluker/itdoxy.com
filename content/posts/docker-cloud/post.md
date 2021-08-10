---
title: "Docker Cloud"
slug: "docker-cloud"
date: 2019-08-27T02:00:00+03:00
categories: ["Docker"]
tags: ["Курс молодого бойца Docker"]
draft: false
---

![](/posts/docker-cloud/docker26.jpg)

Docker Cloud — это сервис, предоставляемый Docker, в котором вы можете выполнять следующие операции:

- **Nodes** — подключение Docker Cloud к своей облачной платформе, например, Azure или AWS, чтобы ускорить работу
контейнеров в этих средах.
- **Cloud Repository** — предоставление места, где вы можете хранить свои собственные репозитории.
- **Continuous Integration** — соединение с **GitHub** и создание конвейера непрерывной интеграции.
- **Application Deployment** — развертывание и масштабирование инфраструктуры и контейнеров.
- **Continuous Deployment** — автоматизация развертывания.

## Начало работы

Перейдите [по следующей ссылке](https://cloud.docker.com/), чтобы начать работу с Docker Cloud.

![](https://i.imgur.com/lepEeZ2.jpg)

После входа в систему вы увидите следующую страницу:

![](https://i.imgur.com/v2qPgWn.jpg)

## Подключение к облачному провайдеру

Первым делом мы подключаемся к существующему облачному провайдеру. Следующие шаги покажут вам как подключиться к облачному
провайдеру Amazon.

**Шаг 1.** Первым делом убедитесь в том, что у вас есть ключи AWS, получить которые вы можете во вкладке **AWS Console**.
Войдите в свой **aws** аккаунт [по следующей ссылке](https://aws.amazon.com/ru/console/).

![](https://i.imgur.com/kJ0tb8f.jpg)

**Шаг 2.** После того как войдёте, перейдите в раздел «Security Credentials». Запишите ключи доступа, которые будут
использоваться в Docker Hub.

![](https://i.imgur.com/g5fqpQS.jpg)

**Шаг 3.** Затем вам необходимо создать политику в **aws**, которая позволит Docker просматривать экземпляры EC2. Перейдите
в раздел профилей в **aws**. Нажмите кнопку «**Create Policy**».

![](https://i.imgur.com/RKEhfe7.jpg)

**Шаг 4.** Нажмите “Create Your Own Policy” (“Создать собтсвенную политику”) и укажите **Polciy Name** как **dockercloudpolicy**,
а также заполните **Policy Document**, как показано ниже:

```
{
    "Version": "2012-10-17",
    "Statement": [ {
        "Action": [
            "ec2:*",
            "iam:ListInstanceProfiles"
        ],
        "Effect": "Allow",
        "Resource": "*"
    } ]
}
```

![](https://i.imgur.com/Q6BVJ6L.jpg)

Затем нажмите на кнопку «**Create Policy**».

**Шаг 5.** После чего вам необходимо создать **role** (роль), которая будет использоваться Docker для развёртывания узлов
на AWS. Для этого перейдите в раздел «**Roles**» в AWS и нажмите на опцию «**Create New Role**».

![](https://i.imgur.com/D2rbLCd.jpg)

**Шаг 6.** Задайте следующее название роли — **dockercloud-role**.

![](https://i.imgur.com/cd6Uc56.jpg)

**Шаг 7.** На следующем экране перейдите в “Role for Cross Account Access” и выберите “Provide access between your account
and a 3rd AWS account”.

![](https://i.imgur.com/Ft0JzxI.jpg)

**Шаг 8.** Далее введите следующие данные:

- В поле Account ID введите ID для сервиса Docker Cloud: 689684103426.
- В поле External ID введите своё имя пользователя в Docker Cloud.

![](https://i.imgur.com/AH3vEqc.jpg)

**Шаг 9.** Затем нажмите на кнопку «**Next Step**» и на следующем экране прикрепите политику, созданную на одном
из предыдущих шагов.

![](https://i.imgur.com/7OP5fjE.jpg)

**Шаг 10.** Наконец, на последнем экране, когда роль создана, обязательно скопируйте **arn** созданной роли.

```
arn:aws:iam::085363624145:role/dockercloud-role
```

![](https://i.imgur.com/xfTwdHx.jpg)

**Шаг 11.** Теперь вернитесь в **Docker Cloud**, выберите **Cloud Providers** и щёлкните на значок **plug symbol** рядом
с Amazon Web Services.

![](https://i.imgur.com/rqRSWMg.jpg)

Введите **arn** роли и нажмите на кнопку «**Save**».

![](https://i.imgur.com/n18rjgq.jpg)

После сохранения, интеграция с AWS будет успешно завершена.

![](https://i.imgur.com/dWWPv2q.jpg)

## Настройка узлов

Как только интеграция с AWS будет завершена, следующим шагом будет настройка узла.

**Шаг 1.** Перейдите в раздел «Nodes» в Docker Cloud. Заметьте, что при настройке узлов сначала будет автоматически
настроен кластер узлов.

![](https://i.imgur.com/42r7BMt.jpg)

**Шаг 2.** Далее вы можете указать детали узлов, которые будут настроены в AWS.

![](https://i.imgur.com/WUEOXcA.jpg)

Вы можете нажать кластер «Launch Node», который будет отображаться в нижней части экрана. Как только узел будет развёрнут,
вы получите уведомление на экране “Node Cluster”.

![](https://i.imgur.com/1EaFYPE.jpg)

## Развёртывание службы

Следующим шагом после развёртывания узла идёт развёртывание службы. Для этого нам необходимо выполнить следующие шаги.

**Шаг 1.** Перейдите в секцию «**Services**» в Docker Cloud. Нажмите на кнопку «**Create**».

![](https://i.imgur.com/AD78AAt.jpg)

**Шаг 2.** Выберите необходимый сервис. В нашем случае выберем **mongo**.

![](https://i.imgur.com/maTUmKJ.jpg)

**Шаг 3.** На следующем экране выберите опцию «**Create & Deploy**». Она начнёт развёртывание контейнера **Mongo**
на вашем кластере узлов.

![](https://i.imgur.com/yrOdyhg.jpg)

После развёртывания вы сможете увидеть контейнер в рабочем состоянии.

![](https://i.imgur.com/Xus6tlo.jpg)

Источник: [Docker — Cloud](https://www.tutorialspoint.com/docker/docker_cloud.htm)
