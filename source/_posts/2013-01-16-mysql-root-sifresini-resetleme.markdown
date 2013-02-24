---
layout: post
title: "MySQL root şifresini resetleme"
date: 2012-10-23 15:22
author: Muhammet DİLEK
comments: true
categories: mysql
---
`root` şifreside unutulurmu demeyin valla unutuluyor :)

* `MySQL` server durdulur

```bash
$ service mysql stop
# veya
$ sudo /etc/init.d/mysql stop
```
* `--skip-grant-tables` parametresi ile güvenli modda tekrar başlatalım. Güvenli modda başlatınca yetkilerin hiçbirisi dikkate alınmayacak.

```bash
$ sudo mysqld_safe --skip-grant-tables
```
* `MySQL` konsoluna giriş yapalım

```bash
$ mysql -u root
```

* `root` şifresini değiştirelim

```bash
mysql> use mysql;
mysql> update user set password=PASSWORD("NEWPASSWORD") where User='root';
mysql> flush privileges; 
mysql> quit
```
* `MySQL` sunucusunu başlatalım

```bash
$ service mysql start
```

İyi çalışmalar...

