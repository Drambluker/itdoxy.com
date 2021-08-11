---
title: "Отправка электронной почты Django"
slug: "отправка-электронной-почты-django"
date: 2019-09-26T02:00:00+03:00
categories: ["Программирование", "Веб-разработка", "Django"]
tags: ["Курс молодого бойца Django"]
draft: false
---

![](/posts/отправка-электронной-почты-django/Django12.jpg)

Django поставляется с готовым и простым в использовании легковесным движком для отправки электронной почты. Для его использования
вам просто нужно импортировать **django.core.mail**. Чтобы начать отправку электронной почты, отредактируйте файл проекта
**settings.py** и установите следующие параметры:

- **EMAIL_HOST** — адрес SMTP-сервера.
- **EMAIL_HOST_USER** — учётная запись для входа на SMTP-сервер.
- **EMAIL_HOST_PASSWORD** — пароль от учётной записи для входа на SMTP-сервер.
- **EMAIL_PORT** — порт SMTP-сервера.
- **EMAIL_USE_TLS** или **_SSL** — True, если используется безопасное соединение.

## Отправка простого электронного письма

Давайте создадим представление **sendSimpleEmail** для отправки простого электронного письма.

```
from django.core.mail import send_mail
from django.http import HttpResponse

def sendSimpleEmail(request,emailto):
    res = send_mail("hello paul", "comment tu vas?", "paul@polo.com", [emailto])
    return HttpResponse('%s'%res)
```

**send_mail** принимает следующие параметры:

- **subject** − тема электронного письма.
- **message** − тело электронного письма.
- **from_email** − электронная почта отправителя.
- **recipient_list** − список адресов электронных почт получателей.
- **fail_silently** − при значении false **send_mail** вызовет исключение в случае ошибки.
- **auth_user** − учетная запись пользователя, если она не указана в **settings.py**.
- **auth_password** − пароль от учётной записи, если он не указан в **settings.py**.
- **connection** − бэкенд электронной почты.
- **html_message** − (начиная с Django 1.7) если данный параметр присутствует, то электронное письмо будет составным.

Давайте создадим URL для доступа к нашему представлению:

```
from django.conf.urls import patterns, url

urlpatterns = paterns('myapp.views', url(r'^simpleemail/(?P<emailto>
    [\w.%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4})/',
    'sendSimpleEmail' , name = 'sendSimpleEmail'),)
```

При доступе к **/myapp/simpleemail/polo@gmail.com** вы получите следующую страницу:

