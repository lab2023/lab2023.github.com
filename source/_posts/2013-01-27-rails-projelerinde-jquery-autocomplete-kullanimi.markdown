---
layout: post
title: "Rails Projelerinde JQuery Autocomplete Kullanımı"
date: 2013-01-27 14:43
author: Muhammet DİLEK
comments: true
categories: ruby-on-rails jquery-ui autocomplete
---
Bunun için [jQuery UI](http://jqueryui.com/) kütüphanesine ihtiyacımız var. Kütüphaneyi projemize eklemek için [jquery-ui-rails](https://github.com/joliss/jquery-ui-rails) gemini kulanacağız. Gemfile dosyasına gemi `gem "jquery-ui-rails"` ekledikten sonra `bundle install` komutunu çalıştıralım. 
Javascript kütüphanesini çağırmak için `application.js` dosyasına 

```javascript
//= require jquery.ui.autocomplete
```
Css kütüphanesini çağırmak için `application.css.scss` dosyasına 

```css
/*
*= require jquery.ui.autocomplete
*/
```
satırlarını ekleyelim.
<!-- more -->
Şimdi autocomplete yapılacak olan alanı oluşturalım.

```haml
= f.text_field :name_or_country_name_cont, id: 'search_zones', :class => 'search-query',  :placeholder => "#{t('search')}...", data: {autocomplete_source: auto_admins_zones_path}
```

`data` autocomplete için arama yapacağı yol.

Şimdide bu alana autocomplete özelliği verelim.

```javascript
//application.js
$(document).ready(function() {    
    $('.search-query').autocomplete({
        source: $('.search-query').data('autocomplete-source'),
        messages: {
            noResults: null,
            results: function() {}
        }
    });
});
```
`.search-query` clas' ına sahip alanlara autocomplete özelliği eklemiş olduk. `source` parametresi ise autocomplete için verilerin yolu. `messages` parametresi ise ismindende anlaşılacağı gibi mesaj ayarları için. "1 sonuç bulundu, Hiç sonuç bulunamadı ..." gibi. Burada hiç bir mesaj döndürmüyor.

Şimdi autocomplete için `json` veri döndürecek metodu yazalım.

```ruby
  def auto
    search = Zone.search(:name_or_country_name_start => params[:term])
    zones = search.result(:distinct => true).limit(10)
    render :json => zones.map {|p| Hash[id: p.id, label: p.name, name: p.name]}
  end
```

Arama işlemlerinde [ransack](https://github.com/ernie/ransack) kullanıyorum kafanız karışmasın. Automoplete aranılan veriyi `term` parametresi olarak gönderiyor.

![Autocomplete](https://dl.dropbox.com/u/44821222/autocomplete-1.png)
![Autocomplete](https://dl.dropbox.com/u/44821222/autocomplete-2.png)
![Autocomplete](https://dl.dropbox.com/u/44821222/autocomplete-3.png)

Autocomplete için daha detaylı dökümana [buradan](http://jqueryui.com/autocomplete/#combobox) ulaşabilirsiniz.

İyi çalışmalar dilerim.
