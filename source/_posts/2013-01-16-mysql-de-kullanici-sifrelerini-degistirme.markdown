---
layout: post
title: "Mysql'de kullanıcı şifrelerini değiştirme"
date: 2012-10-22 15:10
author: Muhammet DİLEK
comments: true
categories: mysql
---
MySQL şifre değiştirme işlemleri için kullandığım iki yöntem var. Onlardan bahsedeceğim.

### `mysqladmin` komutunu kullanarak

```bash
$ mysqladmin -u root -pcurrentpassword password 'newpassword'
```
> -p**currentpassword** teki `p` ile `currentpassword` bitişik. Bu alana şimdiki şifremizi `newpassword` alanınan da yeni şifremizi yazıyoruz.

<!-- more -->

### `UPDATE` sql komutunu kullanarak

1) __Mysql server' a login olalım;__


```bash
$ mysql -u root -p
```

2) __mysql database' ini seçelim;__

```bash
mysql> use mysql;
```

3) __Aşağıdaki sql komutuyla şifreyi değiştiriyoruz;__

```bash
mysql> update user set password=PASSWORD("NEWPASSWORD") where User='root';
```

4) __Yetkileri yenileyelim;__

```bash
mysql> flush privileges;
mysql> quit
```

> __NOT: `MySQL` kullancı bilgileri `mysql` database' inde tutulmaktadır. Step2 de `mysql` database' ini seçerek işlemlere devam ettik.__

İyi çalışmalar...
