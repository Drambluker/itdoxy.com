---
images:
  - /images/uploads/Django08.jpg
title: Сопоставление URL Django
slug: сопоставление-url-django
date: 2019-08-08T00:00:00+03:00
categories:
  - Программирование
  - Веб-разработка
  - Django
tags:
  - Курс молодого бойца Django
draft: false
---

![](/images/uploads/Django08.jpg)

Теперь, когда у нас есть рабочее представление, которое было объяснено в предыдущей главе, мы хотим получить к нему
доступ через URL. У Django есть свой собственный способ сопоставления URL-адресов, он осуществляется путем редактирования
файла urls.py (**myproject/urls.py**). Данный файл выглядит следующим образом:

```
from django.conf.urls import patterns, include, url
from django.contrib import admin
admin.autodiscover()

urlpatterns = patterns('',
    #Examples
    #url(r'^$', 'myproject.view.home', name = 'home'),
    #url(r'^blog/', include('blog.urls')),
    
    url(r'^admin', include(admin.site.urls)),
)
```

Когда пользователь запрашивает страницу вашего веб-приложения, контроллер Django начинает поиск соответствующего представления
в файле **urls.py**, а затем возвращает HTML-ответ или ошибку 404, если она не найдена. В **urls.py** самым важным является
кортеж **urlpatterns**. В нем вы определяете соответствие между URL-адресами и представлениями. Сопоставление представляет
собой кортеж URL-шаблонов, который выглядит следующим образом:

```
from django.conf.urls import patterns, include, url
from django.contrib import admin
admin.autodiscover()

urlpatterns = patterns('',
    #Examples
    #url(r'^$', 'myproject.view.home', name = 'home'),
    #url(r'^blog/', include('blog.urls')),
    
    url(r'^admin', include(admin.site.urls)),
    url(r'^hello/', 'myapp.views.hello', name = 'hello'),
)
```

Последняя строка сопоставляет URL “/hello” с представлением приветствия, созданным в файле **myapp/views.py**. Как вы можете
видеть выше, сопоставление URL состоит из трех частей:

- **Шаблон** — регулярное выражение, соответствующее URL-адресу, который вы хотите разрешить и сопоставить. Все, что может
работать с модулем Python “re” подходит в качестве шаблона (полезно, когда вы хотите передать параметры через URL).
- **Путь к представлению Python** — такой же, как при импорте модуля.
- **Имя** — необходимо для выполнения реверсирования URL-адреса.

Затем просто запустите сервер, чтобы получить доступ к вашему представлению по адресу: **http://127.0.0.1/hello**

## Организация ваших URL

До сих пор мы создавали URL-адреса в файле “myproject/urls.py”, однако, как уже говорилось ранее, лучше всего иметь
возможность повторного использования приложения в различных проектах. Вы можете легко увидеть, в чем проблема, если вы
сохраните все свои URL в файле “myproject/urls.py”. Поэтому рекомендуется создавать для каждого приложения свой собственный
“urls.py” и включать его в основной файл **urls.py** нашего проекта (ранее мы включили в него URL-адрес для интерфейса
администратора).

