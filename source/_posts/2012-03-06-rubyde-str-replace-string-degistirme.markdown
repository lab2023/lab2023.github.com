---
layout: post
title: "Ruby'de String Replace (String Degistirme)"
date: 2012-03-06 21:28
author: Muhammet DİLEK
comments: true
categories: Ruby
---

PHP de kullandığımız `str_replace()` fonksiyonu yok tabi :). Ruby de string değiştirme için bir kaç fonksiyon bulunmakta. İlk fonksiyonu `tr()` inceleyelim.

String içerisinde karakter değiştirmek istiyorsak;
```ruby
"Merhaba".tr("aeiou", "*") # => "M*rh*b*"
```

Görüldüğü gibi "hello" stringindeki "a", "e", "i", "o", "u" karakterlerini "*" karakterine çeviriyor.

Eğer bir string içerisindeki sembolleri kaldırmak istersek;

```ruby
"Merhaba !@#$%^&*()_+ Ruby!".tr("!@#$%^&*()_+", "") # => "Merhaba Ruby"
```
<!-- more -->
`tr` fonksiyonu ile bir cümle içindeki bir kelimeyi değiştirmek isteyelim;

```ruby
message = "Merahaba Ruby !" #"Merhaba Ruby !"
message.tr("Ruby", "Rails") #"Merahaia Rail !"
```

"Ruby" yi "Rails" e çevirmek istedik ama başarılı olamadık :(.

Örnektende anlaşıldığı gibi `tr()` fonksiyonu karakter karakter işlem yapıyor. Bizi burada kurtaracak olan fonksiyon
ise `gsub()`. `gsub()` fonksiyonu ile biraz önceki örnekte yapmaya çalışıtığımızı artık yapabiliriz :).

```ruby
message = "Merahaba Ruby !" #"Merhaba Ruby !"
message.tr("Ruby", "Rails") #"Merhaba Rails !"
```

Görüldüğü gibi "Ruby" stringini "Rails" stringine çevirmiş bulunuyoruz. Eğer bir tane daha "Ruby" stringi olsaydı onuda değiştirecekti.

```ruby
message = "Merahaba Ruby Ruby !" #"Merhaba Ruby Ruby!"
message.gsub("Ruby", "Rails") #"Merhaba Rails Rails!"`
```

Şimdide şöyle bir örnek bir şey yapalım. 

*"Laboratuvarda **30** Dell, **20** Casper, 5 Mac Bilgisayar bulunmaktadır."*

Cümlesini şöyle yapalım; 

*"Labarotuvarda **biraz** Dell, **biraz** Casper, 5 Mac Bilgisayar bulunmaktadır."*

```ruby
"Laboratuvarda 30 Dell, 20 Casper, 5 Mac Bilgisayar bulunmaktadır.".gsub(/[0-9][0-9]/, 'biraz') #Labarotuvarda biraz Dell, biraz Casper, 5 Mac Bilgisayar bulunmaktadır.
```

Umarım faydalı olmuştur. İyi çalışmalar...