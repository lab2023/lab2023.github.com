---
layout: post
title: "MySQL'de yeni veritabanı ve veritabanı kullancısı oluşturma"
date: 2012-10-23 13:04
author: Muhammet DİLEK
comments: true
categories: mysql
---
* `MySQL`' e login olalım

```bash
$ mysql -u root -p
```

* Veritabanı oluşturalım

```mysql
mysql> CREATE DATABASE pigon_production;
```

`pigon_production` isminde bir veritabanı oluşturduk.

* Şimdide bir kullanıcı oluşturalım ve `pigon_production`' ı kullanabilmesi için yetki verelim.

```mysql
mysql> GRANT ALL ON pigon_production.* TO 'pigon'@localhost IDENTIFIED BY 'pigon_password';
mysql> FLUSH PRIVILEGES;
```

Artık `pigon` kullanıcısı oluşturuldu ve `pigon_production` veritabanını kullanma yetkisi verildi. Buradaki yetki `GRANT ALL` komutundan da anlaşılacağı gibi veritabanı üzerinde tüm işlemleri yapabilme yetkisidir.

Kolaylıklar...
