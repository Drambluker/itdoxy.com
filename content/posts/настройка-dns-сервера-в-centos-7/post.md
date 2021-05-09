---
title: "Настройка DNS-сервера в CentOS 7"
date: 2019-04-05T00:00:00+03:00
categories: ["Администрирование", "Linux"]
draft: false
---

![](/posts/настройка-dns-сервера-в-centos-7/shapka2-2.jpg)

**DNS** (**_Domain Name System_** — система доменных имён) — используется для получения IP-адреса по имени хоста или по
URL. Например, если мы введём `www.unixmen.com` в браузере, DNS-сервер преобразует доменное имя в связанный с ним IP-адрес.
Поскольку IP-адрес запомнить трудно, DNS-серверы используются для перевода имён хостов, таких как `www.unixmen.com`, в
`173.xxx.xx.xx`. Таким образом, доменное имя запомнить намного легче, чем IP-адрес.

Данное руководство поможет вам настроить локальный DNS-сервер на вашей системе CentOS 7 (также применимо для RHEL и
Scientific Linux 7).

## Установка DNS-сервера

### План действий

В этом уроке мы будем использовать три узла. Один будет действовать как _основной_ DNS-сервер, другой как _дополнительный_,
а последний будет нашим DNS-клиентом. Все они приведены ниже.

### Основной (главный) DNS-сервер:

```
OperatingSystem : CentOS 7 minimal server
Hostname : masterdns.unixmen.local
IP Address : 192.168.1.101/24
```

### Дополнительный (вторичный) DNS-сервер:

```
OperatingSystem : CentOS 7 minimal server
Hostname : masterdns.unixmen.local
IP Address : 192.168.1.102/24
```

### Клиент:

```
OperatingSystem : CentOS 7 minimal server
Hostname : masterdns.unixmen.local
IP Address : 192.168.1.103/24
```

## Настройка основного (главного) DNS-сервера

Установите на ваш сервер пакеты _bind9_.

```
yum install bind bind-utils -y
```

### 1. Настройка DNS-сервера

Отредактируйте файл _**‘/etc/named.conf’**_.

```
vi /etc/named.conf
```

Теперь добавьте следующие строчки:

```
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
listen-on port 53 { 127.0.0.1; 192.168.1.101;}; ### Master DNS IP ###
#    listen-on-v6 port 53 { ::1; };
    directory     "/var/named";
    dump-file     "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    allow-query  { localhost; 192.168.1.0/24;}; ### IP Range ###
    allow-transfer{ localhost; 192.168.1.102; };   ### Slave DNS IP ###

    /* 
     - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
     - If you are building a RECURSIVE (caching) DNS server, you need to enable 
    recursion. 
     - If your recursive DNS server has a public IP address, you MUST enable access 
    control to limit queries to your legitimate users. Failing to do so will
    cause your server to become part of large scale DNS amplification 
    attacks. Implementing BCP38 within your network would greatly
    reduce such attack surface 
    */
    recursion yes;

    dnssec-enable yes;
    dnssec-validation yes;
    dnssec-lookaside auto;

    /* Path to ISC DLV key */
    bindkeys-file "/etc/named.iscdlv.key";

    managed-keys-directory "/var/named/dynamic";

    pid-file "/run/named/named.pid";
    session-keyfile "/run/named/session.key";
};

logging {
channel default_debug {
file "data/named.run";
severity dynamic;
};
};

zone "." IN {
type hint;
file "named.ca";
};

zone "unixmen.local" IN {
type master;
file "forward.unixmen";
allow-update { none; };
};
zone "1.168.192.in-addr.arpa" IN {
type master;
file "reverse.unixmen";
allow-update { none; };
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

### 2. Создание файлов зон

Создайте файлы прямой и обратной зоны, которые мы упоминали в файле **_‘/etc/named.conf’_**.

#### 2.1 Создание прямой зоны

В директории **_/var/named_** создайте файл **forward.unixmen**.

```
vi /var/named/forward.unixmen
```

Допишите следующие строчки:

```
$TTL 86400
@   IN  SOA     masterdns.unixmen.local. root.unixmen.local. (
2011071001  ;Serial
3600        ;Refresh
1800        ;Retry
604800      ;Expire
86400       ;Minimum TTL
)
@       IN  NS          masterdns.unixmen.local.
@       IN  NS          secondarydns.unixmen.local.
@       IN  A           192.168.1.101
@       IN  A           192.168.1.102
@       IN  A           192.168.1.103
masterdns       IN  A   192.168.1.101
secondarydns    IN  A   192.168.1.102
client          IN  A   192.168.1.103
```

На этом создание прямой зоны завершено, переходим далее.

#### 2.2 Создание обратной зоны

В директории **_/var/named_** создайте файл **reverse.unixmen**.

```
vi /var/named/reverse.unixmen
```

Добавьте следующие строчки:

```
$TTL 86400
@   IN  SOA     masterdns.unixmen.local. root.unixmen.local. (
2011071001  ;Serial
3600        ;Refresh
1800        ;Retry
604800      ;Expire
86400       ;Minimum TTL
)
@       IN  NS          masterdns.unixmen.local.
@       IN  NS          secondarydns.unixmen.local.
@       IN  PTR         unixmen.local.
masterdns       IN  A   192.168.1.101
secondarydns    IN  A   192.168.1.102
client          IN  A   192.168.1.103
101     IN  PTR         masterdns.unixmen.local.
102     IN  PTR         secondarydns.unixmen.local.
103     IN  PTR         client.unixmen.local.
```

### 3. Запуск DNS-сервера

Включите и запустите службу DNS:

```
systemctl enable named
systemctl start named
```

### 4. Настройка файрвола

Нужно при помощи файрвола разрешить DNS-серверу использование порта 53.

```
firewall-cmd --permanent --add-port=53/tcp
firewall-cmd --permanent --add-port=53/udp
```

### 5. Перезапуск файрвола

```
firewall-cmd --reload
```

### 6. Настройка разрешений, прав доступа и SELinux

Пропишите следующие команды друг за другом:

```
chgrp named -R /var/named
chown -v root:named /etc/named.conf
restorecon -rv /var/named
restorecon /etc/named.conf
```

### 7. Проверка на ошибки

Теперь нужно убедиться в корректности выполнения проделанных нами пунктов.

Проверьте стандартный файл конфигурации DNS:

```
named-checkconf /etc/named.conf
```

Если ничего не возвращается, ваш файл конфигурации настроен корректно.

Далее, проверьте прямую зону:

```
named-checkzone unixmen.local /var/named/forward.unixmen
```

Образец вывода:

```
zone unixmen.local/IN: loaded serial 2011071001
OK
```

Затем, проверьте обратную зону:

```
named-checkzone unixmen.local /var/named/reverse.unixmen
```

Образец вывода:

```
zone unixmen.local/IN: loaded serial 2011071001
OK
```

Добавьте данные DNS-сервера в файл конфигурации сетевого интерфейса.

```
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

