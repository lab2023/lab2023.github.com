---
layout: post
title: "PostgreSQL Yedek Alma ve Geri Yükleme"
date: 2013-01-21 18:38
author: Muhammet DİLEK
comments: true
categories: postgresql
---
#### `pg_dump`

`pg_dump veritabaniadi -f dosyaadi.dump`

* 1 vt yedeği alır.
* Öntanımlı olarak düz metin yedek alır.
* Yedeği sıkıştırabilir.
`pg_dump -Fc veritabaniadi -f dosyaadi.dump`

* Global veriyi almaz. (User, tablespace)
* Sıkıştırılmış yedeği tablo bazında açma imkanı. Sadece sıkıştırılmış formattayken.
* Sadece istenilen tabloların yedeğini alma
`-t "satislar."` satislar tablosunun yedeğini al. `-T satislar` satislar dışındaki tabloların yedeğini al.
* Sadece schema yı yedek alma
`-s`
* Sadece dataları almak.
`-a`

#### `pg_dumpall`

* Tüm veritabanlarının yedeğini tek dosyaya alır.
* Sadece düz metin olarak yedek alır.(.sql)
* Bir tablo veya database açılamaz. Yedekler olduğu gibi geri yüklenir.
* Global bilgileri ayrı olarak yedekleyebilirsiniz.
`pg_dumpall -g -f dosyaadi.dump`
* Sadece schema yı yedek alma
`-s`
* Sadece dataları almak.
`-a`

`pg_dump` & `pg_dumpall`

* `INSERT` li yedek almaz. Bunun yerine `COPY` kullanılır. Sebebi her `INSERT` işleminin `TRANSACTION` içerisinde olmasıdır.

Kesinlikle `pg_dump` kullanın, yedeği sıkıştırın, global bilgileri `pg_dumpall` ile alın. Geri yüklerken önce global veriler yüklenir.

### `pg_restore`

* `pg_restore -d veritabaniadi yedek.dump -j 4`
* Sadece sıkıştırlmış verileri geri yükler.
* `-j` parelel oturum.

[ab2013 postgresql](http://ab.org.tr/ab13/postgresql.html) eğitim notlarından.

İyi çalışmalar.
