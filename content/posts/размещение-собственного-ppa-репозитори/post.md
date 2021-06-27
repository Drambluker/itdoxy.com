---
title: "Размещение собственного PPA репозитория на GitHub"
date: 2019-07-02T00:00:00+03:00
categories: ["Linux"]
draft: false
---

![](/posts/размещение-собственного-ppa-репозитори/shapka12.jpg)

Опубликовать свои собственные Debian пакеты и разместить их в репозитории на GitHub довольно просто. Данная статья
является кратким руководством, как это сделать.

## PPA репозиторий может быть представлен просто как одна директория

```
.
└── my_ppa
    ├── my_list_file.list
    ├── InRelease
    ├── KEY.gpg
    ├── Packages
    ├── Packages.gz
    ├── Release
    ├── Release.gpg
    ├── package-a_0.0.1_amd64.deb
    ├── package-a_0.0.2_amd64.deb
    ├── package-b_0.1.0_amd64.deb
    ├── package-b_0.1.1_amd64.deb
    ├── ...
    └── package-z_1.0.0_amd64.deb
```

Рабочий пример можно найти по [адресу](https://github.com/assafmo/ppa).

Вы можете назвать _my_ppa_ и _my_list_file.list_ как угодно. Я использовал эти имена лишь для примера.

Также не забудьте заменить _${GITHUB_USERNAME}_ на своё имя пользователя GitHub и _${EMAIL}_ на свой адрес электронной почты.

## 0. Создание GitHub репозитория с вашими deb-пакетами

[Создайте GitHub репозиторий](https://github.com/new). Мы назовём его my_ppa. Затем перейдите на _https://github.com/${GITHUB_USERNAME}/my_ppa/settings_
и под _GitHub Pages_ выберите _Source_, который должен быть _master branch_.

На самом деле, подойдет любой HTTP-сервер, но страницы на GitHub выгоднее, они бесплатны, легки и быстры.

Теперь клонируйте репозиторий и поместите все ваши Debian-пакеты внутрь.

```
git clone "git@github.com:${GITHUB_USERNAME}/my_ppa.git"
cd my_ppa
cp /path/to/my/package-a_0.0.1_amd64.deb .
```

## 1. Создание GPG-ключа

Установите _gpg_ и создайте новый ключ:

```
sudo apt install gnupg
gpg --full-gen-key
```

Используйте RSA:

```
Please select what kind of key you want:
    (1) RSA and RSA (default)
    (2) DSA and Elgamal
    (3) DSA (sign only)
    (4) RSA (sign only)
Your selection? 1
```

RSA с 4096 битами:

```
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
```

Ключ должен быть действителен всегда:

```
Please specify how long the key should be valid.
0 = key does not expire
 = key expires in n days
w = key expires in n weeks
m = key expires in n months
y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y
```

Введите ваше имя и адрес электронной почты:

```
Real Name: My Name
Email address: ${EMAIL}
Comment:
You selected this USER-ID:
"My Name <my.name@email.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
```

Теперь _gpg_ начнет создавать ваш ключ и запросит пароль для дополнительной защиты. Я предпочитаю оставлять его пустым
поэтому когда я подписываю что-либо своим ключом, он не запрашивает каждый раз пароль.

```
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key B58FBB4C23247554 marked as ultimately trusted
gpg: directory '/home/assafmo/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/assafmo/.gnupg/openpgp-revocs.d/31EE74534094184D9964EF82B58FBB4C23247554.rev'
public and secret key created and signed.

pub rsa4096 2019-05-01 [SC]
31EE74534094184D9964EF82B58FBB4C23247554
uid My Name <my.name@email.com>
sub rsa4096 2019-05-01 [E]
```

Вы можете сделать резервную копию своего закрытого ключа, используя:

```
gpg --export-secret-keys "${EMAIL}" > my-private-key.asc
```

И импортируйте его, используя:

```
gpg --import my-private-key.asc
```

## 2. Создание файла _KEY.gpg_

Создайте файл открытого ASCII ключа _KEY.gpg_ внутри git-репозитория _my_ppa_:

```
gpg --armor --export "${EMAIL}" > /path/to/my_ppa/KEY.gpg
```

_Примечание:_ На закрытый ключ ссылается адрес электронной почты, который вы указали на предыдущем шаге.

## 3. Создание файлов _Packages_ и _Packages.gz_

Внутри git-репозитория _my_ppa_:

```
dpkg-scanpackages --multiversion . > Packages
gzip -k -f Packages
```

## 4. Создание файлов _Release_, _Release.gpg_ и _InRelease_

Внутри git-репозитория _my_ppa_:

```
apt-ftparchive release . > Release
gpg --default-key "${EMAIL}" -abs -o - Release > Release.gpg
gpg --default-key "${EMAIL}" --clearsign -o - Release > InRelease
```

## 5. Создание файла _my_list_file.list_

Внутри git-репозитория _my_ppa_:

```
echo "deb https://${GITHUB_USERNAME}.github.io/my_ppa ./" > my_list_file.list
```

Этот файл будет установлен позже в пользовательской директории _/etc/apt/sources.list.d/_. Он говорит менеджеру пакетов
apt искать обновления вашего PPA по адресу _https://${GITHUB_USERNAME}.github.io/my_ppa_.

## Вот и всё!

Закомитте и отправьте изменения на GitHub и ваш PPA готов к работе:

```
git add -A
git commit -m "my ppa repo is now hosted on github"
git push -u origin master
```

Теперь вы можете попросить всех своих друзей и пользователей установить ваш PPA следующим образом:

```
curl -s --compressed "https://${GITHUB_USERNAME}.github.io/my_ppa/KEY.gpg" | sudo apt-key add -
sudo curl -s --compressed -o /etc/apt/sources.list.d/my_list_file.list "https://${GITHUB_USERNAME}.github.io/my_ppa/my_list_file.list"
sudo apt update
```

Затем они смогут установить ваши пакеты:

```
sudo apt install package-a package-b package-z
```

Всякий раз, когда вы будете публиковать новую версию существующего пакета, ваши пользователи получат её, как и любое другое обновление.

## Как добавить новые пакеты

Просто поместите ваши новые deb-файлы в git-репозиторий _my_ppa_ и выполните:

```
# Packages & Packages.gz
dpkg-scanpackages --multiversion . > Packages
gzip -k -f Packages

# Release, Release.gpg & InRelease
apt-ftparchive release . > Release
gpg --default-key "${EMAIL}" -abs -o - Release > Release.gpg
gpg --default-key "${EMAIL}" --clearsign -o - Release > InRelease

# Commit & push
git add -A
git commit -m update
git push
```

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Оригинальная статья: [Hosting your own PPA repository on GitHub](https://assafmo.github.io/2019/05/02/ppa-repo-hosted-on-github.html)
