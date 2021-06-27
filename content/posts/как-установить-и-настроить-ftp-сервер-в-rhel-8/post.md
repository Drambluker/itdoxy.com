---
title: "Как установить и настроить FTP-сервер в RHEL 8"
slug: "как-установить-и-настроить-ftp-сервер-в-rhel-8"
date: 2019-07-06T00:00:00+03:00
categories: ["Linux", "Администрирование"]
draft: false
---

![](/posts/как-установить-и-настроить-ftp-сервер-в-rhel-8/shapka13.jpg)

**_FTP_** (_File Transfer Protocol_ — **_протокол передачи файлов_**) — это стандартный и проверенный временем сетевой
протокол, используемый для передачи файлов между клиентом и сервером в компьютерной сети. Основан на технологии
“клиент-сервер”, что позволяет получать доступ к файлам и каталогам при помощи FTP-клиента, загружать файлы на сервер,
а также скачивать их оттуда.

В данной статье будут описаны процессы установки и настройки FTP-сервера в RHEL 8 для базового обмена файлами между компьютерами.

## Установка FTP-сервера в RHEL 8

1. Чтобы установить защищённый пакет FTP, используйте следующую команду dnf:

```
#dnf install vsftpd
```

![Установка VsFTP в RHEL 8](https://i.imgur.com/qxwzCNX.png)

2. После завершения установки, необходимо запустить службу vsftpd, а также разрешить автоматический запуск службы во
   время загрузки системы и проверить состояние с помощью следующих команд systemctl:

```
# systemctl start vsftpd
# systemctl enable vsftpd
# systemctl status vsftpd
```

![Запуск и проверка службы FTP](https://i.imgur.com/0EXPBVD.png)

3. Следующим шагом следует открыть порт 21 в фаерволе, чтобы открыть доступ службам FTP для систем извне.

```
# firewall-cmd --zone=public --permanent --add-port=21/tcp
# firewall-cmd --zone=public --permanent --add-port=45073/tcp
# firewall-cmd --reload
```

## Настройка FTP-сервер в RHEL 8

4. Чтобы настроить FTP-сервер, необходимо сделать бэкап основного файла конфигурации FTP **_/etc/vsftpd/vsftpd.conf_**,
   используя следующую команду копирования:

```
# cp /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.orig
```

5. Затем откройте файл конфигурации:

```
# vi /etc/vsftpd/vsftpd.conf
```

Установите следующие параметры с соответствующими значениями (см. man vsftpd.conf для значений параметров конфигурации):

```
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=NO
listen_ipv6=YES
pam_service_name=vsftpd
```

6. Теперь необходимо настроить FTP для того, чтобы разрешать/запрещать пользователям доступ к службам FTP на основе
   файла списка пользователей **_/etc/vsftpd.userlist_**.

Изначально, пользователям, описанным в файле **_/etc/vsftpd.userlist_**, запрещён доступ параметром userlist_deny равным
**_YES_**, если **_userlist_enable = YES_**, то он разрешает доступ.

Однако, изменение параметра **_userlist_deny=NO_** изменяет настройку, а это означает, что доступ будет предоставлен
только пользователям, указанным в **_userlist_file=/etc/vsftpd.userlist_**.

Поэтому добавьте следующие строки в файл конфигурации **_vsftpd.conf_** (если же строки уже написаны, то раскомментируйте
их и установите их значения, как показано далее):

```
userlist_enable=YES                   # allow access to list of usernames from the userlist_file
userlist_file=/etc/vsftpd.userlist    # stores usernames. userlist_deny=NO
```

Сохраните внесённые изменения и закройте файл.

7. Теперь допишите следующие строки в файл конфигурации **_vsftpd.conf_**, чтобы ограничить пользователей FTP их
   домашним каталогом.

```
chroot_local_user=YES #means local users will be placed in a chroot jail, their home directory after login by default settings. user_sub_token=$USER
local_root=/home/$USER/ftp
```

Сохраните внесённые изменения и закройте файл.

8. Чтобы разрешить FTP чтение файлов и запись в файлы домашнего каталога пользователя, необходимо установить следующее
   логическое правило SELinux:

```
# semanage boolean -m ftpd_full_access --on
```

9. Наконец, чтобы все вышеописанные изменения вступили в силу, перезапустите службу **_vsftpd_** следующим образом:

```
# systemctl restart vsftpd
```

## Тестирование FTP-сервера в RHEL 8

10. Проверим корректно ли работают настройки FTP. Первым делом, создайте FTP-пользователя при помощи команды useradd,
    а также создайте пароль для этого пользователя:

```
# useradd -m -c "Tecmint HowTos" -s /bin/bash tecmint
# passwd tecmint
```

11. Затем, занесите пользователя **_tecmint_** в **_/etc/vsftpd.userlist_**, используя следующую ECHO команду:

```
# echo "tecmint" | tee -a /etc/vsftpd.userlist
# cat /etc/vsftpd.userlist
```

12. После чего создайте альтернативный локальный корневой каталог для пользователя и установите соответствующие
    разрешения для этого каталога:

```
# mkdir -p /home/tecmint/ftp
# chown nobody:nobody /home/tecmint/ftp
# chmod a-w /home/tecmint/ftp
```

13. Создайте каталог внутри локального корневого каталога, в котором пользователь будет хранить свои файлы:

```
# mkdir /home/tecmint/ftp/files
# chown tecmint:tecmint /home/tecmint/ftp/files
# chmod 0700 /home/tecmint/ftp/files/
```

14. И, наконец, присоединитесь к FTP-серверу при помощи одного из FTP-пользователей:

```
# ftp tecmint@192.168.56.100
```

### Пример вывода:

```
Connected to 192.168.56.100
220 Welcome to TecMint.com FTP service.
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
```

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Оригинальная статья: [How to Install, Configure and Secure FTP Server in RHEL 8](https://www.tecmint.com/install-ftp-server-in-rhel-8/)
