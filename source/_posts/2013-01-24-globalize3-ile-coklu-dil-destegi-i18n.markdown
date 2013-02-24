---
layout: post
title: "globalize3 ile Çoklu Dil Desteği (I18n)"
date: 2013-01-24 14:29
author: Muhammet DİLEK
comments: true
categories: ruby-on-rails globalize3 i18n
---
Model katmanına I18n desteği eklemek için [globalize3](https://github.com/svenfuchs/globalize3) gemini kullanıyoruz.

Gemfile' a `gem 'globalize3'` ekleyip `bundle install` ile gemi kuralım.

Modelde I18n desteği eklenilecek fieldları belirtiyoruz.

```ruby
class Country < ActiveRecord::Base
  attr_accessible :name
  translates :name
end
```
<!-- more -->
Daha sonra migrationda translates tablosunu oluşturacak kodu ekliyoruz.

```ruby
class CreateCountries < ActiveRecord::Migration
  def change
    create_table :countries do |t|

      t.timestamps
    end
    Country.create_translation_table! :name => :string
    Country.create(name: "Turkey")
  end
end
```

`rake db:migrate` komutunu çalıştıralım.

```ruby
>> I18n.locale
:en
>> Country.first.name
"TURKEY" 
>> I18n.locale = :tr
:tr
>> Country.first.name
nil
>> c = Country.first
#<Country id: 1, name: nil, created_at: "2013-01-24 08:20:59", updated_at: "2013-01-24 11:09:31">
>> c.name = "Türkiye"
"Türkiye"
>> c.save
true
>> I18n.locale
:tr
>> Country.first.name
"Türkiye"
```

Örnekte locale `:tr` iken bir veri kaydedilmediğinden `nil` sonucu döndü. Eğer `nil` yerine default locale değerinin dönmesini istersek `application.rb` dosyasına aşağıdaki satırı ekleyelim.

```ruby
config.i18n.fallbacks = true
```

İyi çalışmalar dilerim.