![](https://i.imgur.com/gA7FwHk.png)

## Как это сделать?

Нам нужно создать файл **urls.py** в **myapp**, используя следующий код:

```
from django.conf.urls import patterns, include, url

urlpatterns = patterns('', url(r'^hello/', 'myapp.views.hello', name = 'hello'),)
```

Тогда **myproject/urls.py** изменится и станет выглядеть следующим образом:

```
from django.conf.urls import patterns, include, url
from django.contrib import admin
admin.autodiscover()

urlpatterns = patterns('',
    #Examples
    #url(r'^$', 'myproject.view.home', name = 'home'),
    #url(r'^blog/', include('blog.urls')),
    
    url(r'^admin', include(admin.site.urls)),
    url(r'^myapp/', include('myapp.urls')),
)
```

В нем вы включили все URL из приложения **myapp**. Файл **hello.html**, к которому мы обращались через “/hello” теперь
будет “/myapp/hello”, что является лучшей и более понятной структурой для веб-приложения.

![](https://i.imgur.com/2l4nMDe.png)

Теперь давайте представим, что у нас есть другое представление в **myapp**, которое называется “morning”, и мы хотим
сопоставить его в **myapp/urls.py**, тогда мы изменим наш **myapp/urls.py**:

```
from django.conf.urls import patterns, include, url

urlpatterns = patterns('',
    url(r'^hello/', 'myapp.views.hello', name = 'hello'),
    url(r'^morning/', 'myapp.views.morning', name = 'morning'),
)
```

Также это может быть сделано следующим образом:

```
from django.conf.urls import patterns, include, url

urlpatterns = patterns('myapp.views',
    url(r'^hello/', 'hello', name = 'hello'),
    url(r'^morning/', 'morning', name = 'morning'),)
```

Как видите, теперь мы используем первый элемент нашего кортежа **urlpatterns**. Это может быть полезно, когда мы захотим
изменить имя приложения.

![](https://i.imgur.com/J0l3GcP.png)

## Отправка параметров в представления

Теперь мы знаем, как сопоставить URL, теперь давайте посмотрим, как отправлять параметры в представления. Классическим
примером является статья (вы хотите получить доступ к ней через “/article/article_id”).

Передача параметров осуществляется путем “захвата” их с помощью **регулярного выражения** в шаблоне URL. Если у нас есть
представление подобное следующему в “myapp/view.py”:

```
from django.shortcuts import render
from django.http import HttpResponse

def hello(request):
    return render(request, "hello.html", {})

def viewArticle(request, articleId):
    text = "Displaying article Number : %s"%articleId
    return HttpResponse(text)
```

И мы хотим сопоставить его с URL-адресом в **myapp/urls.py**, чтобы мы могли получить к нему доступ через “/myapp/article/articleID”,
то нам нужно следующее в “myapp/urls.py”:

```
from django.conf.urls import patterns, include, url

urlpatterns = patterns('myapp.views',
    url(r'^hello/', 'hello', name = 'hello'),
    url(r'^morning/', 'morning', name = 'morning'),
    url(r'^article/(\d+)/', 'viewArticle', name = 'article'),)
```

Когда Django увидит URL: “/myapp/article/42”, он передаст параметр “42” представлению **viewArticle**, и в итоге в вашем
браузере вы должны получить следующий результат:

![](https://i.imgur.com/tdh0zP6.png)

Обратите внимание, что порядок параметров здесь важен. Предположим, нам нужен список статей за месяц, давайте добавим
представление **viewArticles**. Файл **views.py** примет следующий вид:

```
from django.shortcuts import render
from django.http import HttpResponse

def hello(request):
    return render(request, "hello.html", {})

def viewArticle(request, articleId):
    text = "Displaying article Number : %s"%articleId
    return HttpResponse(text)

def viewArticle(request, month, year):
    text = "Displaying articles of : %s/%s"%(year, month)
    return HttpResponse(text)
```

Соответствующий файл **urls.py** будет выглядеть так:

```
from django.conf.urls import patterns, include, url

urlpatterns = patterns('myapp.views',
    url(r'^hello/', 'hello', name = 'hello'),
    url(r'^morning/', 'morning', name = 'morning'),
    url(r'^article/(\d+)/', 'viewArticle', name = 'article'),
    url(r'^articles/(\d{2})/(\d{4})', 'viewArticles', name = 'articles'),)
```

Теперь, когда вы перейдете по “/myapp/Articles/12/2006”, вы получите “Displaying articles of: 2006/12”, но если вы измените
параметры, вы не получите тот же результат.

![](https://i.imgur.com/AFpQbDo.png)

Чтобы избежать этого, можно связать параметр URL с параметром представления. Для этого файл **urls.py** должен принять
следующий вид:

```
from django.conf.urls import patterns, include, url

urlpatterns = patterns('myapp.views',
    url(r'^hello/', 'hello', name = 'hello'),
    url(r'^morning/', 'morning', name = 'morning'),
    url(r'^article/(\d+)/', 'viewArticle', name = 'article'),
    url(r'^articles/(?P\d{2})/(?P\d{4})', 'viewArticles', name = 'articles'),)
```

Источник: [Django — URL Mapping](https://www.tutorialspoint.com/django/django_url_mapping.htm)
