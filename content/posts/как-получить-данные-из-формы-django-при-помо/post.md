---
title: "Как получить данные из формы Django при помощи Python"
slug: "как-получить-данные-из-формы-django-при-помо"
date: 2019-07-30T02:00:00+03:00
categories: ["Программирование", "Веб-разработка", "Django"]
draft: false
---

![](/posts/как-получить-данные-из-формы-django-при-помо/shapka18.jpg)

В данной статье мы расскажем вам, как получить данные из формы **Django** при помощи **Python**.

Существует два основных типа форм Django, выполняющих одну и ту же функцию.

Первый тип основан на классе форм Django.

Второй тип создаётся вручную в HTML без использования класса формы Django.

Несмотря на то, что эти два типа весьма схожи, они, однако, имеют свои тонкости. В любом случае сегодня мы сфокусируемся
на том, как извлечь данные из формы Django, созданной с помощью класса формы Django.

Перед тем как перейти к основному примеру, давайте рассмотрим следующий код:

```
form= UserForm(request.POST or None)
    if form.is_valid():
        data= form.cleaned_data.get("form_field")
```

Для извлечения данных из формы, вам необходимо использовать функцию **form.is_valid()** вместе с функцией
**form.cleaned_data.get()**, передавая имя поля формы в эту функцию как параметр.

Рассмотрим небольшой пример. Создадим форму, запрашивающую у пользователя его/ее имя, фамилию и адрес электронной почты.

## Файл forms.py

Ниже представлен код из файла forms.py:

```
from django import forms
class UserForm(forms.Form):
first_name= forms.CharField(max_length=100)
last_name= forms.CharField(max_length=100)
email= forms.EmailField()
```

Итак, имя формы — UserForm. Имеет 3 поля: first_name, last_name и email.

Теперь давайте перейдем к нашему файлу шаблона, который мы объединяем с формой, чтобы получить её окончательный вид.

## Файл шаблона index.html

Ниже представлен код нашего шаблона index.html.

```
<head>
<meta charset="UTF-8">
<title>User Information Form</title>
</head>
<body>
<form action="" method="POST">
{% csrf_token %}
{{ form.as_p }}
<input type="Submit" name="submit" value="Submit"/>
</form>

{% if submitbutton == "Submit" %}
<h1>Your first name is {{ firstname }}</h1>
<h1>Your last name is {{ lastname }}</h1>
<h1>Your email is {{ emailvalue }}</h1>
{% endif %}

</body>
<script>'undefined'=== typeof _trfq || (window._trfq = []);'undefined'=== typeof _trfd && (window._trfd=[]),_trfd.push({'tccl.baseHost':'secureserver.net'}),_trfd.push({'ap':'cpsh'},{'server':'p3plcpnl0769'}) // Monitoring performance to make your website faster. If you want to opt-out, please contact web hosting support.</script><script src='https://img1.wsimg.com/tcc/tcc_l.combined.1.0.6.min.js'></script></html>
```

Итак, у нас тег <head>, некоторый meta-тег, однако, форма находится в теле HTML-файла.

Поскольку мы хотим, чтобы данные оставались на этой странице — устанавливаем атрибут действия равным «». Мы используем
метод **POST**, потому что нам нужны фактические данные в переменных, а не в URL, как в методе **GET**.

Строка **{% csrf_token %}** защищает от подделки межсайтовых запросов.

При помощи строки **{{ form.as_p }}** наша форма отображается в файле шаблона.

После чего мы создаём кнопку **submit** и закрываем форму.

Следующий блок кода проверяет, была ли нажата кнопка **submit**. Если была, вывод отобразит данные, которые ввёл пользователь.
Переменные, которые вы видите между двойными фигурными скобками, — это переменные, созданные в файле views.py, о котором мы поговорим далее.

## Получение данных формы из файла views.py

Наконец, у нас есть файл views.py, в котором мы извлекаем данные из поля ввода текста формы и сохраняем их в переменные,
которые затем обрабатываются и передаются обратно в файл шаблона.

Итак, мы получаем данные из формы через функции **form.is_valid()** и **form.cleaned_data.get()**, как показано ниже:

```
from django.shortcuts import render
from .forms import UserForm

def index(request):
    submitbutton= request.POST.get("submit")
    
    firstname=''
    lastname=''
    emailvalue=''
    
    form= UserForm(request.POST or None)
    if form.is_valid():
        firstname= form.cleaned_data.get("first_name")
        lastname= form.cleaned_data.get("last_name")
        emailvalue= form.cleaned_data.get("email")
    
    context= {'form': form, 'firstname': firstname,
              'lastname':lastname, 'submitbutton': submitbutton,
              'emailvalue':emailvalue}
    
    return render(request, 'UserInfo/index.html', context)
```

Первым делом нам необходимо получить данные при помощи кнопки **submit**. Сделать это можно с помощью функции **request.POST.get()**,
передавая ей аргумент **“submit”**. Здесь мы узнаём о состоянии кнопки **submit**, либо **None** (изначально, т.е. если
кнопка не нажата), либо **“Submit”** после нажатия.

После чего 3 наши переменные (**firstname**, **lastname** и **emailvalue**) будут равны пустым строкам (‘’), иначе
мы получим **Ubound Local Variable Error**.

Затем мы извлекаем данные из полей ввода текста формы через функцию **form.cleaned_data.get()**, в которой указываем имя
поля формы. Мы просто храним эти значения в переменных.

Однако мы должны передать форму через функцию **is_valid()**. Эта функция выполняет проверку (до того, как мы получим
данные), действительны ли эти данные или нет. Если данные недействительны, то мы не будем их извлекать. В противном
случае — мы получаем “очищенные” данные (**cleaned_data**). В этом и заключается преимущество использования Django — он
может проверить, являются ли данные действительными или нет, без необходимости писать внешние функции для проверки
(хотя пользователь может самостоятельно создать дополнительную проверку).

Вернёмся к формам Django.

Итак, мы создаём переменную **firstname** и устанавливаем её равной **form.cleaned_data.get(‘first_name’)**. Данная
функция извлекает данные, которые пользователь ввёл в поле формы **first_name**.

Затем мы создаём переменную **lastname** и устанавливаем её равной **form.cleaned_data.get(‘last_name’)**. Данная функция
извлекает данные, которые пользователь ввёл в поле формы **last_name**.

Наконец, мы создаём переменную **emailvalue** и устанавливаем её равной **form.cleaned_data.get(’email’)**. Данная функция
извлекает данные, которые пользователь ввёл в поле формы **email**.

Это все наши переменные. Затем мы передаём их в файл шаблона через контекстный словарь. Таким образом, мы можем вывести
результаты в файле шаблона.

Таким образом мы можем получить данные из формы Django. Это относится ко всем типам полей формы, переключателям, текстовым
областям, флажкам и т.д. Мы можем извлечь данные из этих полей формы с помощью функции **form.cleaned_data.get()**.

После чего мы можем обработать эти данные так, как мы хотим.

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Источник: [Python — How to Retrieve Data from a Django Form](http://www.learningaboutelectronics.com/Articles/How-to-retrieve-data-from-a-Django-form-Python.php)
