---
layout: post
title: "Ruby On Rails'te Client Side Validasyon"
date: 2012-10-12 15:00
author: Muhammet DİLEK
comments: true
categories: rails validation client side validations ruby-on-rails
---
Client Side validasyonlar için [ClientSideValidations](https://github.com/bcardarella/client_side_validations) gemini kullanıyoruz. `Simple Form` ile çalışabilmesi içinde [ClientSideValidationsSimpleForm](https://github.com/dockyard/client_side_validations-simple_form) gemini kullanıyoruz.

### Kurulum
Gemleri `Gemfile`' a ekleyip `bundle install` yapalım

```ruby
gem 'client_side_validations'
gem 'client_side_validations-simple_form'
```

Daha sonra kurulum komutunu çalştıralım.

```ruby
rails g client_side_validations:install
```

`config/initializers/client_side_validations.rb` başlangıç dosyasının eklendiğini görüyoruz.

Eğer istersek gem' lerin kullandığı javascript dosyaların assets dizinin altına kopyalayabiliriz.

```ruby
rails g client_side_validations:copy_assets
```
<!-- more -->
### Kullanım
`app/assets/javascripts/application.js` dosyasında gerekli dosyaları çağıralım.

```javascript
//= require rails.validations
//= require rails.validations.simple_form
```

Forma `:validate => true` parametresini geçiyoruz.

```haml
= simple_form_for(@product, :validate => true) do |f|
  = f.error_notification

  .form-inputs
    = f.input :title
    = f.input :price
    = f.input :status

  .form-actions
    = f.button :submit
```

Şimdide modele gidip validasyonları yazalım.

```ruby
class Product < ActiveRecord::Base
  attr_accessible :price, :status, :title
  validates_presence_of :price, :title

  validates_length_of :title, :minimum => 5, :maximum => 10

  validates_numericality_of :price, :greater_than => 10, :less_than => 100

  validates_uniqueness_of :title
end
```

İstersek validasyonları form içinde tek bir input' a da ekleyebiliyoruz.

```haml
= f.input :title, :validate => { :presence => true }
```
![validates_presence_of](https://dl.dropbox.com/u/44821222/validation.png)
![validates_numericality_of](https://dl.dropbox.com/u/44821222/validation-two.png)
![validates_uniqueness_of](https://dl.dropbox.com/u/44821222/uniqueness1.png)

Son ekran görüntüsünde dikkat ettiyseniz ajax ile uniq olup olmadığını bile kontrol edebiliyor. 

En kısa sürede [lab2023](http://www.lab2023.com) bünyesinde `open-source` olarak geliştirdiğimiz  [Rails Template](https://github.com/lab2023/rails-template)' e ekleyeceğiz. [Issue](https://github.com/lab2023/rails-template/issues/30)' yu girdim bile :)

İyi çalışmalar dilerim.
