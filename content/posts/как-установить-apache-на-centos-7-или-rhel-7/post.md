---
title: "Как установить Apache в CentOS 7 или RHEL 7"
date: 2019-04-08T00:00:00+03:00
categories: ["Администрирование"]
tags: ["linux"]
draft: false
---

![](/posts/как-установить-apache-на-centos-7-или-rhel-7/shapka4-1.jpg)

Apache — это бесплатный HTTP-сервер с открытым исходным кодом, который работает на Unix-подобных операционных системах,
включая Linux и Windows. С момента своего релиза, Apache был самым популярным веб-сервером, обслуживающим сразу несколько
сайтов. Он прост в установке и настройке, а также удобен для размещения одного или нескольких веб-сайтов на Linux или
Windows серверах.

В этой статье мы расскажем как установить и настроить веб-сервер Apache, а также как им управлять, на CentOS 7 или
RHEL 7 при помощи командной строки.

## Установка HTTP-сервера Apache

1. Для начала, обновите пакеты программного обеспечения до последней версии.

```
# yum -y update
```

2. Затем, установите HTTP-сервер Apache, используя менеджер пакетов YUM:

```
# yum install httpd
```

![Установка Apache в CentOS 7](https://www.tecmint.com/wp-content/uploads/2017/07/Install-Apache-on-CentOS-7.png)

## Управление HTTP-сервером Apache

3. После установки Apache его можно запустить и добавить в автозапуск.

```
# systemctl start httpd
# systemctl enable httpd
# systemctl status httpd
```

![Запуск и включение Apache](https://www.tecmint.com/wp-content/uploads/2017/07/Start-Enable-Apache.png)

## Настройка фаервола

По умолчанию встроенный фаервол CentOS 7 блокирует трафик Apache. Чтобы разрешить веб-трафик на Apache, обновите правила
фаервола, разрешив входящие пакеты на HTTP и HTTPS:

```
# firewall-cmd --zone=public --permanent --add-service=http
# firewall-cmd --zone=public --permanent --add-service=https
# firewall-cmd --reload
```

![Настройка фаервола](https://www.tecmint.com/wp-content/uploads/2017/07/Allow-Apache-on-Firewalld.png)

## Тестирование HTTP-сервера Apache

5. Теперь вы можете проверить сервер Apache, перейдя по следующему URL-адресу (будет показана стандартная страница Apache):

```
http://ДОМЕННОЕ_ИМЯ_СЕРВЕРА_ИЛИ_IP
```

![Стандартная страница приветствия Apache](https://www.tecmint.com/wp-content/uploads/2017/07/Default-Apache-Welcome-Page-on-CentOS-7.png)

## Настройка виртуальных хостов на основе имён в CentOS 7

Этот раздел полезен лишь в том случае, если вы хотите разместить более одного домена (виртуального хоста) на одном
веб-сервере Apache. Существует множество способов настройки виртуального хоста, однако, в этой статье мы покажем один из
самых простых.

6. Первым делом, создайте **_vhost.conf_** в директории **‘/etc/httpd/conf.d/’**, для хранения нескольких конфигураций
   виртуального хоста.

```
# vi /etc/httpd/conf.d/vhost.conf
```

Добавьте следующий пример шаблона директивы виртуального хоста для веб-сайта mytecmint.com (не забудьте изменить
необходимые значения для вашего собственного домена).

```
NameVirtualHost *:80

<VirtualHost *:80>
ServerAdmin webmaster@mytecmint.com
ServerName mytecmint.com
ServerAlias www.mytecmint.com
DocumentRoot /var/www/html/mytecmint.com/
ErrorLog /var/log/httpd/mytecmint.com/error.log
CustomLog /var/log/httpd/mytecmint.com/access.log combined
</VirtualHost>
```

![Настройки виртуального хоста Apache](https://www.tecmint.com/wp-content/uploads/2017/07/Apache-Virtual-Host-Configurations.png)

**Важно:** вы можете добавить сколько угодно доменов в **_vhost.conf_**, просто скопировав блок **_VirtualHost_**,
описанный выше, и изменив значения для каждого добавляемого домена.

7. Теперь создайте каталоги для сайта mytecmint.com, как указано выше в блоке **_VirtualHost_**.

```
# mkdir -p /var/www/html/mytecmint.com    [Document Root - Add Files]
# mkdir -p /var/log/httpd/mytecmint.com   [Log Directory]
```

8. Создайте временную страницу index.html в **‘/var/www/html/mytecmint.com’**.

```
# echo "Welcome to My TecMint Website" > /var/www/html/mytecmint.com/index.html
```

9. Перезапустите службу Apache для того, чтобы изменения вступили в силу.

```
# systemctl restart httpd.service
```

10. Теперь вы можете зайти на mytecmint.com и проверить страницу индекса, созданную выше.

![Проверка работоспособности сайта](https://www.tecmint.com/wp-content/uploads/2017/07/Check-Virtualhost-Website.png)

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Оригинальная статья: [How to Install Apache on CentOS 7](https://www.tecmint.com/install-apache-on-centos-7/)
