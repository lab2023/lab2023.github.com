---
layout: post
title: "Rails Projelerinde Heroku Scheduler Kullanımı"
date: 2012-03-31 14:51
author: Muhammet DİLEK
comments: true
categories: Heroku Ruby-On-Rails
---

Rails ile geliştiridiğim projelerimi genelde [heroku](http://heroku.com) üzerinde tutuyorum. Bu yazımda da bir projede kullandığım `Scheduler` 'i anlatacağım.

{% img left /images/scheduler.png Heroku Scheduler %} `Scheduler (Zamanlayıcı)` Heroku üzerindeki projelerimizde `cron` yerine kullanabilecegimiz, `cron` 'dan daha gelişmiş add-on. Mesela sisteminizdeki kullanıcıları günlük olarak gelişmelereden haberdar etmek istiyoruz. Hergün uygulamaya girip kendimiz mail gönderme fonksiyonunu çalıştırmayacağız herhalde :). İşte `Scheduler` burada devreye girip bizim yerimize istediğimiz zamanlarda mail gönderme fonksiyonunu çalıştırıyor.
<!-- more -->
Scheduler günlük, saatlik veya 10 dakikalık zaman dilimlerinde görev tanımlama imkanı sunuyor. Şimdilik bunların hepsi ücretsiz :)

Uygulamamıza `Scheduler` eklemek için

 1. 
[https://addons.heroku.com/scheduler](https://addons.heroku.com/scheduler) sayfasındaki add butonuna tıklayarak yada,
 2. Terminalden aşağıdaki komutu yazarak 
{% codeblock %}
heroku addons:add scheduler:standard
{% endcodeblock %}
 
ekleme yapıyoruz.
Daha sonra `lib/rake` klasörü altına `scheduler.rake` isminde bir dosya oluşturup içine şu kodları ekliyoruz.
{% codeblock lang:ruby scheduler.rake%}
desc "This task is called by the Heroku scheduler add-on"
task :update_feed => :environment do
    puts "Updating feed..."
    NewsFeed.update
    puts "done."
end

task :send_reminders => :environment do
    User.send_reminders
end
{% endcodeblock %}

Yukarıdaki kodu açıklayacak olursak; Burada `:update_feed` görev ismi. `puts "Updating feed..."` ile log' a *Updating feed...* yazıyor. Sonra `NewsFeed` classının `update` metodunu çalıştırıyor. Son satırda ise log' a *done.* yazıyor. Diğer görevde aynı mantıkla çalışıyor. Buradaki NewsFeed ve User class' ları NewsFeed ve User modelimizin class' ları.

{% img left /images/37-original.jpg Heroku Scheduler %}Evet şimdi görevleri yazdık, Heroku' da tanımlama zamanı. Uygulamamızın General Info sayfasına gidiyoruz ve sağ üstteki drop down şeklindeki add-ons menüsüne tıklayarak uygulamamıza eklemiş olduğumuz add-on ları görüyoruz. Buradan Scheduler' i seçip scheduler dashboard' a ulaşıyoruz. Add job diyerek yeni bir görev tanımlıyoruz. Görev isminin başına rake komutunu eklemeyi unutmayalım. Çünkü rails' de görevleri rake komutu ile çalıştırıyoruz.

Burada tanımladığımız görevlerin zaman dilimi yukarıda da bahsettiğim gibi saatlik, günlük veya 10 dakikalık. Peki kendimiz bir zaman dilimi belirleyemez miyiz?. Ben bir görevi haftalık çalıştırmak istedğimde şöyle bir yola başvurdum;

Heroku üzerinde görev zaman dilimini günlük olarak ayarlıyıp, görev koduna da günü kontrol eden if bloğu ekleyerek haftanın istedğim gününde görevi çalıştırıyorum.

{% codeblock lang:ruby scheduler.rake%}
desc "This task is called by the Heroku scheduler add-on"
task :send_mail
	if Time.now.saturday?
		User.send_mail
	end
end
{% endcodeblock %}

Burada `Time.now.saturday?` kodu true false döndürüyor. Görev de sadece cumartesi günleri çalışıyor. `Time.now.saturday?` yerine `Date.today.wday == 6` de kullanabiliriz.

Komut satırına `heroku logs --ps run` yazarak görev log' ları görebiliriz.

Kolaylıklar...