![](https://i.imgur.com/KSS3Y6e.png)

## Отправка нескольких писем с помощью send_mass_mail

Метод возвращает количество успешно доставленных сообщений. Это то же самое, что **send_mail**, но с дополнительным
параметром **datatuples**. Тогда наше представление **sendMassEmail** примет следующий вид:

```
from django.core.mail import send_mass_mail
from django.http import HttpResponse

def sendMassEmail(request,emailto):
    msg1 = ('subject 1', 'message 1', 'polo@polo.com', [emailto1])
    msg2 = ('subject 2', 'message 2', 'polo@polo.com', [emailto2])
    res = send_mass_mail((msg1, msg2), fail_silently = False)
    return HttpResponse('% s '%res)
```

Давайте создадим URL для доступа к нашему представлению:

```
from django.conf.urls import patterns, url

urlpatterns = paterns('myapp.views', url(r'^massEmail/(?P<emailto1>
    [\w.%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4})/(?P<emailto2>
    [\w.%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4})', 'sendMassEmail' , name = 'sendMassEmail'),)
```

При доступе к **/myapp/massemail/polo@gmail.com/sorex@gmail.com/** мы получим:

![](https://i.imgur.com/cNKtF4C.png)

**send_mass_mail** принимает следующие параметры:

- **datatuples** — кортеж элементов следующего вида: тема, сообщение, почта отправителя, список получателей.
- **fail_silently** − при значении false **send_mass_mail** вызовет исключение в случае ошибки.
- **auth_user** − учетная запись пользователя, если она не указана в **settings.py**.
- **auth_password** − пароль от учётной записи, если он не указан в **settings.py**.
- **connection** − бэкенд электронной почты.

Как вы можете видеть на изображении выше, два сообщения были успешно отправлены.

**Примечание.** В этом примере мы используем **Python smtp debuggingserver**, который вы можете запустить с помощью:

```
$python -m smtpd -n -c DebuggingServer localhost:1025
```

Эта строчка означает, что все отправленные вами электронные письма будут напечатаны в стандартном системном выводе **stdout**,
а фиктивный сервер будет работать на **localhost:1025**.

## Отправка электронной почты администраторам и менеджерам с помощью методов mail_admins и mail_manager

Эти методы отправляют электронные письма администраторам сайта, которые определены в параметре **ADMINS** файла **settings.py**,
и менеджерам сайта, которые определено в параметре **MANAGERS** файла **settings.py**. Давайте предположим, что наши параметры
**ADMINS** и **MANAGERS** выглядят следующим образом:

**ADMINS** = ((‘polo’, ‘polo@polo.com’),)

**MANAGERS** = ((‘popoli’, ‘popoli@polo.com’),)

```
from django.core.mail import mail_admins
from django.http import HttpResponse

def sendAdminsEmail(request):
    res = mail_admins('my subject', 'site is going down.')
    return HttpResponse(' %s '%res)
```

Приведенный выше код отправит электронное письмо каждому администратору, указанному в **ADMINS**.

```
from django.core.mail import mail_managers
from django.http import HttpResponse

def sendManagersEmail(request):
    res = mail_managers('my subject 2', 'Change date on the site.')
    return HttpResponse(' %s '%res)
```

Приведенный выше код отправит электронное письмо каждому менеджеру, указанному в **MANAGERS**.

Параметры:

- **subject** − тема электронного письма.
- **message** − тело электронного письма.
- **fail_silently** − при значении false вызовет исключение в случае ошибки.
- **connection** − бэкенд электронной почты.
- **html_message** − (начиная с Django 1.7) если данный параметр присутствует, то электронное письмо будет составным.

## Отправка электронной почты в формате HTML

Отправка HTML-сообщения в Django> = 1.7:

```
from django.core.mail import send_mail

from django.http import HttpResponse
    res = send_mail("hello paul", "comment tu vas?", "paul@polo.com",
        ["polo@gmail.com"], html_message=")
```

Данный код создает составное электронное письмо.

Для Django < 1.7 отправка HTML-сообщений осуществляется через класс **django.core.mail.EmailMessage**, а затем для объекта
вызывается метод **send**.

Давайте создадим представление **sendHTMLEmail** для отправки электронного письма в формате HTML:

```
from django.core.mail import EmailMessage
from django.http import HttpResponse

def sendHTMLEmail(request , emailto):
    html_content = "<strong>Comment tu vas?</strong>"
    email = EmailMessage("my subject", html_content, "paul@polo.com", [emailto])
    email.content_subtype = "html"
    res = email.send()
    return HttpResponse(' %s '%res)
```

Параметры при создании экземпляра класса **EmailMessage**:

- **subject** − тема электронного письма.
- **message** − тело электронного письма в формате HTML .
- **from_email** − электронная почта отправителя.
- **to** − список адресов электронных почт получателей.
- **bcc** − список адресов электронных почт получателей bcc.
- **connection** − бэкенд электронной почты.

Давайте создадим URL для доступа к нашему представлению:

```
from django.conf.urls import patterns, url

urlpatterns = paterns('myapp.views', url(r'^htmlemail/(?P<emailto>
    [\w.%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4})/',
    'sendHTMLEmail' , name = 'sendHTMLEmail'),)
```

При доступе к **/myapp/htmlemail/polo@gmail.com** мы получаем следующее:

![](https://i.imgur.com/PzIblU7.png)

## Отправка электронных писем с вложением

Это делается с помощью метода **attach** объекта **EmailMessage**.

Представление для отправки электронного письма с вложением будет следующим:

```
from django.core.mail import EmailMessage
from django.http import HttpResponse

def sendEmailWithAttach(request, emailto):
    html_content = " Comment tu vas? "
    email = EmailMessage("my subject", html_content, "paul@polo.com", emailto])
    email.content_subtype = "html"
    
    fd = open('manage.py', 'r')
    email.attach('manage.py', fd.read(), 'text/plain')
    
    res = email.send()
    return HttpResponse(' %s '%res)
```

**attach** принимает следующие аргументы:

- **filename** — имя файла-вложения.
- **content** — содержимое файла-вложения.
- **mimetype** — mime-тип его содержимого.

Источник: [Django — Sending E-mails](https://www.tutorialspoint.com/django/django_sending_emails.htm)
