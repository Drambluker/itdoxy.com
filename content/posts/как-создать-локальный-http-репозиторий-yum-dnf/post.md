---
title: "Как создать локальный HTTP-репозиторий Yum/DNF в RHEL 8"
slug: "как-создать-локальный-http-репозиторий-yum-dnf"
date: 2019-07-08T00:00:00+03:00
categories: ["Linux"]
draft: false
---

![](/posts/как-создать-локальный-http-репозиторий-yum-dnf/shapka15.jpg)

**_Репозиторий_** программного обеспечения — это место для хранения и поддержки программных пакетов RPM для дистрибутива
Redhat Linux, откуда пользователи могут загружать и устанавливать пакеты на своих серверах Linux.

Обычно репозитории хранятся в общественной сети, к которой могут обращаться сразу несколько пользователей в Интернете.
Однако вы можете создать свой собственный локальный репозиторий на вашем сервере и предоставить доступ к нему как себе,
так и другим компьютерам в вашей локальной сети при помощи веб-сервера HTTP.

Преимущество создания локального репозитория заключается в том, что вам не требуется подключение к Интернету для установки
программного обеспечения или обновлений.

**_YUM (Yellowdog Updater Modified)_** или **_DNF (Dandified YUM)_** — это широко распространённая утилита для управления
пакетами ПО для систем Linux, основанных на **_RPM (RedHat Package Manager)_**. Данная утилита упрощает установку ПО в
RedHat/CentOS Linux.

В данной статье, мы объясним как настроить локальный репозиторий **_YUM/DNF_** в **_RHEL 8_**, используя установочный
DVD или ISO-образ. Мы также покажем вам, как найти и установить пакеты ПО на клиентских компьютерах **_RHEL 8_**, используя
**_HTTP-сервер Nginx_**.

## Среда тестирования

```
Local Repository Server: RHEL 8 [192.168.0.106]
Local Client Machine: RHEL 8 [192.168.0.200]
```

## Шаг 1. Установка веб-сервера Nginx

1. Первым делом установите **_HTTP-сервер Nginx_** с помощью **_диспетчера пакетов DNF_**:

```
# dnf install nginx
```