```
TYPE="Ethernet"
BOOTPROTO="none"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
NAME="enp0s3"
UUID="5d0428b3-6af2-4f6b-9fe3-4250cd839efa"
ONBOOT="yes"
HWADDR="08:00:27:19:68:73"
IPADDR0="192.168.1.101"
PREFIX0="24"
GATEWAY0="192.168.1.1"
DNS="192.168.1.101"
IPV6_PEERDNS="yes"
IPV6_PEERROUTES="yes"
```

Отредактируйте файл **_‘/etc/resolv.conf’_**.

```
vi /etc/resolv.conf
```

Добавьте имя сервера и IP-адрес:

```
nameserver      192.168.1.101
```

Сохраните и закройте файл.

Перезагрузите сетевую службу:

```
systemctl restart network
```

### 8. Тестирование основного DNS-сервера

Введите:

```
dig masterdns.unixmen.local
```

Образец вывода:

```
; <<>> DiG 9.9.4-RedHat-9.9.4-14.el7 <<>> masterdns.unixmen.local
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 25179
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;masterdns.unixmen.local.    IN    A

;; ANSWER SECTION:
masterdns.unixmen.local. 86400    IN    A    192.168.1.101

;; AUTHORITY SECTION:
unixmen.local.     86400    IN    NS    secondarydns.unixmen.local.
unixmen.local.     86400    IN    NS    masterdns.unixmen.local.

;; ADDITIONAL SECTION:
secondarydns.unixmen.local. 86400 IN    A    192.168.1.102

;; Query time: 0 msec
;; SERVER: 192.168.1.101#53(192.168.1.101)
;; WHEN: Wed Aug 20 16:20:46 IST 2014
;; MSG SIZE  rcvd: 125
```

Введите:

```
nslookup unixmen.local
```

Образец вывода:

```
Server:     192.168.1.101
Address:    192.168.1.101#53

Name:    unixmen.local
Address: 192.168.1.103
Name:    unixmen.local
Address: 192.168.1.101
Name:    unixmen.local
Address: 192.168.1.102
```

Теперь основной DNS-сервер готов к использованию.

Настало время настройки дополнительного DNS-сервера.

## Установка дополнительного DNS-сервера

Установите пакеты _bind_ при помощи следующей команды:

```
yum install bind bind-utils -y
```

### 1. Настройка вторичного DNS-сервера

Отредактируйте файл **_/etc/named.conf_**:

```
vi /etc/named.conf
```

Добавьте следующие строчки:

```
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//
options {
listen-on port 53 { 127.0.0.1; 192.168.1.102; };
listen-on-v6 port 53 { ::1; };
directory "/var/named";
dump-file "/var/named/data/cache_dump.db";
statistics-file "/var/named/data/named_stats.txt";
memstatistics-file "/var/named/data/named_mem_stats.txt";
allow-query     { localhost; 192.168.1.0/24; };
.
.
.
.
zone "." IN {
type hint;
file "named.ca";
};
zone "unixmen.local" IN {
type slave;
file "slaves/unixmen.fwd";
masters { 192.168.1.101; };
};
zone "1.168.192.in-addr.arpa" IN {
type slave;
file "slaves/unixmen.rev";
masters { 192.168.1.101; };
};
include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

### 2. Запуск DNS-службы

Введите:

```
systemctl enable named
systemctl start named
```

Теперь прямая и обратная зоны автоматически копируются с главного DNS-сервера на дополнительный DNS-сервер
(расположенный в директории: **_‘/var/named/slaves/’_**).

```
ls /var/named/slaves/
```

Образец вывода:

```
unixmen.fwd  unixmen.rev
```

### 3. Заполнение данных DNS-сервера

Добавьте данные DNS-сервера в файл конфигурации сетевого интерфейса.

```
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

