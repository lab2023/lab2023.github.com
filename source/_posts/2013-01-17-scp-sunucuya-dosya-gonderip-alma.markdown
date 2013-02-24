---
layout: post
title: "scp - Sunucuya Dosya Gönderip Alma"
date: 2012-11-01 13:06
author: Muhammet DİLEK
comments: true
categories: scp ssh
---
Merhabalar,

`scp` komutunu kullanarak;

* Uzak bilgisayar (Sunucu) ile yerel bilgisayar (Locale) arasında 
* Uzak bilgisayar ile uzak bilgisayar arasında 

dosya kopyalama işlemlerini yapabilyoruz.
<!-- more -->

#### Yerel bilgisayardan den uzak bilgisayara dosya kopyalamak için;

```bash
scp ~/Dropbox/pigonws/_20120731_2255.sql deployer@10.1.2.3:~/apps/backups
```

Burada;

* `~/Dropbox/pigonws/_20120731_2255.sql` : Yerel bilgisayardaki dosyanın yolu.

* `deployer@10.1.2.3:~/apps/backups` : kullanıcı @ sunucu ip si : sunucudaki yol.

Komutu çalıştırdıktan sonra ssh ile uzak bilgisayara bağlanıp  yerel bilgisayardaki `_20120731_2255.sql` dosyasını uzak bilgisayardaki `~/apps/backups` dizininin altına kopyalıyoruz. 

#### Uzak bilgisayardan yerel bilgisayara dosya almak için;

```bash
scp deployer@10.1.2.3:~/apps/backups/pigon_production.sql ~/Dropbox/pigonws/
```

#### Uzak bilgisayardan uzak bilgisayara dosya kopyalamak için;

Açıkçası bunu hiç denemedim. Şuradaki linkten http://askubuntu.com/questions/153960/scp-with-two-different-ports bakabilrsiniz.

###Parametreler

* `-P` : SSH portunu belirtme.
* `-q` : Progress barı gösterme.
* `-r` : Klasör kopyalayacaksak.
* `-v` : Debug mesajlarını göster.

İyi çalışmalar...