![Установка Nginx в RHEL 8](https://i.imgur.com/qgNFYmX.png)

2. После завершения установки, необходимо включить автоматический запуск службы **_Nginx_** во время загрузки системы и
   проверить состояние с помощью следующих команд:

```
# systemctl start nginx
# systemctl enable nginx
# systemctl status nginx
```

![Проверка Nginx в RHEL 8](https://i.imgur.com/EtgNLk1.png)

3. Теперь вам необходимо открыть **_80_** и **_443_** порты **_Nginx_** в фаерволе:

```
# firewall-cmd --zone=public --permanent --add-service=http
# firewall-cmd --zone=public --permanent --add-service=https
# firewall-cmd --reload
```

![Открываем порт в фаерволе RHEL 8](https://i.imgur.com/Dfh1DRC.png)

4. Убедитесь, что ваш сервер **_Nginx_** запущен и работает, перейдя по следующему URL-адресу в веб-браузере. Вы увидите
   веб-страницу Nginx по-умолчанию.

```
http://SERVER_DOMAIN_NAME_OR_IP
```

![Проверка работы Nginx в RHEL 8](https://i.imgur.com/t8MC1BG.png)

## Шаг 2. Монтирование установочного DVD/ISO-образа в RHEL 8

5. Создайте точку монтирования локального репозитория в корневом каталоге документов **_/var/www/html/_** и смонтируйте
   загруженный **_DVD/ISO-образ_** в каталог **_/mnt_**.

```
# mkdir /var/www/html/local_repo
# mount -o loop rhel-8.0-x86_64-dvd.iso /mnt  [Mount Download ISO File]
# mount /dev/cdrom /mnt                       [Mount DVD ISO File from DVD ROM]
```

6. Локально скопируйте файлы ISO в каталог **_/var/www/html/local_repo_** и проверьте содержимое с помощью ls команды:

```
# cd /mnt
# tar cvf - . | (cd /var/www/html/local_repo/; tar xvf -)
# ls -l /var/www/html/local_repo/
```

![Проверка содержимого ISO файлов в RHEL 8](https://i.imgur.com/Vjuvalg.png)

## Шаг 3. Настройка локального репозитория

7. Настало время для настройки локального репозитория. Вам необходимо создать файл настройки в директории **_/etc/yum.repos.d/_**
   и установить соответствующие разрешения для файла, как показано ниже:

```
# touch /etc/yum.repos.d/local-rhel8.repo
# chmod  u+rw,g+r,o+r  /etc/yum.repos.d/local-rhel8.
```

8. Затем отредактируйте созданный файл:

```
# vim /etc/yum.repos.d/local.repo
```

9. Скопируйте и вставьте в файл следующее:

```
[LocalRepo_BaseOS]
name=LocalRepo_BaseOS
metadata_expire=-1
enabled=1
gpgcheck=1
baseurl=file:///var/www/html/local_repo/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

[LocalRepo_AppStream]
name=LocalRepo_AppStream
metadata_expire=-1
enabled=1
gpgcheck=1
baseurl=file:///var/www/html/local_repo/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```

Сохраните внесённые изменения и закройте файл.

10. Теперь вам нужно установить необходимые пакеты для создания, настройки и управления вашим локальным репозиторием,
    используя следующую команду:

```
# yum install createrepo  yum-utils
# createrepo /var/www/html/local_repo/
```

## Шаг 4. Тестирование локального репозитория

11. На этом шаге вам необходимо запустить процесс очистки временных файлов, которые хранятся в репозиториях, используя
    следующую команду:

```
# yum clean all
```

или

```
# dnf clean all
```

12. Затем убедитесь, что созданные репозитории отображаются в списке включенных репозиториев.

```
# dnf repolist
```

или

```
# dnf repolist  -v  #shows more detailed information
```

![Проверка локального репозитория в RHEL 8](https://i.imgur.com/fyLQgP9.png)

13. Попробуйте установить пакет из локальных репозиториев, например, установите инструмент командной строки Git, как
    описано ниже:

```
# dnf install git
```

![Установка пакета из локального репозитория Yum в RHEL8](https://i.imgur.com/yqYkGMO.png)

Как видно из вывода вышеуказанной команды, пакет **_Git_** устанавливается из репозитория **_LocalRepo_AppStream_**, что
и показано на скриншоте. Это означает, что локальные репозитории включены и исправно работают.

## Шаг 5. Установка локального репозитория YUM на клиентских компьютерах

14. Теперь на клиентских машинах **_RHEL 8_** добавьте ваши локальные репозитории в конфигурацию YUM:

```
# vi /etc/yum.repos.d/local-rhel8.repo
```

Скопируйте и вставьте конфигурацию, описанную ниже, в файл. Обязательно замените **_baseurl_** IP-адресом или доменом
своего сервера.

```
[LocalRepo_BaseOS]
name=LocalRepo_BaseOS
enabled=1
gpgcheck=0
baseurl=http://192.168.0.106

[LocalRepo_AppStream]
name=LocalRepo_AppStream
enabled=1
gpgcheck=0
baseurl=http://192.168.0.106
```

Сохраните внесённые изменения и начните использовать локальные зеркала YUM.

15. Затем пропишите следующую команду, чтобы увидеть ваши локальные репозитории в списке доступных на клиентских машинах
    репозиториев YUM:

```
# dnf repolist
```

![Проверка локального репозитория клиента в RHEL 8](https://i.imgur.com/Or3eDa9.png)

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Оригинальная статья: [How to Create Local HTTP Yum/DNF Repository on RHEL 8](https://www.tecmint.com/create-local-http-yum-dnf-repository-on-rhel-8/)
