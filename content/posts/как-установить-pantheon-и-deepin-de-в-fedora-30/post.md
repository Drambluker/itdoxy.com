---
title: "Как установить Pantheon и Deepin DE в Fedora 30"
date: 2019-05-02T00:00:00+03:00
categories: ["Linux"]
draft: false
---

![](/posts/как-установить-pantheon-и-deepin-de-в-fedora-30/shapka10.jpg)

**_Fedora 30 Workstation_** поддерживает среды рабочего стола **_Deepin_** и **_Pantheon_**. Однако, нет официальных
сборок Fedora для этих сред рабочего стола. Эта статья покажет как установить и активировать Deepin и Pantheon на вашей
Fedora 30 Workstation.

Не так давно вышла **_Fedora 30_** с поддержкой **_Deepin_** и **_Pantheon_**. В этой статье мы покажем вам, как включить каждую из этих
сред рабочего стола без каких-либо проблем.

Fedora, по умолчанию, поставляется с рабочей средой GNOME и несколькими другими сборками Fedora, которые приходят
упакованными с другими средами рабочего стола, включая KDE, Cinnamon, Xfce и многими другими. Обратите внимание, что
Deepin и Pantheon недоступны в качестве альтернативной сборки Fedora, но они могут быть установлены с помощью DNF.

Давайте начнём с активации Pantheon.

## Установка Pantheon в Fedora 30 workstation

_Шаг 1)_ Из раздела “Activities” зайдите в терминал.

![Запуск терминала](https://i.imgur.com/jvLleF8.png)

_Шаг 2)_ Введите следующую команду и нажмите _Enter_ для установки среды рабочего стола Pantheon.

```
sudo dnf group install 'pantheon desktop'
```

![Установка Pantheon](https://i.imgur.com/U5uPjYk.png)

_Шаг 3)_ Обратите внимание на прогресс в терминале. Вам следует нажать **_‘y’_**, когда будет предложено завершить установку.

![Процесс установки](https://i.imgur.com/isF2Uwd.png)

_Шаг 4)_ После завершения установки закройте терминал и выйдите из системы.

_Шаг 5)_ Вы должны увидеть меню входа в систему. Нажмите на значок шестеренки для того, чтобы увидеть доступные окружения
рабочего стола. Поскольку мы установили Pantheon, вы должны увидеть его в списке вместе с GNOME, GNOME Classic и GNOME
on Xorg.

![Экран входа в Fedora вместе с добавленным окружением Pantheon](https://i.imgur.com/BhoXetl.png)

_Шаг 6)_ Выберите **_“Pantheon”_** и введите пароль для входа в ваш аккаунт в Fedora, однако, с совершенно новой средой
рабочего стола Pantheon.

Наслаждайтесь elementary OS на вашей **_Fedora 30 Workstation_**.

![Установленный Pantheon в Fedora 30](https://i.imgur.com/WnRJDPX.png)

## Установка Deepin в Fedora 30 workstation

_Шаг 1)_ Из раздела **_“Activities”_** зайдите в терминал.

![Запуск терминала](https://i.imgur.com/0aoEYg3.png)

_Шаг 2)_ Введите следующую команду и нажмите _Enter_ для того, чтобы установить среду рабочего стола Deepin.

```
sudo dnf group install deepin-desktop
```

![Установка Deepin в Fedora 30](https://i.imgur.com/C7lliyx.png)

_Шаг 3)_ Вам следует ввести **_‘y’_**, когда будет предложено завершить установку.

_Шаг 4)_ После того, как установка завершилась, закройте терминал и выйдите из системы.

_Шаг 5)_ Вы увидите меню входа, а также, Deepin в списке вместе с GNOME, GNOME Classic и GNOME on Xorg. Нажмите на
значок шестерёнки и выберите **_“Deepin”_**, после чего войдите в систему.

![Deepin вынесен в меню входа](https://i.imgur.com/C1lmPfS.jpg)

_Шаг 6)_ Теперь ваша Fedora должна войти на рабочий стол Deepin!

![Установленный Deepin в Fedora 30](https://i.imgur.com/cfbMlaA.jpg)

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Оригинальная статья: [How to install Pantheon and Deepin DE on Fedora 30 Workstation](https://www.fosslinux.com/13962/how-to-install-pantheon-and-deepin-de-on-fedora-30-workstation.htm)
