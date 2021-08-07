---
title: "Как создать страницу входа на Django-сайт при помощи Python"
slug: "как-создать-страницу-входа-на-django-сайт-пр"
date: 2019-08-02T00:00:00+03:00
categories: ["Программирование", "Веб-разработка", "Django"]
draft: false
---

![](/posts/как-создать-страницу-входа-на-django-сайт-пр/shapka19.jpg)

В данной статье мы покажем вам, как создать страницу входа на Django-сайт при помощи **Python**.

**Предполагается, что вы уже знаете, как создать страницу регистрации**, то есть страницу, на которой посетители могут
зарегистрироваться и получить учетную запись.

Страница входа же предназначена для пользователей, которые уже имеют учётную запись на вашем сайте, и позволяет им войти
в свой аккаунт.

Перейдём непосредственно к коду создания страницы входа в Django.

## Страница шаблона login.html

Первым делом мы создадим файл шаблона страницы входа. Назовём эту страницу — **login.html**.

На этой странице будет располагаться заголовок, который сообщает о том, что это страница входа в систему, а также форма
входа, состоящая из двух полей: имя пользователя и пароль.

Обратим внимание на следующий код:

```
<html>

<body>
<h1>Login Page </h1>

<form action="" method="POST">
{% csrf_token %}
{% if error %}
{{ error }}
{% endif %}
{{ form.as_p }}
<input type="submit" name="submit" value="Log in"/>
</form>

</html>

</body>
```

Итак, на этой странице располагается наша форма входа.

## Файл forms.py

Далее мы перейдем к нашему файлу **forms.py**, который содержит нашу форму.

В файле шаблона мы указали переменную формы, но не создали её. Исправим это создав форму **Loginform** следующим образом:

```
from django import forms

class Loginform(forms.Form):
    username= forms.CharField(max_length= 25,label="Enter username")
    password= forms.CharField(max_length= 30, label='Password', widget=forms.PasswordInput)
```

Вам необходимо импортировать формы из Django в каждом файле **forms.py**.

После чего мы создаем форму **Loginform**, которая является экземпляром класса **Form**.

Эта форма имеет только 2 поля: имя пользователя и пароль, то есть всё, что нам нужно для входа в систему.

В свою очередь, имя пользователя может содержать не более 25 символов, а пароль — не более 30.

## Файл urls.py

Итак, мы собираемся создать нашу отдельную страницу входа в систему по адресу URL **/siteusers/login**.

Открываем файл **urls.py** в приложении **siteusers** и затем приводим код к следующему виду:

```
from django.conf.urls import url
from django.contrib import admin
from .views import signup, pagelogin
urlpatterns = [
    url(r'^signup$', signup, name='signup'),
    url(r'^login$', pagelogin, name='login'),   
]
```

Таким образом мы перейдём на страницу входа с URL-адресом **http://IP_address/siteusers/login**

## Файл views.py

Напоследок обратимся к нашему файлу **views.py**, содержимое которого показано ниже:

```
from django.shortcuts import render
from .forms import Signupform, Loginform
from django.contrib.auth.models import User
from django.contrib.auth import authenticate, login

def pagelogin(request):

    uservalue=''
    passwordvalue=''

    form= Loginform(request.POST or None)
    if form.is_valid():
        uservalue= form.cleaned_data.get("username")
        passwordvalue= form.cleaned_data.get("password")

        user= authenticate(username=uservalue, password=passwordvalue)
        if user is not None:
            login(request, user)
            context= {'form': form,
                      'error': 'The login has been successful'}
            
            return render(request, 'siteusers/login.html', context)
        else:
            context= {'form': form,
                      'error': 'The username and password combination is incorrect'}
            
            return render(request, 'siteusers/login.html', context )

    else:
        context= {'form': form}
        return render(request, 'siteusers/login.html', context)
```

Чтобы создать пользователя нам необходимо импортировать **render** для отображения страницы шаблона, форму **Loginform**
из файла **forms.py** и **User** из **django.contrib.auth.models**.

После чего мы вызываем функцию **pagelogin()**. Вы можете называть функцию как угодно, только не называйте ее **login()**,
так как в Django уже есть встроенная функция **login()**. Если вы назовете функциональное представление **login()**, Django
не поймёт, на какую функцию вы ссылаетесь, и скрипт не будет работать, поэтому назовите эту функцию как-нибудь иначе.

Также у нас есть две переменные — **uservalue** и **passwordvalue**. Устанавливаем их значение равным пустым строкам,
чтобы сделать переменные глобальными.

Далее мы создаём экземпляр form формы **Loginform**, которая была описана нами в файле **forms.py**.

Если форма верна, мы устанавливаем переменную **uservalue** равной полю **username**, а переменную **passwordvalue**
равной полю **password**.

Затем мы аутентифицируем пользователя на основе предоставленного им имени пользователя и пароля. Django имеет встроенную
функцию аутентификации.

Если пользователь существует (т.е. не **None**), то мы регистрируем пользователя с помощью функции **login()**. В эту
функцию передаём запрос и пользователя.

Для простоты, в контекстный словарь передаем форму и сообщение об ошибке.

Если пользователя не существует, то это означает, что комбинация имени пользователя и пароля неверна.

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Источник: [How to Create a Login Page for a Website with Python in Django](http://www.learningaboutelectronics.com/Articles/How-to-create-a-login-page-for-a-website-Python-Django.php)