```
TYPE="Ethernet"
BOOTPROTO="none"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
NAME="enp0s3"
UUID="5d0428b3-6af2-4f6b-9fe3-4250cd839efa"
ONBOOT="yes"
HWADDR="08:00:27:19:68:73"
IPADDR0="192.168.1.102"
PREFIX0="24"
GATEWAY0="192.168.1.1"
DNS1="192.168.1.101"
DNS2="192.168.1.102"
IPV6_PEERDNS="yes"
IPV6_PEERROUTES="yes"
```

Отредактируйте файл **_‘/etc/resolv.conf’_**,

```
vi /etc/resolv.conf
```

Добавьте имя сервера и IP-адрес:

```
nameserver      192.168.1.101
nameserver      192.168.1.102
```

Сохраните и закройте файл.

Перезапустите сетевую службу:

```
systemctl restart network
```

### 4. Настройка файрвола

Нам нужно при помощи файрвола разрешить DNS-серверу использовать порт 53.

```
firewall-cmd --permanent --add-port=53/tcp
```

### 5. Перезапуск файрвола

Перезапустить файрвол можно при помощи следующей команды:

```
firewall-cmd --reload
```

### 6. Настройка разрешений, прав доступа и SELinux

Пропишите следующие команды друг за другом:

```
chgrp named -R /var/named
chown -v root:named /etc/named.conf
restorecon -rv /var/named
restorecon /etc/named.conf
```

### 7. Тестирование вторичного DNS-сервера

Введите:

```
dig masterdns.unixmen.local
```

Образец вывода:

```
; <<>> DiG 9.9.4-RedHat-9.9.4-14.el7 <<>> masterdns.unixmen.local
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18204
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;masterdns.unixmen.local.    IN    A

;; ANSWER SECTION:
masterdns.unixmen.local. 86400    IN    A    192.168.1.101

;; AUTHORITY SECTION:
unixmen.local.     86400    IN    NS    masterdns.unixmen.local.
unixmen.local.     86400    IN    NS    secondarydns.unixmen.local.

;; ADDITIONAL SECTION:
secondarydns.unixmen.local. 86400 IN    A    192.168.1.102

;; Query time: 0 msec
;; SERVER: 192.168.1.102#53(192.168.1.102)
;; WHEN: Wed Aug 20 17:04:30 IST 2014
;; MSG SIZE  rcvd: 125
```

Введите:

```
dig secondarydns.unixmen.local
```

Образец вывода:

```
; <<>> DiG 9.9.4-RedHat-9.9.4-14.el7 <<>> secondarydns.unixmen.local
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60819
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;secondarydns.unixmen.local.    IN    A

;; ANSWER SECTION:
secondarydns.unixmen.local. 86400 IN    A    192.168.1.102

;; AUTHORITY SECTION:
unixmen.local.     86400    IN    NS    masterdns.unixmen.local.
unixmen.local.     86400    IN    NS    secondarydns.unixmen.local.

;; ADDITIONAL SECTION:
masterdns.unixmen.local. 86400    IN    A    192.168.1.101

;; Query time: 0 msec
;; SERVER: 192.168.1.102#53(192.168.1.102)
;; WHEN: Wed Aug 20 17:05:50 IST 2014
;; MSG SIZE  rcvd: 125
```

Введите:

```
nslookup unixmen.local
```

Образец вывода:

```
Server:     192.168.1.102
Address:    192.168.1.102#53

Name:    unixmen.local
Address: 192.168.1.101
Name:    unixmen.local
Address: 192.168.1.103
Name:    unixmen.local
Address: 192.168.1.102
```

## Настройка клиентской составляющей

Добавьте сведения о DNS-сервере в файл **_‘/etc/resolv.conf’_** во всех клиентских системах.

```
vi /etc/resolv.conf
# Generated by NetworkManager
search unixmen.local
nameserver 192.168.1.101
nameserver 192.168.1.102
```

Перезапустите сетевую службу или перезагрузите систему.

## Тестирование DNS-сервера

И теперь, наконец-таки, вы можете протестировать ваш DNS-сервер, используя любую из следующих команд:

```
dig masterdns.unixmen.local
```
```
dig secondarydns.unixmen.local
```
```
dig client.unixmen.local
```
```
nslookup unixmen.local
```
На этом всё. Основной и дополнительный DNS-серверы готовы к использованию.

Спасибо за внимание! Надеемся, что данная статья помогла вам.

Удачи!

Оригинальная статья: [Setting Up DNS Server On CentOS 7](https://www.unixmen.com/setting-dns-server-centos-7/)