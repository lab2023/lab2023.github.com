---
layout: post
title: "Sunucunun ssh portunu değiştirme"
date: 2012-10-22 15:14
author: Muhammet DİLEK
comments: true
categories: ssh port capistrano
---
`ssh` portu default olarak 22 dir. Güvenlik için istersek bunu değiştirebiliyoruz.

`nano` ile `/etc/ssh/` dizinindeki `sshd_config` dosyasını açıp 5. satırdaki `Port 22` yazan yerdeki `22` portunu değiştirip kaydediyoruz.

```bash
$ sudo nano /etc/ssh/sshd_config
```

Değişiklikler algılaması için aşağıdaki komut ile `ssh`' ı yeniden başlatıyoruz.

```bash
$ sudo /etc/init.d/ssh restart
```

Localden bağlanırken portu belirtiyoruz.

```bash
$ ssh root@x.x.x.x -p 9387
```

-p parametresi ile portu belirtmiş olduk.

Eğer deploy işlemleri için `Capistrano` kullanıyorsak `deploy.rb` dosyasında aşağıdaki gibi portu belirtiyoruz.

```ruby
set :port, 30000
```

İyi çalışmalar
