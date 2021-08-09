---
title: "Модели Django"
slug: "модели-django"
date: 2019-08-14T00:00:00+03:00
categories: ["Программирование", "Веб-разработка", "Django"]
tags: ["Курс молодого бойца Django"]
draft: false
---

![](/posts/модели-django/Django10.jpg)

Модель — это класс, который представляет собой таблицу или коллекцию в нашей базе данных, где каждый атрибут класса
является полем таблицы или коллекции. Модели определены в **app/models.py** (в нашем примере: **myapp/models.py**).

## Создание модели

Ниже приведена модель **Dreamreal**, созданная в качестве примера:

```
from django.db import models

class Dreamreal(models.Model):

    website = models.CharField(max_length = 50)
    mail = models.CharField(max_length = 50)
    name = models.CharField(max_length = 50)
    phonenumber = models.IntegerField()
    
    class Meta:
    db_table = "dreamreal"
```

Каждая модель наследуется от **django.db.models.Model**.

Наш класс имеет 4 атрибута (3 CharField и 1 Integer), это будут поля таблицы.

Мета-класс с атрибутом **db_table** позволяет нам определить фактическое имя таблицы или коллекции. Django называет
таблицу или коллекцию автоматически: **myapp_modelName**. Этот класс позволит вам присвоить своё собственное имя таблице.

В **django.db.models** есть и другие типы полей, о которых вы можете узнать больше, перейдя по [ссылке](https://docs.djangoproject.com/en/2.2/ref/models/fields/#field-types).

После создания вашей модели вам потребуется Django для создания фактической базы данных:

```
$python manage.py syncdb
```

## Манипулирование данными (CRUD)

Давайте создадим представление “crudops”, чтобы продемонстрировать, как мы можем манипулировать данными с помощью моделей.
Файл **myapp/views.py** будет выглядеть следующим образом:

```
from myapp.models import Dreamreal
from django.http import HttpResponse

def crudops(request):
    #Создание записи
    
    dreamreal = Dreamreal(
    website = "www.polo.com", mail = "sorex@polo.com",
    name = "sorex", phonenumber = "002376970"
    )
    
    dreamreal.save()
    
    #Чтение ВСЕХ записей
    objects = Dreamreal.objects.all()
    res ='Printing all Dreamreal entries in the DB : <br>'
    
    for elt in objects:
    res += elt.name+"<br>"
    
    #Чтение конкретной записи:
    sorex = Dreamreal.objects.get(name = "sorex")
    res += 'Printing One entry <br>'
    res += sorex.name
    
    #Удаление записи
    res += '<br> Deleting an entry <br>'
    sorex.delete()
    
    #Обновление
    dreamreal = Dreamreal(
    website = "www.polo.com", mail = "sorex@polo.com",
    name = "sorex", phonenumber = "002376970"
    )
    
    dreamreal.save()
    res += 'Updating entry<br>'
    
    dreamreal = Dreamreal.objects.get(name = 'sorex')
    dreamreal.name = 'thierry'
    dreamreal.save()
    
    return HttpResponse(res)
```

## Другие манипуляции над данными

Давайте рассмотрим другие манипуляции, которые мы можем сделать над данными с помощью моделей. Обратите внимание,
что предыдущие операции выполнялись над экземплярами нашей модели, теперь мы будем работать непосредственно с классом,
представляющим нашу модель.

Давайте создадим представление “datamanipulation” в **myapp/views.py**

```
from myapp.models import Dreamreal
from django.http import HttpResponse

def datamanipulation(request):
    res = ''
    
    #Фильтрация данных:
    qs = Dreamreal.objects.filter(name = "paul")
    res += "Found : %s results<br>"%len(qs)
    
    #Сортировка результатов
    qs = Dreamreal.objects.order_by("name")
    
    for elt in qs:
    res += elt.name + '<br>'
    
    return HttpResponse(res)
```

## Связи между моделями

Django ORM предлагает 3 способа связи между моделями.

Одним из первых способов, с которым мы ознакомимся, является отношение «один ко многим». Как видно из приведенного выше
примера, компания **Dreamreal** может иметь несколько онлайн-сайтов. Определение этого отношения осуществляется с помощью
**django.db.models.ForeignKey**:

```
from django.db import models

class Dreamreal(models.Model):
    website = models.CharField(max_length = 50)
    mail = models.CharField(max_length = 50)
    name = models.CharField(max_length = 50)
    phonenumber = models.IntegerField()
    online = models.ForeignKey('Online', default = 1)
    
    class Meta:
    db_table = "dreamreal"

class Online(models.Model):
    domain = models.CharField(max_length = 30)
    
    class Meta:
    db_table = "online"
```

Как вы можете видеть в нашем обновленном файле **myapp/models.py**, мы добавили модель **Online** и связали ее с нашей
моделью **Dreamreal**.

Давайте проверим, как все это работает через оболочку **manage.py**.

Сначала давайте создадим несколько компаний (записей **Dreamreal**) для тестирования в нашей оболочке Django:

```
$python manage.py shell

>>> from myapp.models import Dreamreal, Online
>>> dr1 = Dreamreal()
>>> dr1.website = 'company1.com'
>>> dr1.name = 'company1'
>>> dr1.mail = 'contact@company1'
>>> dr1.phonenumber = '12345'
>>> dr1.save()
>>> dr2 = Dreamreal()
>>> dr1.website = 'company2.com'
>>> dr2.website = 'company2.com'
>>> dr2.name = 'company2'
>>> dr2.mail = 'contact@company2'
>>> dr2.phonenumber = '56789'
>>> dr2.save()
```

Теперь создадим некоторые хостинговые домены:

```
>>> on1 = Online()
>>> on1.company = dr1
>>> on1.domain = "site1.com"
>>> on2 = Online()
>>> on2.company = dr1
>>> on2.domain = "site2.com"
>>> on3 = Online()
>>> on3.domain = "site3.com"
>>> dr2 = Dreamreal.objects.all()[2]
>>> on3.company = dr2
>>> on1.save()
>>> on2.save()
>>> on3.save()
```

Получить доступ атрибуту хостинговой компании (записи **Dreamreal**) из онлайн-домена очень просто:

```
>>> on1.company.name
```

Если мы хотим узнать все онлайн-домены, размещенные компанией в **Dreamreal**, воспользуемся кодом:

```
>>> dr1.online_set.all()
```

Обратите внимание, что все методы, которые мы видели ранее (**filter**, **all**, **exclude**, **order_by** ...),
используются, чтобы получить **QuerySet**.

Вы также можете получить доступ к атрибутам связанной модели в операциях фильтрации, например, вы хотите получить все
онлайн-домены, в которых имя **Dreamreal** содержит “company”:

```
>>> Online.objects.filter(company__name__contains = 'company'
```

**Примечание.** Этот тип запроса поддерживается только в базах данных SQL. Это не будет работать с нереляционными БД,
где нет объединений и есть два «_».

Но это не единственный способ связать модели, у вас также может быть **OneToOneField**, ссылка, которая гарантирует,
что связь между двумя объектами уникальна. Если бы мы использовали **OneToOneField** в нашем примере выше, это означало бы,
что для каждой записи **Dreamreal** возможна только одна запись **Online** и наоборот.

И последнее, **ManyToManyField** для отношений типа (n-n) между таблицами. Обратите внимание, что они актуальны для баз
данных на основе SQL.

Источник: [Django — Models](https://www.tutorialspoint.com/django/django_models.htm)
