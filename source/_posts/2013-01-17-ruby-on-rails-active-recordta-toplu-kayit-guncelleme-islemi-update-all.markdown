---
layout: post
title: "Ruby On Rails Active Record'ta toplu kayıt güncelleme işlemi - update_all"
date: 2012-10-12 14:24
author: Muhammet DİLEK
comments: true
categories: rails active-rocord ruby-on-rails
---
`update_all` metodu ile toplu kayıt güncelleyebiliriz.

```ruby
update_all(updates, conditions = nil, options = {}) # public
```
Aldığı parametreler;

* `updates` : Güncellenecek alan(lar)  `string`, `array` veya `hash` alabilir. SQL deki `SET` alanı.
* `conditions` : Koşul(lar) `string`, `array` veya `hash` alabilir. SQL deki `WHERE` alanı.
* `options` : Ek seçenekler için. `LIMIT`, `ORDER` gibi gibi.

<!-- more -->

Aşağıda örnekleri inceledeğimizde daha iyi anlaşılacaktır.

* Müşteri tablosundaki tüm kayıtların `wants_email` alanınını `true` yapalım.

```ruby
Customer.update_all :wants_email => true
```

* Başlığında(`title`) `Rails` geçen kitapların yazarını(`author`) `David` yapalım

```ruby
Book.update_all "author = 'David'", "title LIKE '%Rails%'"
```

* Son 1 haftadır `migrate` edilen Avatarların `migrated_at` alanını güncelleyelim.

```ruby
Avatar.update_all ['migrated_at = ?', Time.now.utc], ['migrated_at > ?', 1.week.ago]
```

* Başlığında(`title`) `Rails` geçen ve `created_at` alanına göre sıraladığımızda gelen 5 kitabın yazarını(`author`) `David` yapalım.

```ruby
Book.update_all "author = 'David'", "title LIKE '%Rails%'", :order => 'created_at', :limit => 5
```

Yukarıdaki örneklerde `conditions` (koşulları) `update_all` metoduna parametre olarak geçtik. Bunları `update_all` metoduna parametre geçmek yerine dışarıdada kullanabiliriz.

```ruby
Book.where('title LIKE ?', '%Rails%').update_all(:author => 'David')

Book.where('title LIKE ?', '%Rails%').order(:created_at).limit(5).update_all(:author => 'David')
```

İyi çalışmalar dilerim.
