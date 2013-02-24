---
layout: post
title: "Ruby On Rails REST API'de HTTP Basic Authentication Kullanarak Kullanıcı Doğrulama"
date: 2012-10-06 13:09
author: Muhammet DİLEK
comments: true
categories: rails api ruby-on-rails
---
Temel http kullanıcı doğrulama yöntemi. `Rails`' te kullanımı inanılmaz derecede kolay ve çoğu API istemcilerinde hiçbir sorun olmadan çalışabiliyor.

Controller' da `http_basic_authentication_with` metodunu çağırıp parametre olarak `user_name` ve `password` geçiyoruz.

```ruby
http_basic_authenticate_with name: 'admin', password: 'secret'
```

Şimdi uygulamamıza `curl` ile istek yapalım.

<!-- more -->

```bash
$ curl http://pigon.dev/api/products
HTTP Basic: Access denied.
```

```bash
$ curl -I http://pigon.dev/api/products
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm="Application"
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
X-Request-Id: c371c847fb00f9eda805d2ea2c583178
X-Runtime: 0.001656
Connection: close
```

```bash
$ curl http://pigon.dev/api/products -u 'admin:secret'
[{"amount":"4.3","created_at":"2012-10-06T09:58:47Z","id":2,"title":"Product2","updated_at":"2012-10-06T09:58:47Z"},{"amount":"15.0","created_at":"2012-10-06T09:59:14Z","id":3,"title":"Product3Update","updated_at":"2012-10-06T09:59:24Z"}]
```

İyi çalışmalar dilerim...

Kaynak: [RailsCasts](http://railscasts.com/episodes/352-securing-an-api)

