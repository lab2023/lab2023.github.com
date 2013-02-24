---
layout: post
title: "Ruby On Rails' te Ransack Gem' i ile Arama"
date: 2012-12-16 17:19
author: Muhammet DİLEK
comments: true
categories: ruby-on-rails ransack search
---
[`Ransack`](https://github.com/ernie/ransack/) ile kolay bir şekilde arama ve sıralama işlemlerini yapabiliyoruz. `Gemfile` dosyasına

`gem 'ransack'` satırını ekledikten sonra `bundle install` ile kuralım.

Arama için `search` metodunu kullanıyoruz.

```ruby
>> Product.search(:name_cont => "aaaa").result.to_sql
"SELECT `products`.* FROM `products`  WHERE (`products`.`name` LIKE '%aaaa%')"
```

* Aynı anda iki alanda arama yapmak istersek `or` kullanıyoruz.

```ruby
>> Product.search(:name_or_barcode_cont => "aaaa").result.to_sql
"SELECT `products`.* FROM `products`  WHERE ((`products`.`name` LIKE '%aaaa%' OR `products`.`barcode` LIKE '%aaaa%'))"
```
<!-- more -->

### İlişkişli Kayıtlarda Arama Yapmak

```ruby
# product.rb
class Product < ActiveRecord::Base  

  belongs_to :brand

  has_and_belongs_to_many :keywords

  has_many :custom_products

end
```

```ruby
>> Product.search(:name_or_barcode_or_keywords_name_cont => "aaaa").result.to_sql
"SELECT `products`.* FROM `products` LEFT OUTER JOIN `keywords_products` ON `keywords_products`.`product_id` = `products`.`id` LEFT OUTER JOIN `keywords` ON `keywords`.`id` = `keywords_products`.`keyword_id` WHERE (((`products`.`name` LIKE '%aaaa%' OR `products`.`barcode` LIKE '%aaaa%') OR `keywords`.`name` LIKE '%aaaa%'))"
```

```ruby
>> Product.search(:name_or_barcode_or_brand_name_cont => "aaaa").result.to_sql
"SELECT `products`.* FROM `products` LEFT OUTER JOIN `brands` ON `brands`.`id` = `products`.`brand_id` WHERE (((`products`.`name` LIKE '%aaaa%' OR `products`.`barcode` LIKE '%aaaa%') OR `brands`.`name` LIKE '%aaaa%'))"
```

Tamamda bu sondaki `cont` ne işe yarıyor ? diye sorular duyar gibi oluyorum. `cont` `Ransack` eylemlerinden birisi. Yani nasıl arama yapacağını belirtiyoruz. İçerisinde aaaa geçsin, aaaa ile başlayanler vs. vs. `cont` içerisinde aaaa geçen kayıtları getiriyor. Diğer `Ransack` eylemlerini şu linkten incleyebilrsiniz. https://github.com/ernie/ransack/wiki/Basic-Searching

### Controller
```ruby
def index
  @search = Product.search(params[:q])
  @products = @search.result
end
```
* Arama için varsayılan parametre `:q`. İstersek değiştirebiliyoruz. https://github.com/ernie/ransack/wiki/Configuration
* Eğer aram sonuçlarında tekrar eden kayıt istemiyorsak(`DISTINCT`) `:distrinct => true` parametresini geçiyoruz.

``` ruby
def index
  @search = Product.search(params[:q])
  @products = @search.result(:distinct => true)
end
```

### View

```haml
= search_form_for @search do |f|
  = f.text_field :name_cont
  = f.submit "Search", :class => 'btn'
```

### Sıralama
`sort_link` helper metodunu kullanıyoruz.

```ruby
#product.html.haml
%table.table.table-striped.table-bordered.table-condensed
  %tr
    %th= sort_link(@search, :name, "İsim")
    %th= sort_link(@search, :barcode, "Barkod")
    %th= sort_link(@search, :sub_category_name, "Kategori")

  - @products.each do |product|
    %tr
      %td= product.name
      %td= product.barcode
      %td= product.sub_category.try(:name)
```

![product](https://dl.dropbox.com/u/44821222/Screen%20Shot%202012-12-16%20at%206.01.31%20PM.png)

![product](https://dl.dropbox.com/u/44821222/Screen%20Shot%202012-12-16%20at%206.01.49%20PM.png)

İyi çalışmalar dilerim.
