---
layout: post
title: "Ruby on Rails'te Excel Export"
date: 2012-12-07 17:21
author: Muhammet DİLEK
comments: true
categories: rails ruby-on-rails excel-export to_xls
---
Excel export işlemlerinde [`to_xls`](https://github.com/splendeo/to_xls) gemini kullanıyoruz. Export edilen excel dosyaları `xls` uzantısında oluyor.

Excel renderlermek için ` config/initializers/mime_types.rb` doyasına mime type eklemeyi unutmuyalım.


```ruby
Mime::Type.register "application/vnd.ms-excel", :xls
```

Gemfile dosyasına `gem 'to_xls'` ekleyip `bundle install` ile kuruyoruz.

<!-- more -->

### Kullanımı

```ruby
class UserController < ApplicationController

  def index
    @users = User.all

    respond_to do |format|
      format.html
      format.xml { render :xml => @users }
      format.xls { send_data @users.to_xls, :filename => 'users.xls' }
    end
  end
end
```

`@users` koleksiyonu içindeki ilk itemin propertilerine göre `@users` koleksiyonunu export eder. Properti ismileri kolon başlığı olarak kabul edilir.

Eğer kolonlara başlık vermek istemiyorsak `:headers => false` parametresini geçiyoruz.

```ruby
@users.to_xls(:headers => false)
```
Sheet ismini belirtmek istiyorsak `:name => "Users"` parametresini geçiyoruz. Varsayılanı Sheet1

```ruby
@users.to_xls(:name => "Users")
```

Propertilerin hepsini değilde seçtiklerimizi export etmek istersek `:columns` parametresi ile bunu belirtebilyoruz.

```ruby
@users.to_xls(:columns => [:name, :role])
```

Bu kolonların isimlerini değiştirmek istersek `:headers` parametresini kullanıyoruz.

```ruby
@users.to_xls(:columns => [:name, :role], :headers => ['İsim', 'Rol'])
```

Peki ilişkili kayıtlarıda export edebiliyor muyuz ? Evet ilişkili kayıtlarıda export edebiliyoruz.

```ruby
@users.to_xls(:columns => [:name, {:company => [:name, :address]}])
```
`company` modelindeki `:name` ve `:address` propertilerini aldık.

Böyle export edince kolon isimlerini company_name , company_address şeklinde oluşturacaktır. Kolon isimlerini değiştirmek için yine `:headers` parametresini kullanabiliriz.

```ruby
@users.to_xls(:columns => [:name, {:company => [:name, :address]}], :headers => ['Name', 'Company', 'Address']) 
```

İyi çalışmalar dilerim...
