---
title: "Настройка безопасной передачи файлов по FTP при помощи криптографических протоколов SSL/TSL в RHEL 8"
date: 2019-07-06T00:00:00+03:00
categories: ["Linux", "Администрирование"]
draft: false
---

![](/posts/настройка-безопасной-передачи-файло/shapka14.jpg)

В предыдущей [статье](https://itdoxy.com/2019/07/06/как-установить-и-настроить-ftp-сервер-в-rhel-8/) детально описывались
установка и настройка FTP-сервера в RHEL 8 Linux. В данной же статье мы расскажем, как защитить FTP-сервер при помощи
криптографических протоколов SSL/TSL, чтобы включить службы шифрования данных для безопасной передачи файлов между системами.

Надеемся, что ваш FTP-сервер уже установлен и исправно функционирует. Если же нет, то предлагаем воспользоваться следующим
руководством:

- [Как установить и настроить FTP-сервер в RHEL 8](https://itdoxy.com/2019/07/06/как-установить-и-настроить-ftp-сервер-в-rhel-8/)

## Шаг 1. Генерация SSL/TLS сертификата и закрытого ключа

1. Создайте следующий каталог, в котором будут храниться файлы ключа и SSL/TLS сертификат:

```
# mkdir -p /etc/ssl/vsftpd
```

2. Затем, генерируем самозаверенный SSL/TLS сертификат и закрытый ключ, используя следующую команду:

```
# openssl req -x509 -nodes -keyout /etc/ssl/vsftpd/vsftpd.pem -out /etc/ssl/vsftpd/vsftpd.pem -days 365 -newkey rsa:2048
```

Ниже приведено описание каждого флага, используемого в вышеописанной команде:

- req — команда для управления запросом подписи сертификата X.509 (CSR);
- x509 — управление данными сертификата X.509;
- days — количество дней, в течение которых действителен сертификат;
- newkey — процессор ключей сертификата;
- rsa:2048 — процессор ключей RSA сгенерирует 2048-битный закрытый ключ;
- keyout — устанавливает файл, хранящий ключи;
- out — устанавливает файл, хранящий сертификаты, кстати, сертификат и ключ хранятся в одном файле: /etc/ssl/vsftpd/vsftpd.pem.

Приведенная выше команда предложит вам ответить на вопросы ниже, не забывайте использовать нужные вам значения.

```
Country Name (2 letter code) [XX]:IN
State or Province Name (full name) []:Lower Parel
Locality Name (eg, city) [Default City]:Mumbai
Organization Name (eg, company) [Default Company Ltd]:TecMint.com
Organizational Unit Name (eg, section) []:Linux and Open Source
Common Name (eg, your name or your server's hostname) []:tecmint
Email Address []:admin@tecmint.com
```

![Создание SSL сертификата для FTP-сервера](https://i.imgur.com/OPLnUUV.png)

## Шаг 2. Настройка VSFTPD для использования SSL/TLS

3. Откройте файл конфигурации VSFTPD:

```
# vi /etc/vsftpd/vsftpd.conf
```

Добавьте следующие параметры конфигурации, чтобы включить SSL, затем, в конце файла, выберите желаемую версию SSL и TLS.

```
ssl_enable=YES
ssl_tlsv1_2=YES
ssl_sslv2=NO
ssl_sslv3=NO
```

4. Следующим шагом добавим параметры rsa_cert_file и rsa_privatte_key_file, чтобы указать расположение сертификата SSL и
   файла ключа соответственно.

```
rsa_cert_file=/etc/ssl/vsftpd/vsftpd.pem
rsa_private_key_file=/etc/ssl/vsftpd/vsftpd.pem
```

5. Теперь добавим следующие параметры, позволяющие отключить анонимные подключения от использования SSL и принудительно
   устанавливать все неанонимные подключения через SSL.

```
allow_anon_ssl=NO               # disable anonymous users from using SSL
force_local_data_ssl=YES		# force all non-anonymous logins to use a secure SSL connection for data transfer
force_local_logins_ssl=YES		# force all non-anonymous logins  to send the password over SSL
```

6. Теперь отключим повторное использование соединений с данными SSL, а также установим шифры SSL как HIGH, чтобы разрешить
   зашифрованные соединения SSL. Для этого добавьте следующие параметры:

```
require_ssl_reuse=NO
ssl_ciphers=HIGH
```

7. Укажем диапазон пассивных портов (минимальный и максимальный порт), используемых vsftpd для защищённых соединений,
   используя параметры pasv_min_port и pasv_max_port соответственно. К тому же, вы можете дополнительно включить отладку
   SSL для устранения неполадок, используя параметр debug_ssl.

```
pasv_min_port=40000
pasv_max_port=50000
debug_ssl=YES
```

8. Напоследок, сохраните файл и перезапустите службу vsftpd, чтобы вышеуказанные изменения вступили в силу.

```
# systemctl restart vsftpd
```

9. Ещё одна критически важная задача, которую необходимо выполнить, прежде чем вы сможете безопасно получить доступ к
   FTP-серверу — это открыть порты 990 и 40000-50000 в фаерволе, что позволит службам vsftpd устанавливать TSL-соединение
   и откроет диапазон пассивных портов, заданных в файле конфигурации VSFTPD, соответственно, следующим образом:

```
# firewall-cmd --zone=public --permanent –add-port=990/tcp
# firewall-cmd --zone=public --permanent –add-port=40000-50000/tcp
# firewall-cmd --reload
```

## Шаг 3. Установка FileZilla для безопасного подключения к FTP-серверу

Для безопасного подключения к FTP-серверу необходим FTP-клиент, который поддерживает SSL/TLS подключения. Идеально
подойдёт FileZilla — широко используемый кроссплатформенный клиент FTP, SFTP и FTPS с открытым исходным кодом, который
по умолчанию поддерживает соединения SSL/TLS.

Установите FileZilla в Linux, используя ваш менеджер пакетов по-умолчанию, следующим образом:

```
$ sudo apt-get install filezilla   		#Debian/Ubuntu
# yum install epel-release filezilla	#On CentOS/RHEL
# dnf install filezilla			        #Fedora 22+
$ sudo zypper install filezilla			#openSUSE
```

11. Откройте клиент FileZilla. Для быстрого подключения к удалённому FTP-серверу, из основного интерфейса, — укажите
    IP-адрес хоста, имя пользователя и его пароль. После чего нажмите QuickConnect.
    
![Подключение к FTP-серверу с помощью Filezilla](https://i.imgur.com/d5kmyqV.png)

12. Теперь приложение попросит вашего разрешения на установление безопасного подключения, используя неизвестный
    самозаверенный сертификат. Нажмите ОК, чтобы продолжить.
    
![Принять SSL-сертификат для подключения FTP](https://i.imgur.com/vgiESGs.png)

Если сервер настроен правильно, то подключение будет успешно установлено, как показано на скриншоте ниже:

![Успешно подключен к FTP-серверу](https://i.imgur.com/KVYbYZI.png)

13. Проверьте состояние безопасного соединения FTP, путём загрузки файлов со своего компьютера на сервер, как показано
    на скриншоте ниже:
    
![Безопасная передача файлов через Filezilla](https://i.imgur.com/7asM5d3.png)

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Оригинальная статья: [Setup Secure FTP File Transfer Using SSL/TLS in RHEL 8](https://www.tecmint.com/setup-secure-ftp-file-transfer-using-ssl-tls-in-rhel-8/)
