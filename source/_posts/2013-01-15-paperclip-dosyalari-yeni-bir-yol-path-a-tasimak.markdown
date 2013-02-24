---
layout: post
title: "Paperclip: Dosyaları  Yeni Bir Yol (Path)' a Taşımak"
date: 2012-12-19 17:08
author: Muhammet DİLEK
comments: true
categories: ruby-on-rails paperclip
---
Dosya upload işlemleri için genellikle [Paperclip](https://github.com/thoughtbot/paperclip) kulanıyoruz.  Paperclipte `:path` parametresi ile dosyanın hangi path altına kaydedeliceğini belirtebiliyoruz. `Product` modelindeki `:picture` için bir path belirledik. Dosyalar `public/system/products/pictures/` dizinin altına `:style(original, icon, small, large)` klasörününde içine `"#{id}-#{name.parameterize}.format"` olarak kaydedilyor. Linklerde `http://localhost:3000/system/products/pictures/original/1-bilgisayar.jpeg` şeklinde oluyor. Peki biz bu path' i ürün production' a çıktıktan sonra değiştirirsek ne olur ?. Ne olacak tüm dosya linkleri patlayacak :) Yani eski dosyaları yeni path ile tekrar kaydettirmemiz gerekecek.

<!-- more -->

###Önce

```ruby
class Product < ActiveRecord::Base
  has_attached_file :picture,
                    :default_url => "/assets/missing-image.jpg",
                    :styles => {
                        :icon => "20x30#",
                        :small => "180x270#",
                        :large => "400x600#"
                    },
                    :path => "public/system/:class/:attachment/:style/:sef_file_name.:extension",
                    :url => "/system/:class/:attachment/:style/:sef_file_name.:extension"
  
  Paperclip.interpolates :sef_file_name do |attachment, style|
    attachment.instance.sef_file_name
  end
  
  def sef_file_name
    "#{id}-#{name.parameterize}"
  end

end
```

###Sonra

```ruby
class Product < ActiveRecord::Base
  has_attached_file :picture,
                    :default_url => "/assets/missing-image.jpg",
                    :styles => {
                        :icon => "20x30#",
                        :small => "180x270#",
                        :large => "400x600#"
                    }

end
````
> Dosyalar için bir `:path` belirtmezsek `paperclip` dosyaları default path ile kaydediyor.

Resimler şimdi `public/system/products/pictures/000/:id/:style` şeklinde kaydediliyor.  Şimdi eski doyaları bu path altına yeniden kaydedelim. Bunun için kısa bir rake yazacağız.

```ruby
namespace :picture_path do
  desc "product picture"

  task :product => :environment do
    products = Product.where("picture_file_name IS NOT NULL")
    products.each do |p|
      filename = Rails.root.join('public', 'system', 'products', 'pictures', 'original', "#{p.id}-#{p.name.parameterize}.#{p.picture_file_name.split(".").last}")
      if File.exists? filename
        image = File.new filename
        p.picture = image
        p.save!
        image.close
      end
    end
  end

end
```

`filename`' e eski yolu atıyoruz. Eğer böyle bir yol var ise dosyayı yeniden kaydediyoruz. Kaydedilen dosyada yeni path ile kaydediliyor. Eski dosyalar hala duruyor ama.

Son olarakta Rake' i çalıştıralım

```bash
rake picture_path:product
```

İyi çalışmalar dilerim
